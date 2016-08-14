## 机制流程图
![](unity-pc版-刷新机制.png)

## 改动部分

### 登录的sign

原来sign的生成： account + ts +  key

现在sign的生成： account + ts +  key  + servergroupid

### 微端AccountSign

新加的AccountSign生成： account + servergroupid  + key

### Code库客户端 web版/pc版

code库的登录有验证，sign在本地生成。所以此处的生成方法要改

### 客户端 pc版

登录之后，在客户端保存新的AccountSign

### pc版登录

pc版登录成功之后的URL后面加多AccountSign

## WdHelper.exe和xx.php通信

### wdhelper.exe向PHP请求

<pre>
xx.php?AccountSign=xxxxxx&Password=1234&ServerIP=218.15.113.133&Platform=1&IsMinor=1&IsOpenIndulg=1
</pre>

### PHP返回 验证成功

<pre>
result=1&UserName=49you_80&Password=1234&ServerGroupID=5&Sign=3C1DEB7ED0946DD5C2C7BF6DB23145AF&TS=1470041042&ServerIP=218.15.113.133&Platform=1&IsMinor=1&IsOpenIndulg=1&AccountSign=xxxx
</pre>

### PHP返回 验证失败

<pre>
result=0&ERROR=0/1
</pre>

#### 错误枚举

0:该帐号不存在，请重新登录

1:未知错误，请重新登录

#### 错误处理

TIPS：尽量做到错误枚举的显示内容可配置

1、弹出错误信息

2、退出wdhelper.exe，并启动微端