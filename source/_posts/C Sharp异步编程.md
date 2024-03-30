---
title: C Sharp异步编程
date: 2023-09-26
tags: 
- CSharp
categories: 
- CSharp
---

## Async和Await的概念
- async: 将方法标记为异步方法，表示该方法包含异步操作
- await: 用于等待一个异步操作完成，然后继续执行下面的代码，只能在async方法内部使用

## 适用场景
- IO密集型操作：如文件读写、网络请求、数据库查询等
- GUI应用程序：阻塞主线程可能会导致用户界面卡顿，使用异步变成可以保持界面的响应性
- 服务器应用程序：需要同时处理多个客户端请求，使用异步编程可以提高服务器的并发性能

## 编码规范
- 命名异步方法时，可以在方法名后面加上Async后缀，以明确表示它是一个异步方法，例如DownloadDataAsync。
- 异步编程并不是适用于所有情况的解决方案。在某些情况下，同步操作可能更简单、更易于理解。只有在需要提高并发性和响应性的情况下，才应该使用异步。
- 在异步方法内部，不要使用阻塞的同步方法，这会导致整个异步操作的效果减弱。应该尽量使用支持异步操作的方法。
### 示例
```c#
using System;
using System.Net.Http;
using System.Threading.Tasks;

class Program
{
    static async Task Main(string[] args)
    {
        await DownloadWebsiteAsync();
        Console.WriteLine("下载完成！");
    }

    static async Task DownloadWebsiteAsync()
    {
        using (HttpClient client = new HttpClient())
        {
            string website = "https://www.example.com";
            string content = await client.GetStringAsync(website);
            Console.WriteLine("下载内容长度：" + content.Length);
        }
    }
}
```
在上述示例中，Main方法和DownloadWebsiteAsync方法都被标记为async，在DownloadWebsiteAsync方法内部，通过await等待GetStringAsync方法的异步操作完成。

## Task 和 Task\<T>的创建
### 创建Task
1. 使用构造函数
```c#
Task task = new Task(() =>
{
    // Code.
});
```
2. 使用Run()
```c#
Task task = Task.Run(() =>
{
    // Code.
});
```
### 创建Task\<T>
1. 使用构造函数
```c#
Task<int> task = new Task<int>(() =>
{
    // Code, return Int.
    return 2333;
});
```
2. 使用Run()
```c#
Task<int> task = Task.Run(() =>
{
    // Code, return Int.
    return 2333;
});
```

## 启用和等待
### 启动
需要启动一个异步任务，可以直接调用start方法
```c#
task.Start();
```
### await
使用`await`关键字来等待任务的完成
```c#
await task; // 等待任务完成
int result = await task; // 等待任务完成，并获取结果
```
### Wait
使用`Wait`阻塞等待，但需要谨慎使用，避免造成死锁
```c#
task.Wait(); //阻塞当前线程，等待任务完成
int result = task.Result; //阻塞当前线程，等待任务完成，并获取结果
```

## 异步任务状态和异常处理
### 异步任务状态
- TaskStatus.Created：任务已创建，但尚未启动
- TaskStatus.WaitingForActivation：任务正在等待激活
- TaskStatus.WaitingToRun：任务正在等待执行
- TaskStatus.Running：任务正在执行
- TaskStatus.RanToCompletion：任务已成功完成
- TaskStatus.Canceled：任务已被取消
- TaskStatus.Faulted：任务由于异常而失败
### 异常处理
```c#
try
{
    await Task.Run(() =>
    {
        // Code.
    });
}
catch (Exception ex)
{
    Console.WriteLine($"发生异常：{ex.Message}");
}
```

## 并行多任务执行
### Task.WhenAll
Task.WhenAll 方法接受一个 Task 数组，当数组中的所有任务都完成时，返回一个新的任务
```c#
Task[] tasks = new Task[]
{
    Task.Run(() => DoTask1()),
    Task.Run(() => DoTask2()),
    Task.Run(() => DoTask3())
};

await Task.WhenAll(tasks); // 等待所有任务完成
```
### Task.WhenAny
Task.WhenAny 方法接受一个 Task 数组，返回一个 Task，该任务在数组中的任何一个任务完成时就会完成
```c#
Task<string>[] tasks = new Task<string>[]
{
    Task.Run(() => DoTask1()),
    Task.Run(() => DoTask2()),
    Task.Run(() => DoTask3())
};

Task<string> completedTask = await Task.WhenAny(tasks); // 等待任何一个任务完成
string result = completedTask.Result; // 获取完成的任务的结果
```

## 取消任务
### 使用CancellationToken
CancellationToken是一个用于传递取消请求的标记，可以在任务的异步操作中检查 CancellationToken 是否已被触发，如果是则取消任务。
```c#
CancellationTokenSource cancellationTokenSource = new CancellationTokenSource();
CancellationToken cancellationToken = cancellationTokenSource.Token;

Task task = Task.Run(() =>
{
    while (!cancellationToken.IsCancellationRequested)
    {
        // Code.
    }
}, cancellationToken);

cancellationTokenSource.Cancel(); // 取消任务
```

## 使用Task\<T>返回异步操作的结果
```c#
Task<int> CalculateAsync()
{
    return Task.Run(() =>
    {
        int result = 0;
        // 计算操作的代码
        return result;
    });
}

int result = await CalculateAsync(); // 等待操作完成，并获取结果
```

## 异步方法的嵌套调用
在异步方法中调用另一个异步方法是很常见的，但不会导致阻塞，调用链中的每个异步方法都会按照异步的方式执行。
```c#
public async Task OuterMethodAsync()
{
    Console.WriteLine("开始外部方法");
    await InnerMethodAsync();
    Console.WriteLine("结束外部方法");
}

public async Task InnerMethodAsync()
{
    Console.WriteLine("开始内部方法");
    await Task.Delay(233); // 模拟异步操作
    Console.WriteLine("结束内部方法");
}
```

## 注意
- 在异步编程中，避免使用 Wait、Result 等方法来阻塞线程，使用 await 来异步等待任务的完成。
- 在异步编程中，异常的处理方式与同步代码类似。使用 try-catch 块来捕获和处理异常，确保程序的稳定性。
- 在使用 CancellationTokenSource 创建取消标记时，要确保在不再需要时关闭取消标记，以防止资源泄漏。
```c#
cancellationTokenSource.Dispose(); // 关闭取消标记
```