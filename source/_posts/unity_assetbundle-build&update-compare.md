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


本文主要讨论两种做法的比较

## 共同点
游戏中所有资源都打包成assetbundle，在游戏启动时进行加载。
同步和异步两种加载方式
Assetbundle文件放在StreamingAssets目录，区分不同平台。以stream的形式加载，用Unity的` www.load `API

## 区别
### 完整打包
在加载时一般只需要加载单个文件+共用资源即可，
### 分散打包
分散打包则先加载依赖再加载主文件
### UI