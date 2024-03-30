---
title: Blazor Server项目结构
date: 2023-10-08
tags:
- CSharp
- NET
- Blazor
categories:
- CSharp
---

## 目录结构
[以默认Demo为例 - 即获取天气数据和计数功能]
```c#
Project
|
-Connected Services
|
-Properties
|
--launchSettings.json
|
-wwwroot
|
--css
--favicon.ico
|
-依赖项
|
-Data
|
--WeatherForecast.cs
--WeatherForecastService.cs
|
-Pages
|
--_Host.cshtml
--_Layout.cshtml
--Counter.razor
--Error.cshtml
---Error.cshtml.cs
--FetchData.razor
--Index.razor
|
-Shared
|
--MainLayout.razor
---MainLayout.razor.css
--NavMenu.razor
---NavMenu.razor.css
--SurveyPrompt.razor
|
-_Imports.razor
|
-App.razor
|
-appsettings.json
|
-Program.cs
```

## 目录说明
1. Data文件夹：包含WeatherForecast类和WeatherForecastService的实现，主要作用是向应用FeathData组件提供Demo天气数据
2. Pages文件夹：包含构成Blazor应用的各种路由组件和页面（.razor）和根页面，每个页面的路由是由页面顶部的@page指令来指定的。
    - _Host.cshtml: 实现Razor页面应用的根页面
    - _Layout.cshtml: 根页面的布局页，包含通用的HTML元素
        - 默认请求应用的任何页面，都会在响应中返回此页面进行呈现
        - 指定根App.razor的呈现位置
    - Counter.razor: 计数器页面
    - Error.razor: 当应用中发生未经处理的异常时调用
    - FetchData.razor: 数据列表页面
    - Index.razor: Demo创建的Blazor应用的默认首页
3. Shared文件夹：包含共享组件和样式表
    - MainLayout.razor: 应用的布局组件
    - MainLayout.razor.css: 应用主布局的样式表
    - NavMenu.razor: 侧边导航栏，包括NavLink组件，该组件可向其它Razor组件呈现导航菜单，其会在系统加载组件时自动指示选定状态，有助于了解当前选中菜单名称及所显示的页面
        - NavMenu.razor.css: 应用导航菜单的样式表
    - SurveyPrompt.razor: Blazor调查组件
4. wwwroot文件夹：存放静态文件的文件夹，包含应用程序的公共静态文件，一般包括网站使用的css样式表、图像和JavaScript文件
5. _Imports.razor：包括要包含在应用组件中常见的Razor指令，例如用于命名空间的@using指令
6. App.razor：是Blazor应用程序的根组件，使用Router组件来设置客户端路由，Router组件会截获浏览器所发出的请求并导航到相对应的地址页面
7. appsettings.json：环境应用设置文件
8. Program.cs：启动服务器的应用程序的入口，用于设置主机并包含应用的启动逻辑，包括配置应用程序服务和请求处理管道配置
    - 指定应用程序的依赖注入服务(DI)，如：通过调用AddServerSideBlazor添加服务，将WeatherForecastService添加到服务容器以供示例FetchData组件使用
    - 配置应用的请求处理管道，用于处理所有对应用程序的请求
        - 调用MapBlazorHub方法可以为浏览器的实时连接设置终结点，使用SignalR创建连接，用于向应用程序添加实时Web功能
        - 调用MapFallbackToPage("/_Host")以设置应用程序的根页面并启用导肮
9. BlazorAppDemo.csproj文件定义了应用程序项目及其依赖项，可以通过双击解决方案资源管理器中的BlazorAppDemo项目节点来查看
10. Properties文件夹：launchSettings.json文件为本地开发环境定义了不同的配置文件设置，如在项目创建时分配的端口号保存在此文件中