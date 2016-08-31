---
title: 玩转Unity资源，对象和序列化(上)
date: 2016/08/30 22:05:25
tags: 
    - Unity3D
categories: Unity3D项目日志
---

这是一系列文章中的第二章，覆盖了[Unity5的Assets，Resources和资源管理](https://unity3d.com/learn/tutorials/topics/best-practices/guide-asset-bundles-and-resources)



本文将从Unity编辑器和运行时两个角度出发，主要探讨以下两方面内容：**Unity序列化系统内部细节**以及**Unity如何维护不同对象之间的强引用**。另外还会讨论对象与资源的技术实现差别。

译注：除非特别说明，下文中所有的“资源”均指代“Asset”。


**本文内容是理解在Unity中如何高效加载和卸载资源的基础。正确的资源管理对缩短加载时间并减少内存占用来说至关重要**。今天先为大家分享上半部分内容。
<!-- more -->


## 1.1. 深入理解资源和对象

在理解Unity如何确保万无一失地管理数据之前，首先要知道Unity是如何识别并序列化数据的。首先第一点，要正确区分资源（**Asset**）和对象（**UnityEngine.Objects**）。

### Asset

资源（Asset）是硬盘中的文件，存储在Unity工程的Assets文件夹内。例如，纹理（Texture），材质（Material）和FBX文件等，它们都是资源。一些资源的数据格式是Unity原生支持的，例如材质。有些资源则需要转换为原生的数据格式后才能被Unity使用，例如FBX文件。

### UnityEngine.Object

[UnityEngine.Object](http://docs.unity3d.com/ScriptReference/Object.html?_ga=1.146782647.2098911022.1464579255)，或者说以大写字母O开头的Object——对象，代表序列化数据的集合，表示某个资源的具体实例。它可以是Unity引擎使用的任何类型的资源，例如网格，Sprite，音频剪辑或动画剪辑。所有的对象（Object）都是UnityEngine.Object基类的子类。

### 特殊的Object类型

几乎所有的对象（Object）类型都是内建的，其中有两种比较特殊的类型。

1. **[ScriptableObject](http://docs.unity3d.com/ScriptReference/ScriptableObject.html?_ga=1.146782647.2098911022.1464579255)**为开发者提供了一套便捷的系统，供开发者自定义数据类型。这些类型可以被Unity直接序列化或反序列化，并在Unity编辑器的检视器窗口中进行操作。
2. **[MonoBehaviour](http://docs.unity3d.com/ScriptReference/MonoBehaviour.html?_ga=1.217438613.2098911022.1464579255)**提供了链接[MonoScript](http://docs.unity3d.com/ScriptReference/MonoScript.html?_ga=1.217438613.2098911022.1464579255)的容器。MonoScript是一种内部数据类型，Unity用它保存对某个特定程序集和命名空间中特定脚本类的引用，MonoScript本身不包含任何实际的可执行代码。

### 一对多关系

资源（Asset）与对象（Object）是一种一对多的关系，即一个资源文件可能会包括多个Object。

## 1.2. 对象之间的引用

所有UnityEngine.Objects都可以引用其他的UnityEngine.Objects。这里“其他的Object”可能存在于相同的资源文件中，或需要从其他资源文件导入。例如，一个材质Object通常有一个或多个纹理Object的引用。这些纹理Object一般是从一个或多个纹理资源文件中导入的（例如PNG或JPG文件）。

序列化后，这些引用由两部分数据组成：文件GUID和本地ID。文件GUID用于识别资源（Asset）文件中目标资源（Resource）的存储位置。而本地唯一(1)的ID负责识别单个资源文件中的Object，因为一个资源文件可能会包含多个Object。

### 文件GUID(.meta)

文件GUID存储于.meta文件中。Unity会在首次导入资源文件时生成.meta文件，并和资源文件一起存储在相同的目录中。

上述的识别和引用系统可以使用文本编辑器查看：

1. 创建一个全新的Unity工程，更改编辑器设置，将**Edit** - **Project Settings**  -  **Editor**中的**Version Control**设为**Visible Meta Files**，并将**Asset Serialization**设为文本。
2. 新建材质并向工程中导入一个纹理。将材质赋给场景中的一个立方体，保存场景。

使用文本编辑器打开这个材质对应的.meta文件。在文件顶端附近会有一行被标示为“guid”，该行定义了材质资源文件的文件GUID。

```yaml
fileFormatVersion: 2
guid: 6839b719d14310c4f945de352bac3767
timeCreated: 1472566765
licenseType: Pro
NativeFormatImporter:
  userData: 
  assetBundleName: 
  assetBundleVariant: 
```



### 本地ID(具体文件)

如需查看本地ID，使用文本编辑器打开材质文件，材质Object的定义大致如下：

```yaml
%YAML 1.1
%TAG !u! tag:unity3d.com,2011:
--- !u!21 &2100000

Material:

serializedVersion: 6

... more data ...
```

在上面的例子中，前面有&符号的数字就是材质的本地ID。如果这个材质的Object位于一个文件GUID为“abcdefg”的资源文件中，则该材质Object的唯一识别符就是文件GUID“abcdefg”和本地ID“2100000”的组合。

## 1.3. 为什么要用文件GUID和本地ID？

### 工作流程

在Unity中，为什么要使用文件GUID和本地ID这套系统呢？答案是为稳定服务，也是为了提供一套灵活的、无关具体平台的工作流程。文件GUID提供了文件存储位置的抽象，这样一个文件GUID就对应一个具体的文件，这个具体的文件存储在什么位置也就无关紧要了。因此我们才能随意移动这个文件而不破坏所有相关Object对这个文件的引用。

任何资源（Asset）文件中都可能含有（或通过导入产生）多个UnityEngine.Object资源（Resource），因此需要一个本地ID来对其中的Object做明确区分。

如果与资源文件相关联的文件GUID丢失，则所有对该资源文件中的Object的引用都会被破坏。这就是必须保证.meta文件具有和资源文件相同的文件名并存储在同一目录下的原因。注意Unity会重新生成丢失或被删除的.meta文件。

### Unity编辑器维护映射表

Unity编辑器负责维护一张文件路径与文件GUID之间关系的映射表。只要资源文件被读取或导入，这个映射关系就会被建立，映射会将资源的具体位置和资源的文件GUID进行关联。Unity编辑器处于打开状态时，假设一个文件的.meta意外丢失，并且该资源文件的路径没有改变，编辑器可以保证这个资源会被分配到相同的文件GUID。

如果在Unity编辑器处于关闭状态时丢失.meta文件，或资源文件被移动但没有移动对应的.meta文件时，所有对资源文件中的Object的引用都会丢失。

## 1.4. 复合资源和导入器

### 资源导入器

正如前面深入理解资源与对象中所说的一样，不能被Unity直接支持的[资源类型](https://unity3d.com/cn/learn/tutorials/topics/best-practices/assets-objects-and-serialization?playlist=30089#Inside_Assets_and_Objects)必须经过导入才可以使用——使用资源导入器来完成。这些导入器是自动调用的，您也可以使用[AssetImporter](http://docs.unity3d.com/ScriptReference/AssetImporter.html?_ga=1.200234669.2098911022.1464579255)在脚本中调用API及其子类。例如，在导入单独的纹理资源例如PNG和JPG时，[TextureImporter](http://docs.unity3d.com/ScriptReference/TextureImporter.html?_ga=1.200234669.2098911022.1464579255) API提供了导入时要使用的相关设置的访问。

导入过程最终的产物是一系列UnityEngine.Object。在Unity编辑器中，这些对象会具体表现为父资源下的多个子资源，例如作为Sprite Atlas导入的纹理材质，其下属会有多个嵌套的Sprite。每一个对象都会使用相同的文件GUID，因为它们的源数据都存储在同一个资源文件中。它们在纹理资源中的具体区分工作则使用本地ID来完成。

### Library文件夹

导入过程中会将源资源转换为匹配Unity编辑器中选定的目标平台的格式。导入过程可能会牵涉一些重量级操作，例如纹理压缩。如果每次打开Unity编辑器时都要执行这些操作，那效率就太低了。

为了解决这一问题，我们将资源导入的结果缓存在Library文件夹中。具体就是，导入进程的结果将会存储在以资源文件GUID头两位作为名称的文件夹中。这些文件夹位于 **Library/metadata/** 目录下。各个不同的对象会被序列化后存储在一个二进制文件中，文件使用资源文件的GUID来命名。

这对所有资源都是一样的，不仅仅是非原生资源。只不过Unity原生支持的资源不需要对其进行转换或序列化处理。



上半部分的内容主要介绍了资源（Asset）和对象（UnityEngine.Objects）的区别，以及文件GUID和本地ID二者的关联和差异。下半部分将为大家介绍第三种ID：对象的实例ID，并看看这些ID对资源在内存与显存中的加载和卸载分别有着怎样的作用。



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





