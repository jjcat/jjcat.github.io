---
    layout: default
    title: Unity3D Tools Lesson 1--Inspector
    cutcount: 100
---
## 简介 ##
Inspector面板可以用来对Component和Asset进行快速编辑。如果您的Unity中没有看到Inspector面板，可以通过快捷键方式*Ctrl+3*打开。这节课我们将对一个类制作一个自定义的Inspector面板。

##默认的Inspector样式##
新建一个C#文件，命名为**MyPlayer.cs**，输入下面的代码。这些代码定义了一个MyPlayer class，它继承自MonoBehaviour，是一个用户自定义的component。	

{%highlight csharp%}
using UnityEngine;
[System.Serializable]
using System.Collections;
public class MyPlayer : MonoBehaviour 
{
	public int armor  = 75;
	public int damage = 75;
	public GameObject gun; 
}
{%endhighlight%}


>  **注意：**是不是只有派生自MonoBehaviour的类才能够进行自定义化Inspector? 不是，例如贴图，模型等不用附加到GameObject上的资源文件，可以在Project面板下显示并选中，在Inspecotor面板中会列出这些资源的属性，并且可以进行修改。如果您也要自定义asset，需要继承自ScriptableObject class。您可以在Project面板中选择一个FBX文件，或者一个Texture文件，查看Inspector的变化。自定义资源和Inspector会在以后的课程中进行讲解。



然后我们在场景中新建一个GameObject，重命名为MyPlayer，将上面的脚本添加到该GameObject上。
如果一个GameObject拥有该component，那么在Inspector面板中会显示相关的一些信息。默认情况下，Unity会自动为我们生成一个component编辑面板，他会把所有的public的变量显示在Inspector中。如下图：

<a class="imgurl" href="http://imgur.com/piiO0"><img width = 600 src="http://i.imgur.com/piiO0.png" title="Hosted by imgur.com" alt="" /></a></br>
*默认的Inspector面板*

##自定义Inspector面板##
Unity默认的Inspector面板可以随意对该变量进行修改。如果MyPlayer类中的这些变量对输入的数值有范围要求，例如只能输入0到100，这在默认的输入框下是无法做到的。但在自定义Inspector中，我们可以使用slider bar控件对输入范围进行限制。如果这个MyPlayer最后是要给关卡设计师使用，他就不可能输入一些无效的数值。下图为自定义后的Inspector，可以看到我们用slider bar代替了原来的输入框，并且设置slider bar的输入数值的范围为0到100，还加入了一个progress bar显示数值的范围。通过自定义Inspector就能制作适合各种类型的component的Inspector。

<a class="imgurl" href="http://imgur.com/h03dr"><img width = 600 src="http://i.imgur.com/h03dr.png" alt="" title="Hosted by imgur.com" /></a></br>
*自定义的Inspector面板*


现在我们开始自定义Inspector面板的内容，自定义后的Inspector如上图所示。
首先创建一个新的C#文件，命名为**MyPlayerEditor.cs**，并将该文件放在**Editor**文件夹下。
<a class="imgurl" href="http://imgur.com/lxcx6"><img src="http://i.imgur.com/lxcx6.png" title="Hosted by imgur.com" alt="" /></a></br>
*创建MyPlayerEditor.cs*
>**注意**：为什么要放在Editor文件夹下？Unity规定所有的Editor classes都必须放在Editor文件夹下，这样Untiy运行Editor文件夹的Editor class，游戏中运行的代码不要放在Editor文件夹下。

下面的代码将会创建一个自定义的Inspector，你将会在Inspector面板中看到最后的效果。
{%highlight  csharp%}
using UnityEngine;
using UnityEditor;

[CustomEditor(typeof(MyPlayer))]
public class MyPlayerInspector : Editor
{
    public int damageProp;
    public int armorProp;
    public GameObject gunProp;

    // Initilization
    void OnEnable()
    {
     	MyPlayer myPlayer = target as MyPlayer;
        damageProp = myPlayer.damage;
        armorProp = myPlayer.armor;
        gunProp = myPlayer.gun;
    }

    public override void OnInspectorGUI()
    {
        // show slider and progress bar
        damageProp =  EditorGUILayout.IntSlider("Damage", damageProp, 0, 100);
        ProgressBar(damageProp/ 100.0f, "Damage");

        // show slider and progress bar
        armorProp = EditorGUILayout.IntSlider("Armor", armorProp, 0, 100);
        ProgressBar(armorProp/ 100.0f, "Armor");
            
        gunProp = EditorGUILayout.ObjectField("Player's Gun", gunProp, typeof(GameObject), true) as GameObject;

    }

    // Custom GUILayout progress bar.
    private void ProgressBar(float value, string label)
    {
        Rect rect = GUILayoutUtility.GetRect(18, 18, "TextField");
        EditorGUI.ProgressBar(rect, value, label);
        EditorGUILayout.Space();
    }

}
{%endhighlight%}

然后打开Unity编辑器，选中GameObject，您就能看到自定义Inspector面板的内容了。下面对这MyPlayerEditor类进行详细讲解。


##Editor class##
Editor class是所有自定义Inspector的基类。我们必需从Editor class开始派生我们自己的Inspector。

{%highlight csharp%}
[CustomEditor(typeof(MyPlayer))]
public class MyPlayerInspector : Editor
{
	// others...
}
{%endhighlight%}

下面这行代码指定了自定义Inspector的关联类的类型，我们这里要对MyPlayer类进行关联。这行代码的作用就是当您选中含有MyPlayer的GameObject时，就会显示MyPlayerInspector。
{%highlight csharp%}
[CustomEditor[typeof(MyPlayer)]
{%endhighlight%}
## 初始化 ##
Editor class继承自ScriptableObject，ScriptableObject有三个关于生命周期的消息响应函数:

-  OnEable
-  OnDisable
-  OnDestory

对于MyPlayerInspector来说，每当要显示自定义Inspector的时候就会调用OnEable，当切换到其他Inspector面板的时候就会调用OnDisable。所以OnEnable是进行初始化最好的地方。

在下述的初始化代码中，我们获取了当前选中的对象的成员变量。

{%highlight csharp%}
void OnEnable()
{
 	MyPlayer myPlayer = target as MyPlayer;
    damageProp = myPlayer.damage;
    armorProp  = myPlayer.armor;
    gunProp    = myPlayer.gun;
}
{%endhighlight%}
	
在上述的代码中，出现了一个新变量target，他代表了当前要进行Inspect的对象，在MyPlayerEditor类中，我们指定Inspect MyPlayer类，所以target就是当前My Player的一个引用。我们使用as操作符先对target进行类型转化。



Edtior类还有两个成员变量是用来表示当前选中的对象，他们分别叫做targets和serializedObject。

##绘制GUI##
如果您在游戏开发中使用过Unity自带的GUI类，那么您会发现EditorGUI类接口同GUI基本是同样的设计思路。所有的UI绘制必须放在OnInspectorGUI方法中，就好比在游戏中所与的GUI函数必须放在OnGUI方法中。
{%highlight csharp%}
public override void OnInspectorGUI()
{
    // show slider and progress bar
    damageProp =  EditorGUILayout.IntSlider("Damage", damageProp, 0, 100);
    ProgressBar(damageProp/ 100.0f, "Damage");

    // show slider and progress bar
    armorProp = EditorGUILayout.IntSlider("Armor", armorProp, 0, 100);
    ProgressBar(armorProp/ 100.0f, "Armor");
        
    gunProp = EditorGUILayout.ObjectField("Player's Gun", gunProp, typeof(GameObject), true) as GameObject;

}

// Custom GUILayout progress bar.
private void ProgressBar(float value, string label)
{
    Rect rect = GUILayoutUtility.GetRect(18, 18, "TextField");
    EditorGUI.ProgressBar(rect, value, label);
    EditorGUILayout.Space();
}
{%endhighlight%}
EditorGUI类有一个方便布局的版本，叫做EditorGUILayout。使用EdiorGUILayout可以不用指定每个控件的坐标位置从而省去了每次进行计算坐标的苦差事。EditorGUILayout会更具调用顺序进行自动布局。在上述代码中我就使用了EditorGUILayout创建progress bar和slider bar。

##总结##
我们在这一课中详细介绍了创建一个自定义Inspector的详细步骤。我们制作了一个简单的Inspector面板，下一课我们会具体介绍更多的Editor class的功能，重新制作一个纹理查看器。 


	
