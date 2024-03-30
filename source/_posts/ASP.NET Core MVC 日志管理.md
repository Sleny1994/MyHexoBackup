---
title: ASP.NET Core MVC 日志管理
date: 2023-08-24
tags:
- CSharp
- NET
categories:
- CSharp
---

## 安装NLog组件库
- NLog
- NLog.Web.AspNetCore

## 添加配置文件
```xml
<!--nlog.config--> 
<?xml version="1.0" encoding="utf-8" ?>
<nlog xmlns="http://www.nlog-project.org/schemas/NLog.xsd"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      autoReload="true"
      internalLogLevel="Info"
      internalLogFile="c:\temp\internal-nlog-AspNetCore.txt">

    <!-- enable asp.net core layout renderers -->
    <extensions>
        <add assembly="NLog.Web.AspNetCore"/>
    </extensions>

    <!-- the targets to write to -->
    <targets>
        <!-- File Target for all log messages with basic details -->
        <target xsi:type="File" name="allfile" fileName="\%TEMP%\nlog-AspNetCore-all-${shortdate}.log"
                layout="${longdate}|${event-properties:item=EventId:whenEmpty=0}|${level:uppercase=true}|${logger}|${message} ${exception:format=tostring}" />

        <!-- File Target for own log messages with extra web details using some ASP.NET core renderers -->
        <target xsi:type="File" name="ownFile-web" fileName="c:\temp\nlog-AspNetCore-own-${shortdate}.log"
                layout="${longdate}|${event-properties:item=EventId:whenEmpty=0}|${level:uppercase=true}|${logger}|${message} ${exception:format=tostring}|url: ${aspnet-request-url}|action: ${aspnet-mvc-action}" />

        <!--Console Target for hosting lifetime messages to improve Docker / Visual Studio startup detection -->
        <target xsi:type="Console" name="lifetimeConsole" layout="${MicrosoftConsoleLayout}" />
    </targets>

    <!-- rules to map from logger name to target -->
    <rules>
        <!--All logs, including from Microsoft-->
        <logger name="*" minlevel="Trace" writeTo="allfile" />

        <!--Output hosting lifetime messages to console target for faster startup detection -->
        <logger name="Microsoft.Hosting.Lifetime" minlevel="Info" writeTo="lifetimeConsole, ownFile-web" final="true" />

        <!--Skip non-critical Microsoft logs and so log only own logs (BlackHole) -->
        <logger name="Microsoft.*" maxlevel="Info" final="true" />
        <logger name="System.Net.Http.*" maxlevel="Info" final="true" />

        <logger name="*" minlevel="Trace" writeTo="ownFile-web" />
    </rules>
</nlog>
```
[相关详细说明，可以点击该链接：https://github.com/NLog/NLog/wiki/Configuration-file](https://github.com/NLog/NLog/wiki/Configuration-file)

## 注入日志服务
```c#
// Program.cs
// NLog: Setup NLog for Dependency injection
builder.Logging.ClearProviders();
builder.Host.UseNLog();
```

## 日志过滤器
在创建项目时，会默认在appsettings.json下创建日志配置。
```json
// appsettings.json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

## 输出日志
在控制器中输出日志，需要用到ILogger<T>接口，可通过注入的方式实现。
```c#
using MvcDemo.Models;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using System.Diagnostics;

namespace MvcDemo.Controllers
{
    public class HomeController : Controller
    {
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger)
        {
            _logger = logger;
        }

        public IActionResult Index()
        {
            _logger.LogInformation("Hello, This is Index！");
            return View();
        }
    }
}
```

## 日志查看
查看日志，通过打开日志配置文件nlog.config中配置的路径，会生成两个对应的日志（\%TEMP%\nlog-AspNetCore-all-${shortdate}.log）
- nlog-AspNetCore-all-${shortdate}.log中的日志比较全，包含很多程序内部的Trace、Debug日志
- nlog-AspNetCore-own-${shortdate}.log中的日志比较简单

## 日志级别
- Trace|0|LogTrace：包含最详细的消息，这些消息可能包含敏感的应用数据，默认情况下是禁用状态，且不应在生产环境下启用
- Debug|1|LogDebug：用于调式和开发，由于量大，请在生产中小心使用
- Information|2|LogInformation：跟踪应用的常规流，可能具有长期值
- Warning|3|LogWarning：对于异常事件或意外事件，通常包括不会导致应用失败的错误或情况
- Error|4|LogError：表示无法处理的错误和异常，这些消息表示当前操作或请求失败，而不是整个应用失败
- Critical|5|LogCritical：需要立即关注的失败，例如数据丢失、磁盘空间不足等
- None|6|   ：指定日志记录类别不应写入消息

在上述中，LogLevel按严重性由低到高顺序列出。

在实际应用中，Trace日志不会输出，Debug仅在调试和开发时使用，可以通过参数配置修改相应的日志等级。
```xml
<!-- minlevel="Info"则不会输出Trace和Debug日志 -->
<!-- rules to map from logger name to target -->
<rules>
    <!--All logs, including from Microsoft-->
    <logger name="*" minlevel="Info" writeTo="allfile" />

    <!--Output hosting lifetime messages to console target for faster startup detection -->
    <logger name="Microsoft.Hosting.Lifetime" minlevel="Info" writeTo="lifetimeConsole, ownFile-web" final="true" />

    <!--Skip non-critical Microsoft logs and so log only own logs (BlackHole) -->
    <logger name="Microsoft.*" minlevel="Info" final="true" />
    <logger name="System.Net.Http.*" minlevel="Info" final="true" />

    <logger name="*" minlevel="Info" writeTo="ownFile-web" />
</rules>
```

## 日志输出的目标
日志不仅仅可以输出到控制台，也可以输出到文件或者其它媒介，输出目标可以通过targets\target进行配置，日志可以配置多个输出目标。
- name：输出目标名称
- type：输出目标类型，如：File、Database、Mail、Console，用`xsi:type="xxx"`来表示
- layout：输出格式
```xml
<target xsi:type="Console" name="lifetimeConsole" layout="${MicrosoftConsoleLayout}" />
```