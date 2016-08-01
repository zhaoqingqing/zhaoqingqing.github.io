##  readme
qingqing's blog https://zhaoqingqing.github.io/

## modules

### nodejs v4.4.4 x86

历史版本下载：http://nodejs.cn/download/releases/

(PS：win10 x64 + node-v6.3.1-x64  折腾老久都是chokidar/fsevents 安装失败，所以选择了此版本的nodejs)

```bash
C:\Users\qingqing>node -v
v4.4.4
```

**命令举例**

```powershell
npm cache clean --force
npm update
```

### git 2.8.1

http://www.cnblogs.com/zhaoqingqing/p/5151192.html

```powershell
C:\Users\qingqing>git --version
git version 2.8.1.windows.1
```

本文中涉及到使用git 指令，请在运行xx\git\**git-cmd.exe**后，再执行git指令。

### hexo 3.2.2

https://github.com/hexojs/hexo

https://hexo.io/zh-cn/docs/

```powershell
C:\Users\qingqing>hexo -v
hexo-cli: 1.0.2
os: Windows_NT 10.0.10586 win32 ia32
http_parser: 2.5.2
node: 4.4.4
v8: 4.5.103.35
uv: 1.8.0
zlib: 1.2.8
ares: 1.10.1-DEV
icu: 56.1
modules: 46
openssl: 1.0.2h
```

#### 安装过程

```powershell
npm install hexo-cli -g
npm install hexo --save

echo. 指定从国内taobao镜像安装，在连接外网环境差的情况下可选
npm install -g hexo-cli --registry=https://registry.npm.taobao.org 

hexo -v
hexo init blog

echo. 安装成功后的路径：
C:\Users\qingqing\AppData\Roaming\npm\node_modules\hexo\bin
f:\blog\node_modules\hexo\bin\
```

`npm install hexo-cli -g` -g参数代表全局安装

`npm install hexo --save` 中的save参数是把此模块参数保存到package.json中

#### 可能遇到问题

hexo 不是内部命令

解决办法：

- 添加hexo的安装路径到系统的环境变量
- 重新安装nodejs，选择旧一些的版本
- 在hexo的github上寻找答案或帮助

### hexo theme NexT

https://github.com/iissnan/hexo-theme-next

http://theme-next.iissnan.com/getting-started.html

### githubpages

https://pages.github.com/

## 搭建过程

### 两个分支

master branch

​	静态html，发布文件

hexo branch

​	md source + hexo config，理论上是私密文件

### 创建分支

doc: https://git-scm.com/book/zh/ch3-2.html

```powershell
$ cd xx\zhaoqingqing.github.io
$ git branch hexo
$ git checkout hexo
  console:switched to branch 'hexo'
```

执行上述命令成功之后，在你本地就创建了 'hexo'分支。

查看所有已存在的分支：` git branch`

**推送新创建的分支到远端**

方法一

往 hexo 分支 add 某文件，commit ，并push到远端，这样远端就有了 hexo 分支

方法二

```powershell
git remote add origin <地址>
git push origin hexo
```



------



可以在git中设置默认branch为hexo，本地的下次提交就会直接提交到hexo分支

### 安装hexo

### 安装hexo theme

思路：fork 某 theme 变成sub module?

doc: https://git-scm.com/book/zh/v1/Git-%E5%B7%A5%E5%85%B7-%E5%AD%90%E6%A8%A1%E5%9D%97

### 发布博客