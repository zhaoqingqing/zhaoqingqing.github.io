## 脚本执行顺序

### 前言

搭建一个示例来验证Unity脚本的执行顺序，大概测试以下部分：

- 物理方面(Physics)
- 渲染(Scene rendering)
- 输入事件(InputEvent)

### 流程图

Unity文档：https://docs.unity3d.com/Manual/ExecutionOrder.html

原图地址：https://docs.unity3d.com/uploads/Main/monobehaviour_flowchart.svg

![](http://images2015.cnblogs.com/blog/363476/201610/363476-20161023170129935-1286135638.jpg)



## 测试场景

### 搭建场景

示例下载：https://github.com/zhaoqingqing/blog_samplecode/raw/master/unity-framework/monobehaviour_lifecycle/monobehaviour_lifecycle.unitypackage

根据上图中的脚本生命周期，我编写三个脚本来测试部分阶段的生命周期：

- Logs.cs 输出日志(可选)
- TestInputEvent.cs(Input输入事件)
- TestPhysicOrder.cs(物理事件执行顺序)
- TestSceneRender.cs(Render顺序)

创建一个空的场景，创建三个Gameobject，每个gameobject上分别绑上要测试的脚本。每次测试不同的功能，分别激活不同的gameobject

![](http://images2015.cnblogs.com/blog/363476/201610/363476-20161024202446031-995341207.png)

### 打印调用堆栈脚本

可以打印调用方法的堆栈，包括方法名，文件名

github:https://github.com/zhaoqingqing/blog_samplecode/blob/master/unity-framework/monobehaviour_lifecycle/Logs.cs

```c#
using System;
using System.Diagnostics;

public class Logs  {

    /// <summary>
    /// 打印调用者的方法名
    /// </summary>
    public static void DoLog()
    {
        StackTrace st = new StackTrace(true);
        //获取当前调用的方法名
        StackFrame stackFrame = st.GetFrame(1);
        //var callInfo = string.Format("{0}:{1}.{2}",stackFrame.GetFileName(),stackFrame.GetFileLineNumber(),stackFrame.GetMethod().Name);
        var callInfo = stackFrame.GetMethod().Name.ToString();
        DoLog(callInfo);
    }

    public static void DoLog(string szMsg, params object[] args)
    {
        string log = string.Format("[{0}]{1}", DateTime.Now.ToString("HH:mm:ss.ffff"), string.Format(szMsg, args));
        UnityEngine.Debug.Log(log);
    }
}
```

## 物理测试

### 测试脚本

测试脚本中写了Unity的各个脚本函数，大致内容如下：

github:https://github.com/zhaoqingqing/blog_samplecode/blob/master/unity-framework/monobehaviour_lifecycle/TestPhysicOrder.cs

```c#
using System;
using UnityEngine;
using System.Collections;

public class TestPhysicOrder : MonoBehaviour
{
    // Reset to default values
    public void Reset()
    {
        Logs.DoLog();
    }

    // Awake is called when the script instance is being loaded
    public void Awake()
    {
        StartCoroutine(YieldOneFrame());
        StartCoroutine(YieldEndOfFrame());
        StartCoroutine(YieldWaitForFixedUpdate());
        Logs.DoLog();
    }

    // This function is called when the object becomes enabled and active
    public void OnEnable()
    {
        Logs.DoLog();
    }

    // Use this for initialization
    void Start()
    {
        Logs.DoLog();
    }

    // This function is called every fixed framerate frame, if the MonoBehaviour is enabled
    public void FixedUpdate()
    {
        Logs.DoLog();
    }

    // Update is called once per frame
    void Update()
    {
        Logs.DoLog();
    }

    IEnumerator YieldWaitForFixedUpdate()
    {
        yield return new WaitForFixedUpdate();
        Logs.DoLog("WaitForFixedUpdate");
    }

    IEnumerator YieldOneFrame()
    {
        yield return 1;
        Logs.DoLog("YieldOneFrame");
    }

    IEnumerator YieldEndOfFrame()
    {
        yield return new WaitForEndOfFrame();
        Logs.DoLog("YieldEndOfFrame");
    }

    // LateUpdate is called every frame, if the Behaviour is enabled
    public void LateUpdate()
    {
        Logs.DoLog();
    }

    // This function is called when the behaviour becomes disabled or inactive
    public void OnDisable()
    {
        Logs.DoLog();
    }

    // This function is called when the MonoBehaviour will be destroyed
    public void OnDestroy()
    {
        Logs.DoLog();
    }

    // Sent to all game objects when the player gets or looses focus
    public void OnApplicationFocus(bool focus)
    {
        Logs.DoLog();
    }

    // Sent to all game objects when the player pauses
    public void OnApplicationPause(bool pause)
    {
        Logs.DoLog();
    }

    // Sent to all game objects before the application is quit
    public void OnApplicationQuit()
    {
        Logs.DoLog();
    }

}
```

### 测试结果
开始部分截图
![](http://images2015.cnblogs.com/blog/363476/201610/363476-20161023170959576-1011727270.png)

结束部分截图
![](http://images2015.cnblogs.com/blog/363476/201610/363476-20161023171024295-1469828355.png)

## Render测试

### 测试脚本

github:https://github.com/zhaoqingqing/blog_samplecode/blob/master/unity-framework/monobehaviour_lifecycle/TestSceneRender.cs

```c#
using System;
using UnityEngine;
public class TestSceneRender : MonoBehaviour
{

    // OnPreCull is called before a camera culls the scene
    public void OnPreCull()
    {
        Logs.DoLog();
    }

    // OnPreRender is called before a camera starts rendering the scene
    public void OnPreRender()
    {
        Logs.DoLog();

    }

    // Callback that is sent if an associated RectTransform has it's dimensions changed
    public void OnRectTransformDimensionsChange()
    {
        Logs.DoLog();
    }

    // Callback that is sent if an associated RectTransform is removed
    public void OnRectTransformRemoved()
    {
        Logs.DoLog();
    }

    // OnRenderImage is called after all rendering is complete to render image
    public void OnRenderImage(RenderTexture source, RenderTexture destination)
    {
        Logs.DoLog();
    }

    // OnRenderObject is called after camera has rendered the scene
    public void OnRenderObject()
    {
        Logs.DoLog();
    }


    // OnWillRenderObject is called once for each camera if the object is visible
    public void OnWillRenderObject()
    {
        Logs.DoLog();
    }

    // Implement this OnDrawGizmosSelected if you want to draw gizmos only if the object is selected
    public void OnDrawGizmos()
    {
        Logs.DoLog();
    }

    // OnGUI is called for rendering and handling GUI events
    public void OnGUI()
    {
        Logs.DoLog();
    }
}
```

### 测试结果

当把测试脚挂在Cube或者Camera上，会执行的函数是不相同的。

**绑在Camera上**

![](http://images2015.cnblogs.com/blog/363476/201610/363476-20161024202616250-643958843.png)

**绑在Cube上**

![](http://images2015.cnblogs.com/blog/363476/201610/363476-20161024202647625-2041354129.png)