---
title: Unity3D-Assetbundle-打包和更新的两种做法
tags:
    - Unity3D
categories: Unity3D项目日志
---

## 前言

本文根据过往项目经验所写，部分版权归所属公司所有，未涉及代码部分。
两个项目有着不同的资源管理的方式：

- 3D MMO RPG：    整体打包
- 2D日式动作RPG：分散打包
<!-- more -->
### 开发环境

开发引擎：unity3d 4.x
运行环境：android/ios
解包工具：unity studio http://www.cnblogs.com/zhaoqingqing/p/3751351.html
安卓模拟器：海马玩 v0.10.x + 文件管理器 ，基于安卓os 4.2.2

## 完整打包
每一个资源都是整体打包，下面举例说明

### npc打包例子
某NPC文件**zjws_01f_c.unity3d** ，解包后的文件如下：

- dian02 #-464001035.dds
- ZJWS_01F_C #862139260.dds
- ZB_WS_JJIA01F_C #-358047209.dds
- uicharacter #977472046.shader
- Particle Alpha Blend #434039645.shader
- Sprite Vertex Colored #1478359439.shader

在Unity Studio中看到的Hierarchy如下：

<pre>
zjws_01f_c
    run_stand_f
    zb_ws_jjiao1f
    zjws_01f
    zjws_01f_silianji
    zjws_01f_c
</pre>

------------

### 地图打包例子
M1_ACT_ZBT_Minimap.unity3d 某地图文件，解包后的文件如下：
- M1_ACT_ZBT_Minimap #02.dds
- Unlit-Alpha #03.shader

在Unity Studio中看到的Hierarchy如下：
<pre>
无
</pre>

### UI打包例子
zjws_01f_c.unity3d 某NPC文件，解包后的文件如下：
- dian02 #-464001035.dds
- ZJWS_01F_C #862139260.dds
- ZB_WS_JJIA01F_C #-358047209.dds
- uicharacter #977472046.shader
- Particle Alpha Blend #434039645.shader
- Sprite Vertex Colored #1478359439.shader

在Unity Studio中看到的Hierarchy如下：
<pre>
zjws_01f_c
    run_stand_f
    zb_ws_jjiao1f
    zjws_01f
    zjws_01f_silianji
    zjws_01f_c
</pre>

## 资源更新
### 如何触发更新

### resversion.txt 文件列表

```json
object{2}
    buildDatetime:20160728-010315
    listResInfo[2483]
        0 {3}
        1 {3}
            strName    : composeupgrade
            strPath    : assetbundles/audios/composeupgrade.unity3d
            strHash    : c1c28bc73bb4affc7963cc073401c10
        ......此处省略几千行
```
本地的：buildDatetime : 20160727-115054
服务器：buildDatetime : 20160728-010315
两边不一致，玩家需要下载更新
PS:每次根据buildDatetime来判断是否需要更新资源？

### 要更新的文件
制作的更新包如下：
### android 目录
路径：/mnt - sdcard - Android - data - com.xx.xxgame.uc - files - xxgame - data - android
1. resversion.unity3d
2. settingclient.unity3d
3. version.txt

## 资源目录

StreamingAssets\
- Android\
- IOS\
   - Windows\

下面以Android目录为例
- assetbundles\
- scene\
   - textures\
   - resversion.txt
   - resversion.unity3d
   - settingclient.unity3d  (客户端所有的配置表，打包进同一个ab文件)

### 目录详解
<pre>
assetbundles\
    assets
    audios
    fightskill
    npc
    scene
       building
       rocks
       map/trees/textures
       ....more scene files
    ui

------------
scene\
    xxboos
       xxboss.unity3d
       xxboss.txt
       xxboss.bar
       xxboss_nav.unity3d
    bht
       bht1trap1, bht1trap2, bht1trap3 | bht2trap1, bht2trap2, bht3trap3 |
       ....更多的主城文件

------------
textures\
    3D贴图文件

</pre>

------



## 分散打包

## 资源更新
### 如何触发更新
本地assetbundle版本：10050，服务器assetbundle版本：10051，玩家需要下载更新。

### 资源目录
<pre>
StreamingAssets
   Android
      Actor
      Audio
      Card
      Common
      Effect
      Level
      Map
      UI
      ....
      Version.txt
      GameSetting.ab (客户端所有的配置表，打包进同一个ab文件)
   IOS
   Windows
</pre>

### 更新文件

**最新版本号文件**
- NewestVersion_Android.txt
  内容：`10051` 最新的assetbundle版本号
  <br/>

**更新包**
- Android_10050_10051.zip
  *10050更新到10051版本的要下载的资源*

- Android_born_10051.zip
   *10051新APK包的初始资源*

- Android_full_10051.zip
   *10051的完整资源*
  <br/>

**更新包json**
- Android_10050_10051.zip.json
  `{"MD5":"297714FA3CEF25C63AA6BF9432918278","Size":  62473769  ,"FileCount":4438}`
- Android_born_10051.zip.json
  `{"Size":  78895922  , "FileCount": 4817, "MD5": "72FD467252329D01799DD65E9CCA23F0"}`
- Android_full_10051.zip.json
  `{"MD5":"6F8D541B388C98A0FBDC6F5BF4DA7122","Size":  84529108  ,"FileCount":4864}`

------

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

## 做法比较

### 共同点
游戏中所有资源都打包成assetbundle，在游戏启动时进行加载。
同步和异步两种加载方式
Assetbundle文件放在StreamingAssets目录，区分不同平台。以stream的形式加载，用Unity的` www.load `API

### 区别
#### 完整打包
在加载时一般只需要加载单个文件+共用资源即可，
#### 分散打包
分散打包则先加载依赖再加载主文件
#### UI