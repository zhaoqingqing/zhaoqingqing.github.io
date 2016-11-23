---
title: Unity3D-Assetbundle-打包和更新的两种做法
date: 2016-08-05
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

开发引擎：unity3d 4.6.x
运行环境：android/ios
解包工具：unity studio http://www.cnblogs.com/zhaoqingqing/p/3751351.html
测试环境：安卓模拟器（海马玩 v0.10.x） + 文件管理器 ，基于安卓os 4.2.2



# 分散打包

文件的打包是分散的，有依赖关系，依赖关系自己内部维护

TODO

## 资源更新

TODO

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

#### 