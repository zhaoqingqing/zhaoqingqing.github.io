---
title:页游微端多平台/渠道
date: 2016/08/14 18:23:25
tags:
    - Unity3D
	- Visual Studio
categories: Unity3D项目日志
---

## 页游微端的几个问题及解答

### 为什么要开发游戏微游？

众所周知，Google大佬的Chrome浏览器在2015年时停止支持NPAPI，unity3d引擎的web player也是NPAPI的范畴，而我司的3D网页游戏正是使用unity3d引擎的web player，所以在Chrome 45及之后版本将打开我们的游戏。



### 微端版是导出windows平台而不是web player?

正是如此，下载微端玩的正是导出game.exe+game_Data而不是webplayer.unity3d+html，简单点来说: 通过下载微端玩游戏其实是客户端游戏(端游)，而通过网页入口玩游戏还是unity web player.



### 为什么微端不是内嵌网页而是客户端游戏？

这个问题问的好！

假如是内嵌网页，那么玩家的还是要已安装unity web player才能进入游戏(网页插件类似flash)

客户端游戏无需安装web player插件，且端游可以利用更多系统性能，比如多线程，保存资源到本地等



### 为什么要做多平台/渠道打包功能？

我司U3D开发的大型3DMMORPG网页游戏，先后在腾讯，49游上线，近期内将和更多渠道和平台联运，包括多玩(yy)，37玩，爱乐玩等。

微端登录和充值的流程

1. 微端登录器 ——> 登录渠道——>回调游戏服务器 ——>启动并进入游戏
2. 游戏内充值——> 跳到渠道的充值接口——>回调游戏服务器——>向游戏发放道具

**1. 代理商和策划提出的需求**

- 腾讯的游戏版本是1.1
- 多玩的版本是1.2
- 49游的版本是1.3

所以游戏资源需要进行分渠道管理，代码也进行分支版本管理。

**2. 不同渠道/平台的微端有什么区别？**

- 腾讯渠道：ab资源+代码，微端登录页QQ空间，支付是跳转到广点通
- 49游渠道：ab资源+代码，微端登录页平台分配的游戏入口，支付是跳转到49游充值
- 多玩渠道：ab资源+代码，微端登录页yy登录，支付是跳转到yy游戏充值中心
- 爱 乐 玩  ：ab资源+代码，微端登录页平台分配的游戏入口，支付是跳转到爱乐玩充值

**3. 多平台要做的事**

从上述2中可以看出，不同的渠道对应不同的微端以应付差异化，目前来说资源路径，代码，登录，充值链接等一些配置信息上的差异，当然每个渠道微端的外观也可能有差异



微端的技术分析请阅读：[[http://www.cnblogs.com/zhaoqingqing/p/5671398.html](http://www.cnblogs.com/zhaoqingqing/p/5671398.html)](http://www.cnblogs.com/zhaoqingqing/p/5671398.html)

## 制作多渠道的思路分析

### 思路1.单母包+安装器+配置表

微端只打包一次，安装器打包多次，每个平台/渠道有自己的专属配置文件

**如何识别微端的渠道？**

微端启动时读取目录中的配置文件识别当前平台，并将参数传递给游戏客户端


**如何发布不同渠道的微端？**

- A渠道 = 母包 + A渠道配置文件 + A渠道安装器

- B渠道 = 母包 + B渠道配置文件 + B渠道安装器

- N渠道 = 母包 + N渠道配置文件 + N渠道安装器


**微端母包有那些文件？**

- fileupdate : 检查更新程序
- launcher:  游戏登录器/启动器
- uninst : 卸载程序
- unity pc game : unity 导出的windows程序，包含游戏的初始资源及配置文件

更多微端的技术分析请阅读：[http://www.cnblogs.com/zhaoqingqing/p/5671398.html](http://www.cnblogs.com/zhaoqingqing/p/5671398.html)

### 思路2.打多次包 宏定义

把渠道的配置信息写在代码中，通过宏定义区分不同的渠道，但需要打包多次

```csharp
	     string loginURL = "";
#if you49
		loginURL = http://ylxxz.49you.com/client_login.html
#elif ewan21
		loginURL = http://www.2lewan.com/client/ylxxz
#elif duowan
		loginURL = http://www.duowan.com/ylxxz
#endif
```

**如何识别微端的渠道？**

答：微端启动时，通过代码中的宏定义识别识别当前平台，并将参数传递给游戏客户端

**如何发布不同渠道的微端？**

- A渠道 = 打包A渠道包
- A渠道 = 打包B渠道包
- N渠道 = 打包N渠道包

## 我的解决方案

经过思考后我使用思路1**母包+配置表** (注意点：针对不同的渠道，安装器需要重新打包)

### 实现步骤

自己编写简易的多渠道打包工具，打包工具主要功能及界面如下

- 主界面：自动编译母包+安装器
- 渠道选择：从列表选择渠道，程序自动验证渠道配置信息并提供预览
- 打包结果：记录并显示打包结果

### 项目设定

打包工具结合项目的配置信息，进行多渠道打包，项目工程目录如下

- 微端母包 源代码
- 微端安装器 源代码
- 各个平台/渠道 配置文件 /channel_configs  里面的文件如下
  - 49you.json
  - 2lewan.json
  - duowan.json
  - 37wan.json

- 打包后的渠道文件存放位置 /channel_packages
  - /49you
  - /2lewan
  - /duowan
  - /37wan

### 安装器打包思路

1. 合并安装器+配置文件
2. vs打包配置(xml)
3. 配置信息写在代码中，打包时替换代码文件或动态生成代码文件

### 思路1. 安装器和config合并

**通过ilmerge合并文件**

只能合并.net assemblies ，不能合并txt等非.net类型文件

http://www.microsoft.com/en-us/download/details.aspx?id=17630

https://ilmergegui.codeplex.com/

**通过配置文件创建纯资源dll** 

https://msdn.microsoft.com/zh-cn/library/24b2tcy0%28VS.80%29.aspx?f=255&MSPPError=-2147217396

此方法觉得本末倒置了，所以决定使用msbuild来打包不同的安装器

### 思路2. msbuild

在 Visual Studio 中构建应用程序  https://msdn.microsoft.com/zh-cn/library/cyz1h6zd.aspx

msbuild: https://msdn.microsoft.com/zh-cn/library/dd393574.aspx

msbuild + xml (project.csproj)

## msbuild 打包

### 指定条件编译符号(预编译宏)

项目解决方案：WinForm.sln，主要代码如下：

```c#
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
            string channel = "default";
#if GooglePlay
            channel="google play";
#elif Samsung
            channel="sam sung store";
#elif AppStore
            channel="app store";
#endif
            this.textBox1.Text = channel;
        }
    }
```

假设当前要打包 googleplay渠道

```powershell
msbuild /m WinForm.sln /t:Rebuild /p:Configuration=Debug /p:DefineConstants="GooglePlay"
```
假设当前要打包 AppStore渠道

```powershell
msbuild /m WinForm.sln /t:Rebuild /p:Configuration=Debug /p:DefineConstants="AppStore"
```

`/p:DefineConstants="AppStore"`  指定宏

### msbuild 文档资料

MSBuild 命令行参考  [https://technet.microsoft.com/zh-cn/library/ms164311(v=vs.85).aspx](https://technet.microsoft.com/zh-cn/library/ms164311(v=vs.85).aspx)

msbuild-msdn : [https://msdn.microsoft.com/zh-cn/library/dd393574.aspx](https://msdn.microsoft.com/zh-cn/library/dd393574.aspx)

### 可能遇到的问题

**msbuild 不是内部或外部命令？**

起因：家里的机器上同时安装VS2013 ，VS15(2016) 莫名出现msbuild不是内部或外部命令

解决：C:\Program Files (x86)\MSBuild\12.0\Bin\  添加到环境变量的path中

**devenv 不是内部或外部命令？**

解决：C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\ 添加到环境变量的path中