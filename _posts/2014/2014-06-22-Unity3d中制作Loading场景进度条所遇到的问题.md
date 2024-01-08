---
    layout: post
    title: Unity3d中制作Loading场景进度条所遇到的问题
    tagline: 
    tags: [Unity3D, Loading]
---

## 背景

通常游戏的主场景包含的资源较多，这会导致加载场景的时间较长。为了避免这个问题，可以首先加载Loading场景，然后再通过Loading场景来加载主场景。因为Loading场景包含的资源较少，所以加载速度快。在加载主场景的时候一般会在Loading界面中显示一个进度条来告知玩家当前加载的进度。在Unity中可以通过调用`Application.LoadLevelAsync`函数来异步加载游戏场景，通过查询`AsyncOperation.progress`的值来得到场景加载的进度。

## 尝试——遇到问题

第一步当加载完Loading场景后，调用如下的`LoadGame`函数开始加载游戏场景，使用异步加载的方式加载场景1(Loading场景为0，主场景为1)，通过Unity提供的Coroutine机制，我们可以方便的在每一帧结束后调用`SetLoadingPercentage`函数来更新界面中显示的进度条的数值。

{% highlight csharp %}
public void LoadGame() {
    StartCoroutine(StartLoading_1(1));
}

private IEnumerator StartLoading_1(int scene) {
    AsyncOperation op = Application.LoadLevelAsync(scene);
    while(!op.isDone) {            
        SetLoadingPercentage(op.progress * 100);
        yield return new WaitForEndOfFrame();
    }        
}
{% endhighlight  %}

最后进度条的效果显示如下:

![](http://i.imgur.com/kesstj7.gif)

进度条并没有连续的显示加载的进度，而是停顿一下切换一个数字，再停顿一下切换一个数子，最后在没有显示100%就情况下就切换到主场景了。究其原因在于`Application.LoadLevelAsync`并不是真正的后台加载，它在每一帧加载一些游戏资源，并给出一个progress值，所以在加载的时候还是会造成游戏卡顿，`AsyncOperation.progress`的值也不够精确。当主场景加载完毕后Unity就自动切换场景，所以上述代码中的while循环体内的代码是不会被调用的，导致进度条不会显示100%。

## 修补——100%完成

为了让进度条能显示100%，取巧一点的办法是将`AsyncOperation.progress`的值乘上2，这样当加载到50%的时候界面上就显示100%了。缺点是当界面上显示100%的时候，用户还要等待一段时间才会进入游戏。其实Unity提供了手动切换场景的方法，把`AsyncOperation.allowSceneActivation`设为`false`就可以禁止Unity加载完毕后自动切换场景，修改后的`StartLoading_2`代码如下:

{% highlight csharp %}
// this function is not work
private IEnumerator StartLoading_2() {
    AsyncOperation op = Application.LoadLevelAsync(1);
    op.allowSceneActivation = false;
    while(!op.isDone) {
        SetLoadingPercentage(op.progress * 100);
        yield return new WaitForEndOfFrame();
    }
    op.allowSceneActivation = true;   
}
{% endhighlight  %}

我们首先将`AsyncOperation.allowSceneActivation`设为`false`，当加载完成后再设为`true`。代码看上去没有错，但是执行的结果是进度条最后会一直停留在90%上，场景不会切换。通过打印log发现`AsyncOperation.isDone`一直为`false`，`AsyncOperation.progress`的值增加到0.9后就保持不变了，也就是说场景永远不会被加载完毕。

![](http://i.imgur.com/d44E3Yt.gif)


在这个[帖子](http://forum.unity3d.com/threads/using-allowsceneactivation.166106/#post-1146076)中找到了答案，原来把`allowSceneActivation`设置为`false`后，Unity就只会加载场景到90%，剩下的10%要等到`allowSceneActivation`设置为`true`后才加载，这不得不说是一个坑。所以代码改为如下。当`AsyncOperation.progress`到达0.9后，就直接把进度条的数值更新为100%，然后设置`AsyncOperation.allowSceneActivation`为`ture`，让Unity继续加载未完成的场景。

{% highlight csharp %}
private IEnumerator StartLoading_3() {
    AsyncOperation op = Application.LoadLevelAsync(1);
    op.allowSceneActivation = false;
    while(op.progress < 0.9f) {
        SetLoadingPercentage(op.progress * 100);
        yield return new WaitForEndOfFrame();
    }
    SetLoadingPercentage(100);
    yield return new WaitForEndOfFrame();
    op.allowSceneActivation = true;   
}
{% endhighlight  %}
最后的效果如下：

![](http://i.imgur.com/BircPFa.gif)



## 打磨——增加动画

上述的进度条虽然解决了100%显示的问题，但由于进度条的数值更新不是连续的，所以看上去不够自然和美观。为了看上去像是在连续加载，可以每一次更新进度条的时候插入过渡数值。这里我采用的策略是当获得`AsyncOperation.progress`的值后，不立即更新进度条的数值，而是每一帧在原有的数值上加1，这样就会产生数字不停滚动的动画效果了，迅雷中显示下载进度就用了这个方法。


{% highlight csharp %}
private IEnumerator StartLoading_4() {
    int displayProgress = 0;
    int toProgress = 0;
    AsyncOperation op = Application.LoadLevelAsync(1);
    op.allowSceneActivation = false;
    while(op.progress < 0.9f) {
        toProgress = (int)op.progress * 100;
        while(displayProgress < toProgress) {
            ++displayProgress;
            SetLoadingPercentage(displayProgress);
            yield return new WaitForEndOfFrame();
        }
    }

    toProgress = 100;
    while(displayProgress < toProgress){
        ++displayProgress;
        SetLoadingPercentage(displayProgress);
        yield return new WaitForEndOfFrame();
    }
    op.allowSceneActivation = true;
}
{% endhighlight  %}


`displayProgress`用来记录要显示在进度条上的数值，最后进度条的动画如下：

![](http://i.imgur.com/mAjrMuS.gif)

对比第一种的进度条

![](http://i.imgur.com/kesstj7.gif)


## 总结

如果在加载游戏主场景之前还需要解析数据表格，生成对象池，进行网络连接等操作，那么可以给这些操作赋予一个权值，利用这些权值就可以计算加载的进度了。如果你的场景加载速度非常快，那么可以使用一个假的进度条，让玩家看上几秒钟的loading动画，然后再加载场景。总之进度条虽然小，但要做好也是不容易的。

### 参考

1. 阿高.[Unity 显示Loading(加载)进度 对于网上流行的方法进行更正](http://blog.csdn.net/fg5823820/article/details/28913163)
2. Unity3d官方论坛.[using allowSceneActivation](http://forum.unity3d.com/threads/using-allowsceneactivation.166106/#post-1146076)







