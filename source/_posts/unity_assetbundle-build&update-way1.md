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

开发引擎：unity3d 4.3
运行环境：android/ios
解包工具：unity studio http://www.cnblogs.com/zhaoqingqing/p/3751351.html
测试环境：安卓模拟器（海马玩 v0.10.x） + 文件管理器 ，基于安卓os 4.2.2

# 完整打包
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

TODO地图

M1_ACT_ZBT_Minimap.unity3d 某贴图文件，解包后的文件如下：
- M1_ACT_ZBT_Minimap #02.dds
- Unlit-Alpha #03.shader

在Unity Studio中看到的Hierarchy如下：
<pre>
无
</pre>

### UI打包例子
UI中每个面板单独打包，依赖于共用的UIShare

## 资源更新
### 如何触发更新

### resversion.txt 文件列表

```json
{
    "buildDatetime": "20160728-010315",
    "listResInfo": [
        {
            "strName": "M2_ZC_Minimap",
            "strPath": "textures/M2_ZC_Minimap.unity3d",
            "strHash": "bfbe425f3b74abad3fd3219c7510d6b1"
        },
        {
            "strName": "settingclient",
            "strPath": "settingclient.unity3d",
            "strHash": "6a88b87c1cce7ff2acbb8782bf7503e"
        }
          ......此处省略几千行
    ]
}
    
```
本地的：buildDatetime : 20160727-115054
服务器：buildDatetime : 20160728-010315
两边不一致，玩家需要下载更新
PS:每次根据resversion的内容是否相同来判断是否需要更新资源

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


