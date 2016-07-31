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

#### 命令举例

```powershell
npm install hexo-cli -g
npm install hexo --save

npm install -g hexo-cli --registry=https://registry.npm.taobao.org 

hexo -v
hexo init blog

echo. 安装成功后的路径：
C:\Users\qingqing\AppData\Roaming\npm\node_modules\hexo\bin
f:\blog\node_modules\hexo\bin\
```

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