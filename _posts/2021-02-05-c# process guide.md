---
layout: post
title: c# 如何使用 Process 类执行程序，打开文件/文件夹/http地址
categories: c#
tags: [process]
---

* content
{:toc}

# 前言

Process 类主要有两个用途，一是当个uri启动器，直接打开相关联的软件。二是执行程序并监视其输出

# 1. uri启动器

这里简单介绍下什么是uri，注意，我本人没学过这玩意，所以我只能说说个人理解

你可以把他看成一个地址协议定义规则，具体规则如下

```
xxx:yyy

xxx是协议名
yyy是协议内容 或者称 协议参数
```

常见的uri地址有下面几种

| 名称 | 协议示例 |
| ----- | ----- |
| http | https://www.cnblogs.com/ |
| mailto | mailto:kljzndx@outlook.com |
| file | file:///d:/xxx/yyy/zzz.exe |


ok，介绍完了，开始正文

想启动一个uri地址还是比较方便的

简单方法

``` c#
Process.Start("explorer.exe", "uri").Close();
```

高级方法

``` c#
using (var process = new Process())
{
    process.StartInfo.FileName = "uri";
    process.StartInfo.UseShellExecute = true;
    process.Start();
}
```

# 2. 执行程序并监视其输出

这个就有点复杂了，不像上面几行代码就成了，这次有三部分代码

首先是配置方法

``` c#
private static Process _exeProcess;

private static void SetUpExeProcess(string exeName, string args)
{
    if (_exeProcess != null)
        return;

    _exeProcess = new Process();
    _exeProcess.StartInfo.FileName = exeName;
    _exeProcess.StartInfo.Arguments = args;

    _exeProcess.StartInfo.CreateNoWindow = true;  // 不显示窗口
    _exeProcess.StartInfo.WorkingDirectory = "";  // 设置工作目录
    _exeProcess.StartInfo.Environment.Add("key", "value");  // 添加环境变量

    // 是否使用 explorer.exe 执行命令。
    // 如需监听输出，请设置为false，原因如下：
    // 若为true，那么就基本等同下面两行代码
    // _exeProcess.StartInfo.FileName = "explorer.exe"
    // _exeProcess.StartInfo.Arguments = exeName;
    _exeProcess.StartInfo.UseShellExecute = false;

    // 启用标准输出，异常输出监听支持
    _exeProcess.StartInfo.RedirectStandardOutput = true;
    _exeProcess.StartInfo.RedirectStandardError = true;

    // 启用 Exited 事件支持
    _exeProcess.EnableRaisingEvents = true;

    _exeProcess.OutputDataReceived += ExeProcess_OutputDataReceived;
    _exeProcess.ErrorDataReceived += ExeProcess_ErrorDataReceived;
    _exeProcess.Exited += ExeProcess_Exited;

    // {
        // 启用标准输入流支持
        // _exeProcess.StartInfo.RedirectStandardInput = true;

        // 标准输入流的写入方法
        // _exeProcess.StandardInput.Write("");
        // _exeProcess.StandardInput.WriteLine("");
    // }
}

private static void ExeProcess_OutputDataReceived(object sender, DataReceivedEventArgs e)
{
    Console.WriteLine(DateTime.Now.ToShortTimeString() + "\t" + e.Data);
}

private static void ExeProcess_ErrorDataReceived(object sender, DataReceivedEventArgs e)
{
    Console.WriteLine(DateTime.Now.ToShortTimeString() + "\t" + e.Data);
}
```

> 可能有细心的读者会发现少了一个退出事件的处理方法，放心，在后面放着呢

接着是启动

``` c#
private static void StartExeProcess()
{
    if (_exeProcess == null)
        return;

    _exeProcess.Start();
    
    // 开启消息监听
    _exeProcess.BeginOutputReadLine();
    _exeProcess.BeginErrorReadLine();
}
```

最后是停止

``` c#
private static void StopExeProcess()
{
    if (_exeProcess == null)
        return;

    // 立即关闭关联进程
    _exeProcess.Kill();

    //// 如果要关闭的是gui程序，最好用下面这个方法
    // _exeProcess.CloseMainWindow();
}

private static void ExeProcess_Exited(object sender, EventArgs e)
{
    // 取消消息监听
    _exeProcess.CancelOutputRead();
    _exeProcess.CancelErrorRead();

    _exeProcess.OutputDataReceived -= ExeProcess_OutputDataReceived;
    _exeProcess.ErrorDataReceived -= ExeProcess_ErrorDataReceived;
    _exeProcess.Exited -= ExeProcess_Exited;

    _exeProcess.Close();
    _exeProcess = null;
}
```