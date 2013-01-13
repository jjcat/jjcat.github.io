---
    layout: post
    title: Unity3D Tools Lesson 2
    tagline: Inspector Texture Preview
    tags: [Unity3D]
    imgurl: /image/Unity3D_Tools_Lesson/02/
---
## 简介

[上一课][1]我们使用Edtior class制作了一个简单的自定义Inspector，这一课我们将进一步介绍Editor class，制作一个Unity纹理预览的简化版。
<!-- Insert image Compare Untiy's and Ours -->

## Editor class
首先我们来具体的看一下Editor class有哪些成员变量和函数，这些变量和函数都回在制作纹理预览中用到。您只需要大致了解即可，如果现在看不懂也没关系，会在下面实际应用中了解到这些变量和函数的功能。

###成员变量 

成员变量          |  												  |
-----------------|----------------------------------------------------|
target           | 被监视的对象										  |
targets          |一个所有被监视对象的数组（多选情况下） 				  |
serializedObject |一个序列化对象，他表示一个或多个（多选情况下）被监视的对象 |
{: .table-bordered}

target和targets的用法和函义基本相同，都是表示在Project面板或Hierarchy面板中为选中的对象。target返回一个选中的对象，targets返回的是一个对象数组，表示一组选中的对象。例如下图中在Project面板中，只有Tree_01被选中，那么target就会返回Tree_01对象。
![]({{page.imgurl}}target.png)
<small class="muted">仅一个对象被选中</small>

<!------------------说清楚如果对象没有被Inspector的话，target是不会得到结果的---------------->
如果是多选的情况，那么请使用targets，targets返回一个数组，数组里面包含Tree_01,Tree02,Tree_03和Tree_04。
![]({{page.imgurl}}targets.png)
<small class="muted">多个对象被选中</small>

serializedObject返回的是一个序列化对象，将在以后讲解。

成员函数               |    												   |
----------------------|----------------------------------------------------|
DrawDefaultInspector  |显示内置的Inspector
OnInspectorGUI	      |在这个函数中调用GUI函数，最终会在Inspector上显示
Repaint	              |刷新Inspector
OnPreviewGUI	      |在该函数中调用GUI绘制函数，会最终在Preview窗口显示
OnInteractivePreviewGUI|在该函数中绘制
HasPreviewGUI	      |重写该方法，如果实现了OnPreviewGUI
GetPreviewTitle	      |如果想更改Preview的标题，可以重新改函数
RenderStaticPreview	  |
OnPreviewSettings	  |如果想要显示自定义控件，可以复写该方法
GetInfoString	      |在该函数中



<!-- link reference below-->
[1]: #