---
    layout: post
    title: Commits Recover on Detached Head Mode
    tagline: 
    tags: [git] 
---

`git checkout <commit>` 命令会把HEAD设为commit,在这种状态下HEAD没有指向branch, 这种HEAD叫做Detached Head。顾名思义如果在这种情况下提交的话，会在一个不存在branch下工作。但当你切换回master分支或者别的分支，那么在Detached Head下提交的所有内容都不见了。

我在使用source tree的时候就碰到过commit在Detached Head下丢失。在命令行模式下会有警告出现，但使用source tree尽然没出现警告，直接导致我半天的工作找不到了。



接下来说下恢复的方法。查看项目下`.git\objects\`目录中的提交记录，使用日期进行排序。然后使用`git show commit`查看那些不存在的commit，那些commit就是在Detached Head下提交的，因为不属于任何的分支，所以使用log查看不到。

接下来使用`merge commit`合并游离的commit 
 