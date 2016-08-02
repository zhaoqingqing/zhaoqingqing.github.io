---
title: 微软准备开源PowerShell
---
  

## 微软有计划在近期内开源PowerShell

近日微软再次在向开源投出橄榄枝， PowerShell是面向Windows和Windows Server的自动化平台和脚本语言，帮助用户简化系统的管理。在纳德拉的带领下微软也逐渐走向开放，根据相关爆料称微软有计划在近期内 开源该脚本语言。

知名Windows爆料人士WalkingCat和Tom Hounsell在推文都分享了他们的发现。Tom在包的元数据说明中注意到这样一段话：“PowerShell是开源、跨平台、脚本语言和丰富的对象Shell。”但是目前尚未得到微软官方证实。
<!-- more -->
## PowerShell已经开源了？

问：微软的PowerShell脚本语言已经开源了 ？

答：绝对真的！已经！

问：源码在哪？

答：微软.net源码网站。   http://referencesource.microsoft.com/

 问：为什么在哪里？

答： powershell本身是基于.net的。是`System.Management`其中的一个类。

问：powershell源码如何看？

答：在 namespaces 搜索栏输入  

`System.Management.Automation.PowerShell`

`System.Management.Automation.ScriptBlock` 等。

##  问：powershell有什么特色？

答：cmd（bat）中有命令行管道。vbs中有面向对象。 powershell相当于bat+vbs。既有命令行和管道，又有面向对象。并极大增强了命令。相当于linux中的bash+python。

面向对象比字符串增加了属性和方法。 返回的属性，比字符串粒度更小，更严禁细致。让你写脚本不用扣字符串。 方法让你不用编程，就有相关功能。

##  问：能否介绍下windows命令行的历史？

答： win2000时代：基本上只有图形功能，而对应的命令缺失。

win2003时代：图形功能，基本都有对应的命令。但只限于win。

win2008时代：除了win之外的微软软件中。图形功能，而对应的命令基本全了。但还都是面向字符串的。

win2012时代：重新造所有轮，用所有powershell命令代替所有字符串命令。使windows命令行全面进化到【面向对象】阶段。 

winxp：cmd命令行。

win7：基本powershell。

win8.1：全面【面向对象】。

win10：更全面，连剪贴板都面向对象了！ 

## 问：powershell的缺点是什么？

答： 依赖高版本windows。 

## 问：兼容老版CMD?

答：1 语法不兼容cmd和bat。powershell语法更佳。

2 所有外部命令都兼容。比如ping.exe等。 

问：powershell源码的字体和配色，看起来有点矮丑挫，咋办？

答：用powershell ise 高大上 【字体 + 配色文件】。  

##  问：powershell比CMD（bat）强在哪?

答：

1 语法方面。语法简单。有函数，功能强大。

2 有正则，有查找，替换，切割，有大字符串对象，更适合于处理字符串。

3 有多线程并发，多进程并发。有计时器。有图形界面。

##  问：powershell比python如何？

答：

1 python可以跨平台，powershell暂时不能，这是缺点。

2 powershell为win峰。win本地深耕比python深。而python有些独有的linux功能powershell没有。

2.1 有管理dns服务器，dhcp服务器，打印机，网卡，域服务器，exchange服务器管理命令。这些python不行。

2.2 .net内置图形界面【System.Windows.Forms】powershell开发图形界面比python容易。

3 手册中文，开发工具中文，调试功能佳。不容易乱码等。   

## 问：powershell比linux bash如何？

答：

1 bash中没有属性方法，python中没有管道命令行。假如python带有命令行管道，bash跪不？

2 bash中有多线程，计时器，图形界面？ 

## 问：我听说linux命令比win多，更全，对么？

答：

win中命令更多，更全面，linux不行。

1 powershell命令比linux多得多。

2 linux软件中，很多命令缺失。不能【修改软件运行时的配置】。

2.1 上述exchange服务器，命令手册在：    https://technet.microsoft.com/zh-cn/library/bb124413.aspx    500多条命令，哪个linux邮件服务器能比？

2.2 给bind服务器中的一个dns域中的一个域名，添加一个ip。如何用命令实现？

3 linux软件依赖配置文件，但是有些命令只能【修改软件运行时的配置】。而没有【导出单个配置到文件】，【导出所有配置到文件】的功能。

3.1 win2012中的iis管理命令中有Backup-WebConfiguration，Clear-WebConfiguration，Get-WebConfiguration，Restore-WebConfiguration。这些命令 今年出的nginx中有对应的命令么？ 

## 问：powershell能管理linux么？

答： 可以的。

powershell的做法是外部合作，而不是内部取代bash。

1 通过【powershell dsc】或【powershell + sftp客户端】来推拉配置文件。

2 通过【ssh客户端模块】，模拟linux终端。远程调用linux内置的bash，python等，返回结果。 

## 问：我只有winxp，2001年出的，没装powershell，没有curl，就没法用powershell了吧？

答： 可以的。

还是用外部合作的办法。通过http(s)，用b/s的方法访问。

1 找一台有高版本win的电脑或虚拟机，在上面安装上支持powershell的http服务器。如： http://www.poshserver.net/            这个软件支持http，https，php，powershell，自定义端口，win认证。

2 在bat中用mshta ( http://powershell服务器ip/xxx.ps1 "get-date" ) 之类的命令即可。 

## 问：powershell的强项优势是什么？

答：

1 .net在win中的性能【应该最佳】，强于java和python。用同样语句，用同样对象的powershell脚本，我测了几个，比py稍 快。（未做定论）个人认为是虚拟机优化的最好的原因。

2 开发winform，wpf图形界面。或者即是命令行，又是图形程序。（类似于ghost.exe）

3 powershell多线程可以用多核。

4 win系统管理最强，绝大数微软软件。

5 管理vmware，微软云，亚马逊云，和云中的虚拟机。 

## 问：将来powershell会进入安卓，mac osx么？

答： 不知道。

但powershell会进入物联网系统。win11 iot？将来树莓派应该可以用上。

原文地址：http://www.windoweye.com/news/2016/07/281052.html