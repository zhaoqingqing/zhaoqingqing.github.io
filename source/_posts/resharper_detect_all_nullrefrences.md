---
title: Resharper 检测所有NullReferenceExceptions(空指针)
date: 2016-10-23
tags:
    - Unity3D
categories: Unity3D项目日志
---
### 需求分析

”null exception“很见的一种异常，但在某些情况下却会引起严重的bug！

本文目的就是对代码进行null 的检查，避免不应该出现的Error。

本文借助reshaper，全局检测项目中所有可能出现的null exception

关于resharper的知识：http://zzk.cnblogs.com/s?w=blog%3Azhaoqingqing%20resharper

<!-- more -->

### 环境

Windows 7 X64

Visual Studio 2015社区版

Resharper 10.x



### 使用方法

在可能出现null的字段/属性上按**Alt+Enter** ，让Resharper智能分析整个项目中可能出现null exception的地方

![](http://images2015.cnblogs.com/blog/363476/201610/363476-20161017111044607-575199101.png)





### 分析结果

待resharper分析完成之后，可以点击对应的代码文件，跳到对应的位置，进行代码修正。

![](http://images2015.cnblogs.com/blog/363476/201610/363476-20161017111025810-1767474978.png)

### Resharper的Null Detect

https://www.jetbrains.com/help/resharper/2016.1/Detect_possible_NullReferenceExceptions.html



### 扩展阅读

《编程精粹:编写高质量C语言代码》
