---
    layout: post
    title: Offset Bezier Curve
    tagline: 
    tags: [Bezier, Unity3d] 
---
#Unfinished

在绘制水流体的过程中我碰到了一个问题，如何利用一条Bezier曲线生成水流体的网格多边形数据。最后我决定先生成一条Bezier曲线，然后对该曲线进行偏移来生成4条Bezier曲线分别代表一个立方体的4条边。对于Bezier的偏移问题，一开始只是简单的想利用平移来实现，但最后出来的效果不竟如人意。在网上搜索后发现这不是一个简单的问题，Bezier的偏移曲线是无法用Bezier曲线来表示的，但有几种近似的解法。

- [GameDev][1]上有关于此问题的一个讨论
- [StackOverFlow][2]有人对此提出了问题
- 论文[Bezier Flattening][3]
- This [guy][4] use flash to implement it,他的文章的最后还列出了一些Bezier的参考资料
- [An offset algorithm for polyline curves][5]

[1]:http://www.gamedev.net/topic/173228-quotparallelquot-bezier-curves/
[2]:http://stackoverflow.com/questions/408457/outline-of-cubic-bezier-curve-stroke
[3]:http://www.cis.usouthal.edu/~hain/general/Publications/Bezier/BezierFlattening.pdf
[4]:http://seant23.wordpress.com/2010/11/12/offset-bezier-curves/
[5]:http://seant23.files.wordpress.com/2010/11/anoffsetalgorithm.pdf

