---
title: ASP.NET Core MVC 路由
date: 2023-08-17
tags:
- CSharp
- NET
categories:
- CSharp
---

## 定义
路由是一种机制，主要用于检查每个用户请求，将用户请求映射到Action中，此动作通过路由中间件来实现。

## 默认路由
一般通过模板创建的MVC项目，默认会添加路由中间件，并提供一种默认的路由映射规则和约束。</br>
MapControllerRoute用于创建单个路由，单个路由命名为default，大多数具有Controller和View的应用都使用类似default路由的路由模板。
```c#
using Microsoft.AspNetCore.Server.Kestrel.Core;
using System.Text.Encodings.Web;
using System.Text.Unicode;

var builder = WebApplication.CreateBuilder(args);
// Add services to the container.
// View() -> Json() ,解决中文乱码的问题
builder.Services.AddControllersWithViews().AddJsonOptions(options =>
{
    options.JsonSerializerOptions.Encoder = JavaScriptEncoder.Create(UnicodeRanges.All);
});
builder.Services.Configure<KestrelServerOptions>(options =>
{
    options.AllowSynchronousIO = true;
});
var app = builder.Build();
// Configure the HTTP request pipeline.
if (!app.Environment.IsDevelopment())
{
    app.UseExceptionHandler("/Home/Error");
    // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
    app.UseHsts();
}
app.UseHttpsRedirection();
app.UseStaticFiles();
//1. 添加路由中间件EndpointRoutingMiddleware
app.UseRouting();
app.UseAuthorization();
//2.为控制器和Action添加一种路由映射规则，包括名称，规则，约束等
app.MapControllerRoute(
    name: "default",
    pattern: "{controller=Home}/{action=Index}/{id?}");
app.Run();
```
- name: 表示路由名称，默认值为default
- pattern: 为匹配模板，默认值为`"{controller=Home}/{action=Index}/{id?}"`，其中id后面的？表示为可空类型
### 示例
- /表示controller和action都采用默认值，即/Home/Index
- /Hello表示只指定controller，action为默认缺省值，即/Hello/Index
- /Hello/Test/1表示指定controller=HelloController，action=Test，id=1，当存在对应controller和action时则匹配导航成功
```c#
// 默认路由的简写
app.MapDefaultControllerRoute();
```
### 特点
- 仅基于控制器和操作名称
- 不基于命名空间、源文件位置或方法参数
- 使用默认路由进行传统路由可以无需为每个操作提出新的URL模式，有助于简化代码

## 多个路由
在实际应用中，恶意设置多个路由，为某些特定的需求设置专有路由
```cs
app.MapControllerRoute(
    name: "blog",
    pattern: "blog/{*article}",
    defaults: new { controller = "Blog", action = "Article" });

app.MapControllerRoute(
    name: "default",
    pattern: "{controller=Home}/{action=Index}/{id?}");
```
- blog路由比default路由具有更高的优先级，是因为其先添加
- 路由名称为路由指定逻辑名称，使用命名路由可以简化URL的创建，在应用程序范围内必须唯一
- 默认情况下，传统路由依赖于顺序；一般情况下，具有区域的路由应放在路由表的前列位置，因为其更具体；即特殊的在前面，通用的在后面。 

## 不明确操作
如果同一个请求有两个action都满足路由终结点的匹配，那么路由会进行如下处理：
- 选择最佳的候选项
- 引发异常
如何解决上述问题，在这种情况下，要解析正确的路由，需要通过HTTP谓词来区分，只有当请求为POST时，才会请求actionA，其它请求则匹配actionB
```c#
public IActionResult Edit(int id)
{
    var student = new Student()
    {
        Id = 1,
        Name = "Sleny",
        Age = 20,
        Sex = "男"
    };
    return View();
}

[HttpPost]
public IActionResult Edit(int id, Student student)
{
    return View();
}
```

## 属性路由
一般用于WebAPI，使用一组属性将操作直接映射到路由模板，将应用功能建模为一组资源，属性路由使用`[Route(template)]`标记于controller或action中
```c#
public class TestController : Controller
{
    [Route("Test1")]
    [Route("Test1/Index")]
    [Route("Test1/Index/{id?}")]
    public IActionResult Index(int id)
    {
        ViewBag.Id = id;
        return View();
    }

    public IActionResult Test()
    {
        return View();
    }
}
```
【注】：属性路由中，也可用标记：`[Route("[controller]/[action]")]`，效果与传统路由一致
### 与传统路由的差异
- 属性路由需要更多输入才能指定路由，自定义比较强，能更精准的控制路由
- 传统路由会更简洁
- 属性路由优先级高于传统路由，对于属性路由，controller和action名称在操作匹配中不起作用，除非使用标记替换
- 属性路由支持定义多个访问同一操作的路由，意味着每个路由属性都与操作方法上的每个路由属性相结合
- 属性路由支持使用传统路由相同的内联语法，来指定可选参数、默认值与约束，如`[HttpPost("product14/{id:int}")]`

## 保留的关键字
1. MVC中作为路由参数的名称
- action
- area
- controller
- handler
- page
2. Razor视图或Razor页面中
- page
- using
- namespace
- inject
- section
- inherits
- model
- addTagHelper
- removeTagHelper
### 错误示例
使用page作为属性路由的路由参数，会导致与URL生成不一致和令人困惑的行为
```cs
public class TestController : Controller
{
    [Route("/articles/{page}")]
    public IActionResult ListArticles(int page)
    {
        return View(page);
    }
}
```

## Http谓词和路由模板
1. 常见谓词
    - [HttpGet]
    - [httpPost]
    - [HttpPut]
    - [HttpDelete]
    - [HttpHead]
    - [HttpPatch]
2. 常见路由模板
    - 所有HTTP谓词模板都是路由模板
    - [Route("...")]

## 混合路由
ASP.NET Core应用可以混合使用传统路由和属性路由。
- 一般对为浏览器提供HTML页的Controller使用传统路由
- 而对为API提供服务REST的Controller使用属性路由

操作既支持传统路由，也支持属性路由，但不能通过传统路由访问定义属性路由的操作，反之相同。控制器上的任何路由属性都会使控制器中的所有操作使用属性路由。

属性路由和传统路由使用相同的路由引擎。
