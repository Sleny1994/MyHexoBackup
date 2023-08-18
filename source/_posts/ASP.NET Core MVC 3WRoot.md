---
title: ASP.NET Core MVC 3WRoot
date: 2023-08-17
tags:
- CSharp
- NET
categories:
- CSharp
---

## 3WROOT文件夹
模板创建的MVC项目，会在程序的根目录创建一个wwwroot文件夹，此文件夹又被称为webroot文件夹，主要用于存放静态资源文件，如：html、JavaScript、CSS样式等。默认情况下，存放在该文件夹的所有静态资源都可以通过Http请求提供服务。在新的框架中，有且只有存放于该目录的静态资源可以直接通过Http访问，其他目录下的静态资源都将阻止。

## 静态资源中间件
为了能够通过HTTP直接访问3Wroot文件夹下的静态资源，需要在程序启动时[Program.cs]加载静态资源中间件
```c#
app.UseStaticFiles();
```

## 静态资源示例
- 首先，在wwwroot目录下，创建index.html
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title>静态首页</title>
</head>
<body>
    <h1>Welcome!</h1>
    <h2>这是静态首页</h2>
</body>
</html>
```
- 接着，在Program.cs启动文件中，添加允许默认文件映射
```cs
// 允许默认文件映射
app.UseDefaultFiles();
// 启动静态资源服务中间件
app.UseStaticFiles();

// 文件服务中间件 - 也可以实现静态资源文件
app.UseFileServer(options);
```
- 最后，运行程序，会发现静态资源的index页面会替换原先Home/index视图页面，即默认静态资源首页优先级高于默认路由

## 修改默认资源名称
默认启动静态资源名称为Index.html，可以通过DefaultOptions指定默认的首页加载顺序和名称
```cs
//默认文件启动项
DefaultFilesOptions options = new DefaultFilesOptions();
options.DefaultFileNames.Add("Hello.html");
app.UseDefaultFiles(options);
app.UseStaticFiles();
```
但此时启动程序却发现依然加载的是Index.html，这是因为DefaultFilesOptions会自动填充4个默认页面名称，后面添加的页面名称会在原有默认页面之后，将Index.html删除后即可实现。

## 客户端库
定义：主要是指JavaScript、CSS等第三方库
安装步骤：
1. 在项目名称处右键，选择添加，客户端库
2. 在打开的添加窗口中，选择需要的库名称进行搜索安装
3. 安装成功后，即可查看