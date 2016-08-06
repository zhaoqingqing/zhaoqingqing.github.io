---
title: Unity 5.3.5p8的C#编译器升级
date: 2016-08-06
---



## Unity 5.3.5p8的C#编译器升级

**注意：该版本是单独升级C#编译器的测试版！请使用文中提供的下载链接！**

基于Unity 5.3.5p8的C#编译器升级！[下载链接](http://beta.unity3d.com/download/5e8bd2cd2415/public_download.html)

试用该版本前请先备份项目，遇到任何问题请及时反馈到Unity官方中文社区（http://forum.china.unity3d.com/）。

新升级的Mono C#编译器（对应Mono 4.4）将用于编译所有的C#脚本。
<!-- more -->

## 重要注意事项
Unity编辑器及播放器所使用的Mono运行时还未升级。
新编译器仍针对C# 4。是旧的编译器也支持的版本。
新编译器目标框架为.Net 3.5，也是之前支持的框架。

这只是升级Mono/.Net迈出的第一步。

## FAQ


### 对现有项目有何影响？
该升级应该能够“正常工作”，它并未添加任何新功能。不会对现有项目带来任何影响，还有些小益处。新的Mono和C#编译器与之前相比运行速度普遍更快，而且修复了多年以来的Bug。


### 有什么需要注意的？
理论上有可能在运行时产生奇怪的问题。但我们自己测试的过程中并未发现。

大多数情况下都会在编译时发现问题。目前为止我们遇到的情况有：
有些无效的C#代码是旧的编译器允许但新的编译器会报错的
涉及到编译器特定属性（安装目录、名称等）的代码或插件会出错

另外，如果你经常使用MonoDevelop或VSTU（Visual Studio Tools for Unity）调试C#代码，那欢迎向我们反馈你遇到的任何问题。编译器输出结果应该是一致的，但调试器可能对于某些生成的代码模式会很敏感。


### 有什么重大变化吗？
新编译器有一处已知的重大变化，就是闭包获取foreach变量的方式，这是由C#语言团队做出的改变。也就是说，如下代码将输出“b a r”而非目前的“r r r”：

```csharp
var actions = new Action[3];
var j = 0;
foreach (var c in "bar")
   actions[j++] = () => Console.WriteLine(c);
         
foreach (var a in actions)       
    a();
```

使用较新的VS或Mono均会得到这样的结果，这也是目前WSA平台所表现的行为，具体细节请看：

[https://msdn.microsoft.com/en-us/library/hh678682(v=vs.110).aspx](https://msdn.microsoft.com/en-us/library/hh678682(v=vs.110).aspx)


### 是否修复了“foreach”内存分配的问题？
某些情况下是的，问题描述请看：

[http://stackoverflow.com/questions/18552669/memory-allocation-when-using-foreach-loops-in-c-sharp](http://stackoverflow.com/questions/18552669/memory-allocation-when-using-foreach-loops-in-c-sharp)

对Array、List<T>、Dictionary<T>或HashSet<T>做“foreach”遍历是不会分配内存的。


### 为何不面向C# 5或C# 6？
第一步我们希望先推出新的编译器。未来再启用新的C#特性。

支持C#语言新特性面临的几个问题：
目前的生态系统不能完全把控新的C#（脚本更新器、调试器、特定的MonoDevelop/VisualStudio版本）
目前仍以旧的.Net 3.5为目标框架。在旧框架上支持新的C#或多或少会导致些迷惑。从头开始编写所有代码也许可行，但对于已有代码来说可能需要C#语言和框架都是最新才可以。

原文出处：http://forum.china.unity3d.com/thread-19893-1-1.html?sukey=3997c0719f151520c3814d65da88b37940419f1240e8b1919f76db44b25a351e66aba58667def65781188fd3f96d1fc2

## 个人见解

### 疑问地方
新升级的Mono C#编译器（对应Mono 4.4）

Unity编辑器及播放器所使用的Mono运行时还未升级。

新编译器仍针对C# 4。是旧的编译器也支持的版本。

新编译器目标框架为.Net 3.5，也是之前支持的框架。

### 我的理解
.Net版本和C#版本：http://www.cnblogs.com/zhaoqingqing/p/5478239.html

.Net 3.5 ->C#3.0   .Net 4.0 ->C#4.0   

mono 4.4 == net 3.5 大概的类比

mono编译器版本升级 mono运行库没有升级，目前运行时的mono还是 3.5



