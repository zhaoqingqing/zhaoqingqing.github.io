---
title: 从Unity3D编译器升级聊起Mono
date: 2016-08-08
tags:
    - Unity3D
categories: Unity3D技术积累
---

接前篇[Unity 5.3.5p8 C#编译器升级](http://www.cnblogs.com/zhaoqingqing/p/5745881.html)，本文侧重了解一些Mono的知识。

<!-- more -->

## Unity3D的编译器升级

新升级的Mono C#编译器（对应Mono 4.4）

Unity编辑器及播放器所使用的Mono运行时还未升级。

新编译器仍针对C# 4，是旧的编译器也支持的版本。

新编译器目标框架为.Net 3.5，也是之前支持的框架。


### Mono是如何运行的？

1. *.cs源文件通过编译器编译成IL(中间文件)
2. IL 运行在Mono的 JIT(just in time)
3. Mono虚拟机运行在物理机中

### 查看Unity使用的Mono版本

参考：http://answers.unity3d.com/questions/259448/how-to-determine-mono-version-of-unity-.html

我在unity 5.3.4f1 中测试有效，而在unity 4.3.4f1下测试无效

```csharp
using UnityEngine;
using System;
using System.Reflection;

public class MonoVersion : MonoBehaviour {
	void Start()
	{
		Type type = Type.GetType("Mono.Runtime");
		if (type != null)
		{
			MethodInfo info = type.GetMethod("GetDisplayName", BindingFlags.NonPublic | BindingFlags.Static);
			
			if (info != null)
				Debug.Log(info.Invoke(null, null));
		}
      //Debug.Log(System.Enviroment.Version);//net version?
	}
}
```

方法二

```powershell
d:\Program Files\Unity_5_34\Editor\Data\Mono\bin>monop2 --runtime-version mono.exe
runtime version: 2.0.50727.1433
```

```powershell
d:\Program Files\Unity_5_34\Editor\Data\Mono\bin>mono.exe --version
Mono JIT compiler version 2.0 (Visual Studio built mono)
Copyright (C) 2002-2010 Novell, Inc and Contributors. www.mono-project.com
        TLS:           normal
        GC:            Included Boehm (with typed GC)
        SIGSEGV:       normal
        Notification:  Thread + polling
        Architecture:  x86
        Disabled:      none
```

## Mono

### Mono是.Net Framework？

.NET和Mono两者是完全独立的，简单的理解：Mono项目是第三方的.Net Framework实现，比喻为.Net的跨平台版，绝大多数C#的特性在.net和mono中都是相同的，少部分特性两者的实现不一致。

Mono的目标是创建一系列符合ECMA 标准（`Ecma-334 `和`Ecma-33`5）的.NET 工具，包括C# 编译器和共通语言执行平台

Mono 虚拟机包含一个实时编译引擎，该引擎可用于如下处理器：x86，SPARC，PowerPC，ARM，S390（32位模式和64位模式），x86-64，IA64 和64位模式的 SPARC。该虚拟机可以将代码实时编译或者预先编译到原生代码。对于那些没有列出来的系统，则使用的是代码解释器。

------

这部分内容摘自：http://www.mono-project.com/docs/about-mono/

### mono - C# Compiler

> Mono’s C# compiler is feature complete for C# 1.0, 2.0, 3.0, 4.0 and 5.0 (ECMA). A good description of the feature of the various versions is available on [Wikipedia](http://en.wikipedia.org/wiki/C_Sharp_%28programming_language%29#Versions).
>

> mono的c#编译器提供c# 1.0,2.0,3.0,4.0和5.0(ECMA)完整的功能。不同版本的特性的描述可以在[维基百科](http://en.wikipedia.org/wiki/C_Sharp_%28programming_language%29#Versions)上查看。



### mono - Mono Runtime

>The runtime implements the ECMA Common Language Infrastructure (CLI). The runtime provides a Just-in-Time (JIT) compiler, an Ahead-of-Time compiler (AOT), a library loader, the garbage collector, a threading system and interoperability functionality.

>mono运行时实现了ECMA 公共语言基础(CLI)。运行时提供了即时(JIT)编译器，提前的编译器(AOT)，库加载器，垃圾回收机器，线程操作，互操作性
>



### mono - .NET Frame Class Libray

>The Mono platform provides a comprehensive set of classes that provide a solid foundation to build applications on. These classes are compatible with Microsoft’s .Net Framework classes.

> Mono平台提供了一组全面的类来构建应用程序提供一个坚实的基础。这些类是兼容微软的.Net framework



### mono - Mono Class Libray

>Mono also provides many classes that go above and beyond the Base Class Library provided by Microsoft. These provide additional functionality that are useful, especially in building Linux applications. Some examples are classes for Gtk+, Zip files, LDAP, OpenGL, Cairo, POSIX, etc.

> Mono还提供了很多类,甚至超越了微软提供的基类库。这些提供额外的功能是有用的,特别是在构建Linux应用程序。一些例子是Gtk +,Zip文件、LDAP、OpenGL,Cairo,POSIX等等。



## .Net和Mono

### .Net和C#版本
.Net版本和C#版本：http://www.cnblogs.com/zhaoqingqing/p/5478239.html

| .net framework | c# version | note      | date    |
| -------------- | ---------- | --------- | ------- |
| .net 3.5       | 3.0        | vs2008    | 2007-08 |
| .net 4.0       | 4.0        | vs2010    | 2010-04 |
| .net 4.5       | 5.0        | vs2012/13 | 2012-10 |
| .net 4.6       | 6.0        | vs2015    | 2015-07 |
### .Net和Mono版本关系

一个大致的版本对应关系

| date       | mono version | net version         | note |
| ---------- | ------------ | ------------------- | ---- |
| 2008-10-01 | mono 2.0     | .net 2.0    c# 3.0  |      |
| 2011-12-19 | mono 2.10.8  | .net 4.0    c# 4.0  |      |
| 2014-03-31 | mono 3.4     | .net 4.5    c#5.0   |      |
| 2016-06-08 | mono 4.4.0   | .net 4.6.1    c#6.0 |      |
mono编译器版本升级 mono运行库没有升级，目前运行时的mono还是 3.5

### Mono发行版本

| Date(时间)   | Version(版本) | Note(备注)                                 |
| ---------- | ----------- | ---------------------------------------- |
| 2004-06-30 | 1.0         | C# 1.0 support                           |
| 2006-11-09 | 1.2         | C# 2.0 support                           |
| 2008-10-06 | 2.0         | .Net 2.0 C# 3.0                          |
| 2010-09-22 | 2.8         | .Net profile C#4.0 support               |
| 2012-10-15 | 3.0         | C# 5.0 support,async base class libray,MVC4 partial |
| 2014-03-31 | 3.4         |                                          |
| 2015-04-29 | 4.0         | .NET 4.5 profile .NET4.5 assembiles,C#6.0,integrate .net core code |
| 2014-03-31 | 4.2         |                                          |
| 2016-06-08 | 4.4         |                                          |
| 2016-08-01 | 4.6         |                                          |

2016 Mono joins the .NET Foundation

> 参考资料

Mono release: http://www.mono-project.com/docs/about-mono/releases/
Mono 4.4：http://www.mono-project.com/docs/about-mono/releases/4.4.0/
Mono software: [https://en.wikipedia.org/wiki/Mono_(software)](https://en.wikipedia.org/wiki/Mono_(software))

### Unity3D发行版本

| Unity Version | Mono Runtime/Compiler Version | Note                       |
| ------------- | ----------------------------- | -------------------------- |
| 3.5           |                               |                            |
| 4.0           |                               | 引擎大版本升级                    |
| 4.3           |                               | 新的2D工具，Sprite              |
| 4.6           |                               | uGUI，IL2CPP                |
| 5.0           | 2.0/2.0                       | shader，assetbundle重大改变 ，等等 |
| 5.3.5p8       | 2.0/4.4                       | Mono编译器升级运行时未升级，等等         |
| 5.4.0 f1      | 2.0/2.0                       | 更多VR平台支持，等等                |

扩展资料

http://blogs.unity3d.com/2016/04/01/unity-joins-the-net-foundation/

## 更多信息

### 我的疑问

  编译器和运行时版本不匹配可以正常执行？

### 文献资料

mono project github: https://github.com/mono/mono

mono doc: http://www.mono-project.com/docs/

Mono英文百科：[[https://en.wikipedia.org/wiki/Mono](https://en.wikipedia.org/wiki/Mono)](https://en.wikipedia.org/wiki/Mono)

Mono中文百科：[https://zh.wikipedia.org/wiki/Mono](https://zh.wikipedia.org/wiki/Mono)

### 扩展资料

[[.NET Core与.NET Framework、Mono之间的关系](http://www.cnblogs.com/shanyou/p/4295163.html)](http://www.cnblogs.com/shanyou/p/4295163.html)

[魅力 .NET：从 Mono、.NET Core 说起](http://kb.cnblogs.com/page/514268/)

[[c#基础系列二\]：从编译，执行过程理解c#](http://www.cnblogs.com/yeanzhi/archive/2013/02/24/2924687.html)

[Mono为何能跨平台？聊聊CIL(MSIL)](http://www.cnblogs.com/murongxiaopifu/p/4211964.html)

国内 Mono 相关文章汇总：[http://www.cnblogs.com/shanyou/archive/2012/07/28/2612919.html](http://www.cnblogs.com/shanyou/archive/2012/07/28/2612919.html)





