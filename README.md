##  readme
windows下使用hexo + gitpages 搭建个人博客。

qingqing's blog https://zhaoqingqing.github.io/

hexo分支: blog配置文件+md源文件

master分支: 发布后的静态html

本文涉及的指令，建议以 管理员身份 在 git cmd中执行

## modules

以下是搭建需要用到的组件

### nodejs

历史版本下载：http://nodejs.cn/download/releases/

(PS：win10 x64 + node-v6.3.1-x64  一直遇到`chokidar/fsevents `安装失败，所以选择了nodejs v4.4.4 x86)

```bash
C:\Users\qingqing>node -v
v4.4.4
```

**命令举例**

```powershell
npm cache clean --force
npm update
```

### git

基于 git 2.8.1 x64

git的知识请参考：http://www.cnblogs.com/zhaoqingqing/p/5151192.html

```powershell
C:\Users\qingqing>git --version
git version 2.8.1.windows.1
```

本文中涉及到使用git 指令，请在**git-cmd.exe** 中执行git指令。

### hexo

基于hexo  3.2.2

https://github.com/hexojs/hexo

https://hexo.io/zh-cn/docs/

**安装过程和结果**

```powershell
管理员: Git CMD
C:\Users\qingqing>npm install hexo-cli -g
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

**安装过程**

建议以 管理员身份 在 git cmd中执行以下指令

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

### hexo theme NexT

https://github.com/iissnan/hexo-theme-next

http://theme-next.iissnan.com/getting-started.html

### githubpages

博客托管在git的page上：https://pages.github.com/

## 搭建过程

### 思路分析

创建两个分支

- master branch: 静态html，发布文件


- hexo branch : md source + hexo config，理论上是私密文件	


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

​      往 hexo 分支 add 某文件，commit ，并push到远端，这样远端就有了 hexo 分支

方法二

```powershell
git remote add origin <地址>
git push origin hexo
```

------

可以在git中设置默认branch为hexo，在下次提交时就会直接提交到hexo分支

### install hexo theme

思路：fork 某 theme 变成sub module?

submodule doc: https://git-scm.com/book/zh/v1/Git-%E5%B7%A5%E5%85%B7-%E5%AD%90%E6%A8%A1%E5%9D%97

**添加submodule git指令**

```powershell
git submodule add git@github.com:iissnan/hexo-theme-next.git next
```

如果提示：*Please make sure you have the correct access rights and the repository exists.*

此时建议使用Tortoisegit，并勾选 加载putty 

### 发布到博客

hexo布署到何处？打开配置文件 _config.yml，修改以下配置:

```yaml
deploy:
   type: git
   repo: git@github.com:zhaoqingqing/zhaoqingqing.github.io.git
   branch: master
```

执行此指令即可同步提交到github `F:\Document\zhaoqingqing.github.io>hexo deploy -g`

```powershell
Microsoft Windows [版本 10.0.10586]
(c) 2015 Microsoft Corporation。保留所有权利。

C:\Windows\system32>f:

F:\>cd Document\zhaoqingqing.github.io
F:\Document\zhaoqingqing.github.io>hexo deploy -g
INFO  Start processing
INFO  Files loaded in 381 ms
INFO  0 files generated in 352 ms
INFO  Deploying: git
INFO  Clearing .deploy_git folder...
INFO  Copying files from public folder...
warning: LF will be replaced by CRLF in 2016/08/01/hello-world.html.
On branch master
nothing to commit, working directory clean
Branch master set up to track remote branch master from git@github.com:zhaoqingqing/zhaoqingqing.github.io.git.
To git@github.com:zhaoqingqing/zhaoqingqing.github.io.git
 + 6bbdb6a...7af9afd HEAD -> master (forced update)
INFO  Deploy done: git
```

## 多台电脑同步

针对于已有hexo + gitpages的用户，并且和我一样使用两个分支的同学，建议在另一台电脑上**同步而不用重新搭建**

### 同步步骤

1. git clone 已有project，比如我的存放在 F:\Document\zhaoqingqing.github.io
2. git cmd 进入拉到存放的目录 ，执行 `npm instal hexo`，此过程时间可能稍久
3. 执行 `npm install` 安装package.json中的modules，此过程时间较久
4. `npm install hexo-deployer-git` ，安装布署组件
5. 同步完成后，在 F:\Document\zhaoqingqing.github.io\node_modules下可以看到新安装的库

```powershell
cd f:
cd Document

git clone git@github.com:zhaoqingqing/zhaoqingqing.github.io.git

F:\Document\zhaoqingqing.github.io>npm install hexo
npm WARN optional dep failed, continuing fsevents@1.0.14

F:\Document\zhaoqingqing.github.io>npm install
-- hexo-generator-feed@1.2.0

F:\Document\zhaoqingqing.github.io>npm install hexo-deployer-git
npm WARN optional dep failed, continuing fsevents@1.0.14

F:\Document\zhaoqingqing.github.io>npm install hexo-deployer-git --save
```

注意事项

不要执行`F:\Document\zhaoqingqing.github.io>hexo init` 指令

执行之后，就会初始化blog

### 同步失败

如果你根据上述步骤同步失败的话，请尝试下面的方法：

1. 重新安装hexo
2. 重新执行上述的**同步步骤**

## 遇到的问题

### hexo调试

当你在使用hexo指令时，假如遇到错误而闪退时，也许你会在bat中加上 pause阻止闪退，但我在win7和win10上测试无效。

为解决看不到log而烦恼，请在 git cmd 中执行hexo指令，git cmd会打印错误log而不会自动退出。

### hexo 不是内部命令

如果在某台电脑上安装成功之后，在另外电脑上同步遇到此问题，可以尝试以下方法

尝试以下方法：

- 添加hexo的安装路径到系统的环境变量
- 重新安装nodejs，选择旧一些的版本
- 在hexo的github上寻找答案或帮助

### 文章永久链接Permalinks

doc: https://hexo.io/zh-cn/docs/permalinks.html

**相关issues**

https://github.com/hexojs/hexo/issues/1162

https://github.com/hexojs/hexo/issues/799

**示例**

```yaml
# URL
permalink:year/:month/:day/:title/	2013/07/14/hello-world
permalink:year-:month-:day-:title.html	2013-07-14-hello-world.html
permalink:category/:title	foo/bar/hello-world
permalink:year/:month/:day/:id.html	2013/07/14/circ225fb0001uotwthqx55bq.html
```

**我的测试**

使用id来作为文件的永久链接的话，id共25个字符

经实验使用 `hexo clean` 之后，重新启动服务`hexo server `，同一篇文章的ID会发生改变

```powershell
hello world
circ2g76j0000wwtwu6fyocer
circ2is670000uotwrtfgyw0w
```

### Github提交错误

错误码：`Error: Permission denied (publickey)`

**参考资料**

http://www.cnblogs.com/amaoxiaozhu/p/3319233.html

**解决办法**

添加了此环境变量

变量名：GIT_SSH

变量值： C:\Program Files\TortoiseGit\bin\TortoisePLink.exe

1、cd到ssh-agent.exe所在目录C:\Program Files\Git\usr\bin>

```powershell
C:\Program Files\Git\usr\bin>ssh-keygen -t rsa -C "zhaoqignqing@gmail.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/c/Users/qingqing/.ssh/id_rsa):
```

接着按回车，如果要设定密码，请输入要设定的密码

重点：ssh-keygen -t rsa -C "**你的Git对应邮箱地址**"

2、打开C:\Users\qingqing\.ssh\id_rsa.pub

复制**id_rsa.pub**所有内容，在github的设置中添加新的SSH Keys，

title：自定义

key: **id_rsa.pub**的所有内容

3、输入 ssh -vT git@github.com 进行测试

```powershell
C:\Program Files\Git\usr\bin>ssh -vT git@github.com
OpenSSH_7.1p2, OpenSSL 1.0.2g  1 Mar 2016
debug1: Reading configuration data /etc/ssh/ssh_config
debug1: Connecting to github.com [192.30.253.112] port 22.
debug1: Connection established.
debug1: identity file /c/Users/qingqing/.ssh/id_rsa type 1
debug1: key_load_public: No such file or directory
debug1: identity file /c/Users/qingqing/.ssh/id_rsa-cert type -1
debug1: key_load_public: No such file or directory
debug1: identity file /c/Users/qingqing/.ssh/id_dsa type -1
debug1: key_load_public: No such file or directory
debug1: identity file /c/Users/qingqing/.ssh/id_dsa-cert type -1
debug1: key_load_public: No such file or directory
debug1: identity file /c/Users/qingqing/.ssh/id_ecdsa type -1
debug1: key_load_public: No such file or directory
debug1: identity file /c/Users/qingqing/.ssh/id_ecdsa-cert type -1
debug1: key_load_public: No such file or directory
debug1: identity file /c/Users/qingqing/.ssh/id_ed25519 type -1
debug1: key_load_public: No such file or directory
debug1: identity file /c/Users/qingqing/.ssh/id_ed25519-cert type -1
debug1: Enabling compatibility mode for protocol 2.0
debug1: Local version string SSH-2.0-OpenSSH_7.1
debug1: Remote protocol version 2.0, remote software version libssh-0.7.0
debug1: no match: libssh-0.7.0
debug1: Authenticating to github.com:22 as 'git'
debug1: SSH2_MSG_KEXINIT sent
debug1: SSH2_MSG_KEXINIT received
debug1: kex: server->client chacha20-poly1305@openssh.com <implicit> none
debug1: kex: client->server chacha20-poly1305@openssh.com <implicit> none
debug1: expecting SSH2_MSG_KEX_ECDH_REPLY
debug1: Server host key: ssh-rsa SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8
debug1: Host 'github.com' is known and matches the RSA host key.
debug1: Found key in /c/Users/qingqing/.ssh/known_hosts:1
debug1: SSH2_MSG_NEWKEYS sent
debug1: expecting SSH2_MSG_NEWKEYS
debug1: SSH2_MSG_NEWKEYS received
debug1: SSH2_MSG_SERVICE_REQUEST sent
debug1: SSH2_MSG_SERVICE_ACCEPT received
debug1: Authentications that can continue: publickey
debug1: Next authentication method: publickey
debug1: Offering RSA public key: /c/Users/qingqing/.ssh/id_rsa
debug1: Server accepts key: pkalg ssh-rsa blen 279
Enter passphrase for key '/c/Users/qingqing/.ssh/id_rsa':
debug1: Authentication succeeded (publickey).
Authenticated to github.com ([192.30.253.112]:22).
debug1: channel 0: new [client-session]
debug1: Entering interactive session.
debug1: client_input_channel_req: channel 0 rtype exit-status reply 0
Hi zhaoqingqing! You've successfully authenticated, but GitHub does not provide shell access.
debug1: channel 0: free: client-session, nchannels 1
Transferred: sent 3080, received 1776 bytes, in 0.5 seconds
Bytes per second: sent 5933.2, received 3421.2
debug1: Exit status 1
```

`Hi zhaoqingqing! You've successfully authenticated`重点日志，输出此就表示你成功了。

### 启动hexo服务失败

公司电脑windows 7 x64，安装hexo成功之后，启动hexo server或 hexo generate服务失败

根据错误提示安装缺少的库，以下是我缺少的库，请根据出错提示安装。

> npm install hexo-deployer-git --save
>
> npm install --save object-assign
>
> npm install --save connect
>
> npm install --save hexo-server



### deploy失败

在公司的电脑上同步hexo 博客，本地正常布署，但提交到git时，报以下错误 `Host key verification failed.`

```powershell

nothing to commit, working directory clean
Host key verification failed.
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
FATAL Something's wrong. Maybe you can find the solution here: http://hexo.io/docs/troubleshooting.html
Error: Host key verification failed.
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.

    at ChildProcess.<anonymous> (E:\Code\zhaoqingqing.github.io\node_modules\hexo-deployer-git\node_modules\hexo-util\lib\spawn.js:37:17)
.......
FATAL Host key verification failed.
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.

Error: Host key verification failed.
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.

    at ChildProcess.<anonymous> (E:\Code\zhaoqingqing.github.io\node_modules\hexo-deployer-git\node_modules\hexo-util\lib\spawn.js:37:17)
	.......

E:\Code\zhaoqingqing.github.io>
```

## next主题定制

### Pisces主题

Pisces主题的宽度修改：https://github.com/iissnan/hexo-theme-next/issues/759#issuecomment-202242848

步骤

1. 打开 themes\source\css\_schemes\Pisces\_layout.styl

2. 修改内容如下

   ```css
   .header{ width: 80%; }                            /*默认在4行*/
   .container .main-inner { width: 80%; }           /*默认在47行*/
   .content-wrap { width: calc(100% - 260px); }    /*默认在61行*/
   ```

3. 注意事项：.content-wrap里的width后的 -260px不可设置过大否则左则栏和主体间隙很大。

4. 修改之后，请执行` hexo clean `，再重新生成。

### 第三方服务

next主题集成第三方服务文档：http://theme-next.iissnan.com/third-party-services.html

腾讯统计：http://v2.ta.qq.com/bind/site

JiaThis分享：http://www.jiathis.com/ (Adb可能会过滤掉)

## 我的总结

### 需要上传的文件
_config.yml，theme/，source/，scaffolds/，package.json，.gitignore 需要上传到hexo分支

### 无需上传的文件

.git/，node_modules/，public/，.deploy_git/，db.json 文件无需上传至hexo分支

### 安装其它组件

安装其他的一些必要组件，如果在node_modules里面有的，就不要重复安装了：

- 为了使用hexo d来部署到git上，需要安装
  `npm install hexo-deployer-git --save`
- 为了建立RSS订阅，需要安装
  `npm install hexo-generator-feed --save`
- 为了建立站点地图，需要安装
  `npm install hexo-generator-sitemap --save`

插件安装后，有的需要对配置文件_config.yml进行配置，具体怎么配置，可以参考上面插件在github主页上的具体说明

作者：skycrown

链接：https://www.zhihu.com/question/21193762/answer/103097754



### github sshkey

目录：C:\Users\qingqing\\.ssh

SSH key密钥对

| 文件名         | 说明                         |      |
| ----------- | -------------------------- | ---- |
| id_rsa      | 私钥，千万不要泄露出去                |      |
| id_rsa.pub  | 公钥，可以放心给别人，添加到git的ssh keys |      |
| known_hosts |                            |      |

## TODO

hexo-theme-next第三方服务集成 http://theme-next.iissnan.com/third-party-services.html

- [x] 评论插件
- [ ] 分享插件
- [ ] 搜索服务
- [x] //scheme: Pisces 修改宽度 https://github.com/iissnan/hexo-theme-next/issues/759#issuecomment-202242848
- [ ] 文章目录自动展开
- [ ] 进阶技巧

## 待读资料

关联 GitHub, 让 Hexo 支持查看文章更新历史: http://moxfive.xyz/2016/01/10/hexo-post-version-control/

Hexo搭建Github-Pages博客填坑教程: http://www.jianshu.com/p/35e197cb1273