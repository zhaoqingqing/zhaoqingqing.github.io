---
title: 检测是否安装.NET Framework 环境
---



### 错误信息

应用程序正常初始化0xc0000135*失败



### 讨论一

你要用.net写一个程序检查目标机器上有没有安装.net?

前提是： 如果目标机器上没有安装.net, 你的检测程序根本就运行不起来，更别谈上它检测了

不过你可以用非托管的代码来写个引导程序，如C++ 来检测一下注册表就知道了

很久之前倒是用C++搞过一个类似的引导程序，发你参考一下思路吧：

http://download.csdn.net/detail/q107770540/4753895

参考：http://bbs.csdn.net/topics/390383802

### 参考二

如可检测.NET Framework是否安装及安装版本

http://m.blog.csdn.net/article/details?id=47000813



### 方案三

[https://msdn.microsoft.com/zh-cn/library/hh925568(v=vs.110).aspx](https://msdn.microsoft.com/zh-cn/library/hh925568(v=vs.110).aspx)