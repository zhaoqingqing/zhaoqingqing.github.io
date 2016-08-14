

## 页游多平台/多渠道

### 思路:渠道参数 对接渠道SDK

### 充值回调链接

以49you的充值回调为例

```html
http://{0}/default/weblogin/RequestPay.php?roleid={1}
```

拼接请求url：http://218.15.113.133/default/weblogin/RequestPay.php?roleid=212

服务器返回json

```json
{"ret":0,"url":"http:\/\/pay.49you.com\/?gid=161&scode=0&info=eyJyb2xlaWQiOjIxMn0="}
```

解析之后：http://pay.49you.com/?gid=161&scode=0&info=eyJyb2xlaWQiOjExMTB9

### 腾讯广点通

接入QQ用户的玩家登录，黄钻信息，VIP信息，充值信息

### 四九游

接入登录，充值信息



## 功能模块

### TODO

快速完成法

ylwd中写url

launcher/fileupdate + config.json

### LogicModule

微端的逻辑功能抽取在此模块，目前已处理的有

- Launcher
- AssistApp

待处理
- FileUpdate


### json库

Newtonsoft.Json free：http://www.newtonsoft.com/json/help/html/Introduction.htm

**可能遇到的错误**

错误代码：4  描述：缺少编译器要求的成员“System.Runtime.CompilerServices.ExtensionAttribute..ctor”	

这个问题相信大多数 .neter 都遇到过， 处理方法一般是删除“Newtonsoft.dll”， 再重新引用。

但这个动作做得多了， 还是很烦，可以在 JsonHelper (或其它引用到 Newtonsoft.dll 的类)中添加：

```csharp
//缺少编译器要求的成员“ystem.Runtime.CompilerServices.ExtensionAttribute..ctor”  
namespace System.Runtime.CompilerServices  
{  
    public class ExtensionAttribute : Attribute { }  
} 
```
### 参考资料

http://www.programgo.com/article/66182300875/