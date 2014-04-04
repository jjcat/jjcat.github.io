---
    layout: post
    title: Photoshop Scripting Tutorial
    tagline: 
    tags: [photoshop, script] 
---

Photoshop cs2?就开始支持编写脚本，看到美术同学使用Action纪录动作，重放实现批处理，但还是觉得有点麻烦，一来Action中的动作无法改变参数，录制Action又需要手动操作，裁剪，缩放难免可能会引入手动输入错误。所以决定研究下脚本来实现批处理，让美术同学彻底告别录制Action。

要实现的功能非常简单，先缩小原图，然后按中心点裁剪，非常适合我初学Photoshop script。在网上搜索资料，参考资料有

- 官方的开发文档[Photoshop CS5 Scripting Guide](http://wwwimages.adobe.com/www.adobe.com/content/dam/Adobe/en/devnet/photoshop/pdfs/photoshop_cs5_scripting_guide.pdf)
  
- 官方脚本手册[JavaScript](http://wwwimages.adobe.com/www.adobe.com/content/dam/Adobe/en/devnet/photoshop/pdfs/photoshop_cs5_javascript_ref.pdf)，[AppleScript](http://wwwimages.adobe.com/www.adobe.com/content/dam/Adobe/en/devnet/photoshop/pdfs/photoshop_cs5_applescript_ref.pdf)，[VBScript](http://wwwimages.adobe.com/www.adobe.com/content/dam/Adobe/en/devnet/photoshop/pdfs/photoshop_cs5_vbscript_ref.pdf)
- 关于编写UI的，我参考了其中的UI代码[ScriptUI Window in Photoshop – Palette vs. Dialog](http://www.davidebarranca.com/2012/10/scriptui-window-in-photoshop-palette-vs-dialog/)

Photoshop Scripting支持三种脚本语言，JavaScript，AppleScript和VBScript，我选择JavaScript。编写Photoshop 脚本可以使用任何编辑器，但Adobe有自己的脚本编写软件ExtensionScript,有点类似Flash中的脚本编辑器，有代码提示，可以直接运行脚本并进行Debug。

编写好的脚本保存在Photoshop安装目录下的Presents/Scripts 目录下，Photoshop重启后能在菜单->文件->脚本 下找到编写的脚本名字，点击就能运行。


如果要加入GUI那么就要使用ScriptUI，文档在`C:\Program Files (x86)\Adobe\Adobe Utilities - CS6\ExtendScript Toolkit CS6\ExtendScript Toolkit ReadMe.pdf`，查看UI一章，还可以参考这篇博客里的代码[ScriptUI Window in Photoshop – Palette vs. Dialog](http://www.davidebarranca.com/2012/10/scriptui-window-in-photoshop-palette-vs-dialog/)，对于简单的GUI，使用dialog就可以了。

