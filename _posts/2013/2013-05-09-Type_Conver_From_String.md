---
    layout: post
    title: Type Conver From String
    tagline: 从String转换类型
    tags: [dotNet, C#, Unity3d] 
---
#Unfinished

我想在Unity中加入一个Console，方便游戏调试，类似上古卷轴，按下slashquote键，跳出输入框，输入命令后执行。

我在Asset商店中找到了一款插件，[easyconsole](http://u3d.as/content/sander-homan/easy-console/3cf) 要加25$,我认为可以自己实现一个。

输入命令格式：cmdName parm1 parm2 ....

通过cmdName来执行一个注册函数，函数的参数是parm1 parm2...

很显然参数是字符串，所以要将字符串转换为注册函数的参数类型。 

C#的反射功能可以得到函数的参数类型列表，剩下的就是将string转换到相依的参数。

几个要用到的函数：
 
- Type.GetMethor(string funname):MethodInfo 通过注册函数名字获得函数信息
 
- MethodInfo.GetParameters():ParameterInfo[] 通过函数信息得到函数参数信息列表

- MethodInfo.Invoke(Object,Object[]):Object 传入参数列表，调用函数

通过这几个函数就可以动态调用函数了，但问题在于如何将string转为相应的参数类型。

C#中有两种类型转换方法，参考[MSDN](http://msdn.microsoft.com/en-us/library/yy580hbd.aspx),一开始查看IConvertible，发现只能从类转换到值类型，无法从值类型转换到类，而且是不支持反射的。另一种方法是TypeConverter，可以满足我的需要，可以通过借口 Type.ConverFromString(string):Object 来转换到参数类型。
 
