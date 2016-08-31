---
title: 玩转Unity资源，对象和序列化(下)
date: 2016/08/31 13:31:27
tags: 
    - Unity3D
categories: Unity3D项目日志
---



本文将从Unity编辑器和运行时两个角度出发，主要探讨以下两方面内容：**Unity序列化系统内部细节**以及**Unity如何维护不同对象之间的强引用**。另外还会讨论对象与资源的技术实现差别。

译注：除非特别说明，下文中所有的“资源”均指代“Asset”。



本文内容是理解在Unity中如何高效加载和卸载资源的基础。正确的资源管理对缩短加载时间并减少内存占用来说至关重要。之前已经介绍了 *[上半部分](http://www.cnblogs.com/zhaoqingqing/p/5823927.html)*，今天继续为大家分享下半部分内容。

<!-- more -->

## 1.5. 序列化和实例

### 实例ID

尽管文件GUID和本地ID已准备妥当，它们可以强有力地维护资源之间的关系，但还有一个问题，GUID比较效率低下，我们需要为运行时准备一个效率更高的解决方案。Unity在内部维护着一个缓存表(2)，负责将文件GUID和本地ID转换成为整数数值，这个数值在本次会话中是唯一的，称作实例ID。实例ID会简单地以单调递增的方式分配给缓存中新注册的对象。



缓存负责维护实例ID与文件GUID和本地ID定义的对象源数据位置以及对象在内存中的地址（如果存在）的映射。 这样Unity就能够强而有力地保证它们互相之间的引用关系。通过解析实例ID，我们能够快速找到并返回ID对应的已载入实例。如果目标没有被加载，则Unity会通过文件GUID和本地ID解析获得对象的源数据，实时载入对象。



启动时，实例ID缓存与所有工程内建的对象（例如在场景中被引用），以及Resources文件夹下的所有对象，都会一起被初始化。如果在运行时(3)导入了新的资源，或从AssetBundle中载入了新的对象，缓存会被更新并为这些对象添加相应条目。实例ID仅在失效时才会被从缓存中移除，当提供了指定文件GUID和本地ID的AssetBundle被卸载时就会产生移除操作。



卸载AssetBundle会使实例ID失效，实例ID与其文件GUID和本地ID之间的映射会被删除以便节省内存。重新载入AssetBundle后，载入的每个对象都会获得一个新的实例ID。

关于AssetBundle隐式卸载的更深层次讨论，参考[AssetBundle Usage Patterns](https://unity3d.com/learn/tutorials/topics/best-practices/asset-bundle-usage-patterns)中的管理[已加载资源章节](https://unity3d.com/learn/tutorials/topics/best-practices/asset-bundle-usage-patterns#Managing_Loaded_Assets)。

### 注意事项(IOS上挂起状态)

注意在某些平台上，有一些系统事件会使得对象在内存中被强行卸载。例如iOS平台上，当一个app处于挂起状态时，图形资源就会从显存中卸载。如果这些对象都来自于一个已被卸除的AssetBundle时，Unity将无法再次从源数据处加载这些对象。任何已有的对这些对象的引用都会失效。这个例子中，导致的后果就是出现网格不可见（丢失），或模型的纹理和材质呈现为洋红色（Shader丢失）。

### 提示

提示：在运行时，对上述控制流程的描述并非完全准确。文件GUID和本地ID的比较操作在载入负担较重时的效率也会下降。构建Unity工程时，文件GUID和本地ID，确切地说会被映射到一种更简单的格式中。不过概念还是大致相同的，在考虑“运行时”的时候以文件GUID和本地ID的工作方式为思路还是有一些参考意义的。

这也是在运行时资源的文件GUID无法被调取的原因。

## 1.6.MonoScripts

理解MonoBehaviour很重要的一点是知道它有一个对MonoScript的引用。MonoScript的用途非常简单，里面包括了定位某个具体的编程类所需要的信息。这两种对象都没有程序类的可执行代码。



一个MonoScript含有三个字符串：程序库名称，类名称，命名空间。

### 程序库(dll)

构建工程时，Unity会收集Assets文件夹中独立的脚本文件并将它们编译，组成一个Mono程序库。要特别说明的是，Unity会将Assets目录中的语言分开编译，Assets/Plugins目录中的脚本同理。Plugins子目录之外的C#脚本会放在Assembly-CSharp.dll中。而Plugins及其子目录中的脚本则放置在Assembly-CSharp-firstpass.dll中，以此类推。



这些程序库（加上预编译好的DLL程序库）将被包含在最终构建的Unity应用程序中。这些程序库都会被MonoScript所引用。与其他类型的资源不同，Unity应用程序中的所有应用程序都会在程序第一次启动时被加载。



MonoScript就是为什么AssetBundle（或一个场景，一个Prefab）中的MonoBehaviour组件不包含有任何实际可执行代码的原因。这样就可以让不同的MonoBehaviour引用某个共享类，即便这些MonoBehaviour不在同一个AssetBundle中。

## 1.7.资源（Resource）生命周期

**UnityEngine.Objects从内存中加载或卸载的时间点是定义好的**。为了缩短程序载入时间，管理应用程序的内存足迹，理解UnityEinge.Object的资源生命周期是很重要的。

### 加载UnityEngine.Object的两种方式

**有两种加载UnityEngine.Object的方式：自动加载或外部加载**。

当对象的实例ID与对象本身解引用，对象当前未被加载到内存中，而且可以定位到对象的源数据，此时对象会被自动加载。对象也可以外部加载，通过在脚本中创建对象或调用资源加载API来载入对象（例如[AssetBundle.LoadAsset](http://docs.unity3d.com/ScriptReference/AssetBundle.LoadAsset.html?_ga=1.182882597.2098911022.1464579255)）。

对象加载后，Unity会尝试修复任何可能存在的引用关系，通过将每个引用的文件GUID和本地ID转化成为实例ID的方式。

一旦对象的实例ID被解引用且满足以下两个标准时，对象会被强制加载：

1. 实例ID引用了一个没有被加载的对象。
2. 实例ID在缓存中存在对应的有效GUID和本地ID。



这种情况通常会在引用被加载并解析后很短的一段时间内发生。

如果文件GUID和本地ID没有实例ID，或一个已卸载对象的实例ID引用了非法的文件GUID和本地ID，则引用本身会被保留，但实际对象不会被加载。在Unity编辑器中表现为“（空）”引用。在运行的应用程序中，或场景视图里，“（空）”对象通常以多种方式表示，这取决于丢失对象的类型：网格会变得不可见，纹理呈现为洋红色等等。

### 对象被卸载的三种情况

对象被卸载有以下三种情况：

1. 闲置资源清理进程开始后，一些对象会被自动卸载。该过程通常会在切换场景切不保留原场景（例如调用了非叠加的场景切换API [Application.LoadLevel](http://docs.unity3d.com/ScriptReference/Application.LoadLevel.html?_ga=1.216561301.2098911022.1464579255)），或者脚本中调用了[Resources.UnloadUnusedAssets](http://docs.unity3d.com/ScriptReference/Resources.UnloadUnusedAssets.html?_ga=1.216561301.2098911022.1464579255)时自动触发。该进程仅卸载没有被引用的对象：对象仅在Mono变量不存在对其的引用，且不存在引用该对象的其他活动对象时被卸载。
2. Resources目录中的对象可以通过调用[Resources.UnloadAsset](http://docs.unity3d.com/ScriptReference/Resources.UnloadAsset.html?_ga=1.216561301.2098911022.1464579255) API主动卸载。卸载后对象的实例ID会保持可用状态，对文件GUID和本地ID的条目会被保留且仍然有效。如果有Mono变量或其他有指向该对象的活动对象引用了被[Resources.UnloadAsset](http://docs.unity3d.com/ScriptReference/Resources.UnloadAsset.html?_ga=1.246716935.2098911022.1464579255)卸载的对象，则该对象会在任意有效的引用被解引用时立刻重新加载。
3. 调用[AssetBundle.Unload(true)](http://docs.unity3d.com/ScriptReference/AssetBundle.Unload.html?_ga=1.246716935.2098911022.1464579255) API时，加载自AssetBundle的对象会被立刻自动卸载。该操作会释放对象实例ID的文件GUID和本地ID引用，任何对卸载对象的引用都会变成“（空）”引用。C#脚本中，任何试图访问已卸载对象上的方法和属性都会导致抛出空引用异常（NullReferenceException）。

如果调用了[AssetBundle.Unload(false)](http://docs.unity3d.com/ScriptReference/AssetBundle.Unload.html?_ga=1.142447025.2098911022.1464579255)，被卸载的AssetBundle中仍然处于激活状态的对象不会被回收，但Unity会释放其实例ID的文件GUID和本地GUID引用。之后假如它们被从内存中卸载，只剩下对这些被卸载对象额引用，Unity无法再次重新加载这些对象(4)。

## 1.8.载入大型结构树（Hierarchies）

当序列化含有大量Unity游戏对象的结构树时（例如序列化Prefab），要记住一点，即整个结构树都会被完全序列化。这就是说，结构树中的每一个游戏对象和组件都会在序列化数据中单独表示。这会对游戏对象结构的载入与实例化的耗时带来有趣的影响。


假设一个代码块实例化了一定数量的游戏对象，结构树非常庞大的单个Prefab的实例化，会比分别实例化结构树的多个模块然后运行时组合花费更多的CPU时间。



深度分析底层数据后发现，实例化和唤醒游戏对象所花费的CPU时间在各种情况下大致是相同的，单个独立的Prefab的实例化和唤醒仅需非常少量的CPU时间（因为不需要tranmpolining和SendTransformChanged回调）。但是，这些细小的时间节省相对于花在读取和序列化数据上的时间是很不值的。



正如之前提到的，序列化单独的Prefab时，每个游戏对象和其组件都会被分开序列化——即便数据是重复的。一个有30个独立元素的UI界面中，Unity会为这些元素序列化30次，这就会产生大量的序列化数据。载入时，所有30个重复元素的游戏对象和组件在被转化为新实例对象之前都需要从硬盘中进行读取。正是这里讨论的读取时间决定了实例化大型Prefab的性能花销。



Unity支持嵌套Prefab之后，对于有载入大型结构体游戏对象需求的工程而言，要想减少这类工程的载入时间，可以考虑将大型Prefab中的可重用元素分开存储到不同的Prefab中，并在运行时对它们进行实例化，而不是完全依靠Unity的序列化和Prefab系统。

## Footnotes(脚注)

1. 在文件中，本地ID是唯一的。即在一个资源文件中，里面包含的本地ID都是不重复的。

2. 在内部，这种缓存被称为PersistentManager。实际的转换工作在在Unity的C++ Remapper类中进行，Remapper类没有提供任何C# API调用接口。

3. 运行时创建资源的示例是在脚本中创建Texture2D对象：`var myTexture = new Texture2D(1027, 768);`

4. 程序运行时对象并没有被卸载却被从内存中移除的情况通常会发生在Unity失去了对图形内容的控制的时候。例如，当手机应用被挂起并被强制在后台运行。这种情况下，手机操作系统通常会将所有的图形资源从GPU显存中强行卸载。之后APP再回到前台运行时，Unity不得不重新向GPU上传需要的材质、着色器和网格数据，以便恢复场景的正常渲染。


------

到此整个Unity内部资源管理与对象引用及序列化的内容就结束了，希望看完本文的你对如何合理分配Unity项目结构都有了比较清晰的概念。

原文链接：[http://unity3d.com/cn/learn/tuto ... tion?playlist=30089](http://unity3d.com/cn/learn/tutorials/topics/best-practices/assets-objects-and-serialization?playlist=30089)
感谢Unity官方翻译组成员“E.A.S”对本文翻译所做的贡献。
转载请注明来源：Unity官方中文社区 ([forum.china.unity3d.com](http://forum.china.unity3d.com/thread-20129-1-1.html))。请勿私自更改任何版权说明信息。





