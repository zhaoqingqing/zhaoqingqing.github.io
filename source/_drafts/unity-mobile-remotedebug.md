---
title: Unity3D手机远程调试-Hdg Remote Debug
---

[http://www.maosongliang.com/archives/219](http://www.maosongliang.com/archives/219)



Assetstore：[https://www.assetstore.unity3d.com/cn/#!/content/61863](https://www.assetstore.unity3d.com/cn/#!/content/61863)

文档？[http://www.horsedrawngames.com/hdg-remote-debug-more-info/](http://www.horsedrawngames.com/hdg-remote-debug-more-info/)

### 遇到的问题

unity5.3.5 f1

```c#
rdtClientEnumerateServers: Exception System.Net.Sockets.SocketException: 通常每个套接字地址(协议/网络地址/端口)只允许使用一次。

  at System.Net.Sockets.Socket.Bind (System.Net.EndPoint local_end) [0x00000] in <filename unknown>:0 
  at System.Net.Sockets.UdpClient.InitSocket (System.Net.EndPoint localEP) [0x00000] in <filename unknown>:0 
  at System.Net.Sockets.UdpClient..ctor (System.Net.IPEndPoint localEP) [0x00000] in <filename unknown>:0 
  at Hdg.rdtClientEnumerateServers.StartHelloListening () [0x00028] in D:\Git\UnityRemoteDebugger\Assets\RemoteDebugger\Editor\rdtClientEnumerateServers.cs:80    at System.Net.Sockets.Socket.Bind (System.Net.EndPoint local_end) [0x00000] in <filename unknown>:0 
  at System.Net.Sockets.UdpClient.InitSocket (System.Net.EndPoint localEP) [0x00000] in <filename unknown>:0 
  at System.Net.Sockets.UdpClient..ctor (System.Net.IPEndPoint localEP) [0x00000] in <filename unknown>:0 
  at Hdg.rdtClientEnumerateServers.StartHelloListening () [0x00028] in D:\Git\UnityRemoteDebugger\Assets\RemoteDebugger\Editor\rdtClientEnumerateServers.cs:80 
UnityEngine.Debug:LogError(Object)
Hdg.rdtDebug:Log(Object, LogLevel, String, Object[]) (at Assets/RemoteDebugger/rdtDebug.cs:74)
Hdg.rdtDebug:Log(Object, Exception, LogLevel, String, Object[]) (at Assets/RemoteDebugger/rdtDebug.cs:90)
Hdg.rdtDebug:Error(Object, Exception, String, Object[]) (at Assets/RemoteDebugger/rdtDebug.cs:34)
Hdg.rdtClientEnumerateServers:StartHelloListening() (at Assets/RemoteDebugger/Editor/rdtClientEnumerateServers.cs:85)
Hdg.rdtClientEnumerateServers:.ctor() (at Assets/RemoteDebugger/Editor/rdtClientEnumerateServers.cs:22)
Hdg.ConnectionWindow:Update() (at Assets/RemoteDebugger/Editor/ConnectionWindow.cs:645)
UnityEditor.EditorApplication:Internal_CallUpdateFunctions()
```