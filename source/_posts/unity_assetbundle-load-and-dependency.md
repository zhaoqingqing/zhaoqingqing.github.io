---
title: Unity3D-Assetbundle-加载和依赖处理
date: 2016-08-05
tags:
    - Unity3D
categories: Unity3D项目日志
---

原理适用于Unity 4.x和Unity 5.x

不过Unity 5.x有了自己的依赖系统

## ab依赖处理

以下内容来自ulua&tolua技术交流群，时间2016-08-03 14:30 ~ 15:30

### 加载

**故国之晚秋 **

异步读取AB就是要注意，读取状态

因为AB不仅仅只有读取，还有清理策略，要防止正在加载，又被清理

还有就是，A依赖B，C也依赖B。现在读取A，先读取B，这时候立马读取C，这时候B也在读取，不要反复去读取，减少

读取A，本来就会形成队列A->C->D,这是A形成的队列，然后读取B，形成
B->C（只是把C的节点加到B，并不会再去加C后面的依赖，只是判断C的状态），读取顺序都是从右到左

并且，出现这个情况只是在都是处于正在读取状态

如果用WWW，同时读太多，内存吃紧

AB不要打得太散也不要打得太多

简单的解决办法：

同步AB都解决了，同步加载AB没有这个问题，同步ab最好不要做的太细。

同步很简单，同步读取AB

**上海-江南-5.3.1f4 **

特别是公共资源
多引用的单独打包
卸载清理要处理好

衣匠

记住一点，先依赖，后主资源

### 资源名

自动化,

我是通过文件名规范来做，有人是通过工具来做

建目录的时候，加个@或者_就行，主要跟文件夹有关，否则一个资源一个AB

### 压缩

之前项目只支持非压缩AB，
后面加入压缩AB后，复杂度几何上升
之前游戏项目也只用了非压缩AB

我们现在也不用LZMA了，原因是加载速度和内存

#### fbx压缩

晚秋，你fbx压缩是3dmax导出的插件啊还是unity插件？

3DSMAX 脚本插件，和UNITY插件两个都有

原理：fbx剔除多余帧，有加判断

### 其它

usb 沁血寒冰

------



**故国之晚秋**

近期：开源fbx压缩

计划：ab加载依赖系统



其它

Assetbundle文件放在StreamingAssets目录，区分不同平台。以stream的形式加载，用Unity的` www.load `API
