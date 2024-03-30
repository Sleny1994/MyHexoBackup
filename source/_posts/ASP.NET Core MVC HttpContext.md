---
title: ASP.NET Core MVC HttpContext
date: 2023-08-21
tags:
- CSharp
- NET
- HTTP
categories:
- CSharp
---

## HttpContext定义
在B/S模式开发的程序中，客户端是浏览器，服务器端Web应用程序，HttpContext是连接客户端和服务端的桥梁，描述了当前请求的环境信息，封装了[Request]和[Response]及其它所有信息。
1. 直接访问程序
(Network) <--HTTP--> {Kestrel} <--HttpContext--> {App Code}
2. 反向代理访问程序
(Network) <--HTTP--> (Reverse Proxy Server[IIS/Nginx/Apache]) <--HTTP--> {Kestrel} <--HttpContext--> {App Code}

【注】：
- Kestrel是一个基于libuv的跨平台ASP.NET Core Web服务器
- HttpContext从客户端发起一个请求开始，到服务器端响应完成结束，每一个新的请求，都会创建一个新的HttpContext对象

## HttpContext属性
常用的属性有3个：
1. Request - HttpRequest获取此请求的对象
2. Response - HttpResponse获取此请求的对象
3. Session - 获取或设置用于管理此请求的用户会话数据的对象

## HttpContext应用
1. 控制器内
    - 在控制器中，HttpContext作为控制器父类ControllerBase的属性存在，且Request和Response作为使用频率非常高的常用对象都声明成为了属性，可直接使用
2. 控制器外
    - 首先，需要一个服务接口IDemoService和服务实现类DemoService，在DemoService中访问HttpContext
    ```c#
    // Models/DemoService.cs 服务实现类
    namespace MvcDemo.Models
    {
        public class DemoService : IDemoService
        {
            private readonly IHttpContextAccessor contextAccessor;

            public DemoService(IHttpContextAccessor contextAccessor)
            {
                this.contextAccessor = contextAccessor;
            }

            public void Save()
            {
                var name = this.contextAccessor.HttpContext?.Request.Query["name"];
                Console.WriteLine(name);
            }
        }
    }

    // Models/IDemoService.cs 服务接口
    namespace MvcDemo.Models
    {
        public interface IDemoService
        {
            void Save();
        }
    }
    ```
    - 其次，在控制器中通过构造函数的方式将IDemoService注入
    ```c#
    using Microsoft.AspNetCore.Mvc;
    using MvcDemo.Models;

    namespace MvcDemo.Controllers
    {
        public class DemoController : Controller
        private readonly IDemoService demoService;

        public DemoController(IDemoService demoService) 
        {
            this.demoService = demoService;
        }

        public IActionResult Save()
        {
            demoService.Save();
            return Json("Succeeded！");
        }

        public IActionResult Index()
        {
            return View();
        }
    }
    ```
    - 最后，在Program.cs中将服务添加到容器中
    ```c#
    //增加一个默认的HttpContextAccessor
    builder.Services.AddHttpContextAccessor();
    //增加服务
    builder.Services.AddScoped<IDemoService, DemoService>();
    ```

## HttpRequest
### 定义
表示单个请求的传入端，常用的Query用于获取Get请求传递的参数，Form用于获取Post请求传递的参数
1. Form - 获取或设置窗体形式的请求正文
2. Query - 获取从RequestQueryString分析的查询值集合

### 示例
1. 以Request.Form为例，获取Post方式传递的参数，客户端将所有需要传递的内容包括在Form表单内容中，在服务器端Action中通过Request.Form["Key"]进行获取。
```cs
// Views/Hello/Form.cshtml
...

// Controllers/HelloController.cs
...
```

2. 其它示例
```c#
 public IActionResult Test5()
 {
     Console.WriteLine($"Request.Host:{Request.Host}");
     Console.WriteLine($"Request.Path:{Request.Path}");
     Console.WriteLine($"Request.Protocol:{Request.Protocol}");
     Console.WriteLine($"Request.ContentType:{Request.ContentType}");
     Console.WriteLine($"Request.Headers:");
     foreach (var header in Request.Headers)
     {
         Console.WriteLine($"{header.Key}:{header.Value}");
     }
     Console.WriteLine($"Request.Cookies:");
     foreach (var cookie in Request.Cookies)
     {
         Console.WriteLine($"{cookie.Key}:{cookie.Value}");
     }

     return View();
 }

// CMD Output:
Request.Host:localhost:5228
Request.Path:/Hello/Test5
Request.Protocol:HTTP/1.1
Request.ContentType:
Request.Headers:
Accept:text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Connection:keep-alive
Host:localhost:5228
User-Agent:Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.0.0 Safari/537.36 Edg/115.0.1901.203
Accept-Encoding:gzip, deflate, br
Accept-Language:zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Cache-Control:max-age=0
Cookie:.AspNetCore.Antiforgery.IiQD26AZ5A4=CfDJ8IfgIi-nkbNKnH68S3fYoq19ZVQomb17eB6ozUOTshG7wsYq4h8TpRNSJEbAqJ1IHYv3V8o9FM1pA-kTLMMAa-hvfxEyqv_AV2rxo5_QzE2gnpnJck7hjlvVoYjboecS57m-4RybA4vSqmlKAHJzOEI
Upgrade-Insecure-Requests:1
sec-ch-ua:"Not/A)Brand";v="99", "Microsoft Edge";v="115", "Chromium";v="115"
sec-ch-ua-mobile:?0
sec-ch-ua-platform:"macOS"
Sec-Fetch-Site:none
Sec-Fetch-Mode:navigate
Sec-Fetch-User:?1
Sec-Fetch-Dest:document
Request.Cookies:
.AspNetCore.Antiforgery.IiQD26AZ5A4:CfDJ8IfgIi-nkbNKnH68S3fYoq19ZVQomb17eB6ozUOTshG7wsYq4h8TpRNSJEbAqJ1IHYv3V8o9FM1pA-kTLMMAa-hvfxEyqv_AV2rxo5_QzE2gnpnJck7hjlvVoYjboecS57m-4RybA4vSqmlKAHJzOEI
[41m[30mfail[39m[22m[49m: Microsoft.AspNetCore.Mvc.ViewFeatures.ViewResultExecutor[3]
```
[注:]如果在Request的Get请求中，默认ContentType为空，Cookies如果没有设置，也为空。

## HttpResponse
### 定义
表示单个请求的传出内容
- Body：获取或设置响应正文Stream
- BodyWriter：获取响应正文PipeWriter
- ContentLength：获取或设置响应标头的值Content-Length
- ContentType：获取或设置响应标头的值Content-Type
- Cookies：获取一个对象，该对象可用于管理此响应的Cookie
- HasStarted：获取一个值，该值指示是否已将响应标头发送到客户端
- Headers：获取响应标头
- HttpContext：获取HttpContext此响应的
- StatusCode：获取或设置HTTP响应代码
### 状态码StatusCode
- StatusCode是一个INT类型，表示当前响应HTTP请求的状态，可通过System.Net.HttpStatusCode(enum)进行转换
    - OK = 200，成功
    - NotFound = 404， 未发现即请求的信息不存在
    - InternalServerError = 500，服务器内部错误
    - Redirect = 302，请求重定向
- 在Controller中，常见状态码返回值可以直接调用，如：Ok()、NotFound()
### 示例
在响应的Headers中添加Author信息
```c#
public IActionResult Test6(){
    Response.Headers.Add("Author", "王五");
    return Json("ABC");    
}
// InvalidOperationException: Invalid non-ASCII or control character in header: 0x738B
// 当输入汉字时，会报错，需要进行转码
// 修改后的代码如下
public IActionResult Test6(){
    var author = HttpUtility.UrlEncode("王五", Encoding.UTF8);
    Response.Headers.Add("Author", author);
    return Json("1");    
}
// F12中可以查看响应头
/*
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Date: Tue, 22 Aug 2023 01:32:47 GMT
Server: Kestrel
Transfer-Encoding: chunked
Author: %e7%8e%8b%e4%ba%94
*/
```

## 会话Session
由于HTTP请求是无状态的，单次请求完后就会进行释放，而使用Session可以将用户请求的一些数据，以键值对的形式保存在服务器端的缓存中，可解决无状态协议模式下数据的频繁传递，减少数据量的请求，提高性能。</br>
Session一般用于小型单体应用程序中，对于大型的分布式程序，则不适用。</br>
每一用户的浏览器请求都有自己的Session内存块，不会和其它用户的请求相混肴。
### 启用
需要在Program.cs中添加Session服务，和启用Session中间件
```c#
// Program.cs
using Microsoft.AspNetCore.Server.Kestrel.Core;
using Microsoft.EntityFrameworkCore;
using MvcDemo.Entities;
using MvcDemo.Models;
using System.ComponentModel.DataAnnotations;
using System.Text.Encodings.Web;
using System.Text.Unicode;

var builder = WebApplication.CreateBuilder(args);
//注入数据库框架
builder.Services.AddDbContext<DemoDbContext>(options => options.UseSqlServer(builder.Configuration.GetConnectionString("Default")));

// Add services to the container.
builder.Services.AddControllersWithViews();
builder.Services.AddControllersWithViews().AddJsonOptions(options =>
{
    options.JsonSerializerOptions.Encoder = JavaScriptEncoder.Create(UnicodeRanges.All);
});

builder.Services.Configure<KestrelServerOptions>(options =>
{
    options.AllowSynchronousIO = true;
});

// 增加一个默认的HttpContextAccessor
builder.Services.AddHttpContextAccessor();
// 增加服务
builder.Services.AddScoped<IDemoService, DemoService>();

// 在容器中添加Session服务，启用Session服务
builder.Services.AddSession();

var app = builder.Build();

// Configure the HTTP request pipeline.
if (!app.Environment.IsDevelopment())
{
    app.UseExceptionHandler("/Home/Error");
}

// 使用Session中间件，主要用于拦截Http请求
app.UseSession();
//app.UseHttpsRedirection();

app.UseDefaultFiles();
app.UseStaticFiles();

app.UseRouting();

app.UseAuthorization();

app.MapControllerRoute(
    name: "default",
    pattern: "{controller=Home}/{action=Index}/{id?}");

app.Run();
```
### 属性和方法
实际情况下，一般使用扩展方法SetString(key,value),GetString(key,value)进行Session值的设置和获取
- 属性
    1. Id，当前会话的唯一标识符，会Cookie不同，因为Cookie生存期可能与数据存储中的会话条目生存期不同
    2. IsAvailable，指示当前会话是否已成功加载，在加载会话之前访问此属性将导致其内联加载
    3. Keys，枚举所有键（如果存在）
- 方法
    1. Clear()，从当前会话中删除所有条目，不会删除会话Cookie
    2. CommitAsync(CancellationToken)，将会话存储在数据存储中，如果数据存储不可用，可能会引发此问题
    3. LoadAsync(CancellationToken)，从数据存储加载会话，如果数据存储不可用，可能会引发此问题
    4. Remove(String)，如果存在，请从会话中删除给定密钥
    5. Set(String, Byte[])，在当前会话中设置给定的键值，如果在发送响应之前未建立会话，则会引发此事件
    6. TryGetValue(Strin, Byte[])，检索给定键的值（如果存在）
- 扩展方法
    1. Get(ISession, String)，从ISession中获取字节数组值
    2. GetInt32(ISession, String)，从ISession中获取int值
    3. GetString(ISession, String)，从ISession中获取字符串值
    4. SetInt32(ISession, String, Int32)，在ISession中设置int值
    5. SetString(ISession, String, String)，在ISession中设置String值

【注:】
1. 在Controller中，可以直接使用Session属性
2. 在非Controller中，可以使用请求上下文HTTPContext进行获取
### 示例
以登录页面为例：</br>
1. 用户打开登录页面，输入账号密码，点击登录按钮
2. 验证用户名密码，成功后保存Session，并跳转至首页/Home/Index
3. Home/Index中获取Session中保存的内容，并通过ViewBag传递到客户端，在页面上显示
- 首先，创建控制器LoginController
```c#
// Controllers/LoginController.cs
using Microsoft.AspNetCore.Mvc;

namespace MvcDemo.Controllers
{
    public class LoginController : Controller
    {
        public IActionResult Index()
        {
            return View();
        }

        public IActionResult Login()
        {
            var username = Request.Form["username"];
            var password = Request.Form["password"];
            if (username == "admin" && password == "admin")
            {
                HttpContext.Session.SetString("username", username);
            }
            return Redirect("/Home");
        }
    }
}
```
- 其次，创建Login下的Index视图
```c#
// Login/Index.cshtml
<form action="~/Login/Login" method="post">
    <div style="margin:10px;">
        <span style="display:inline-block; width:80px;">用户名：</span>
        <input type="text" name="username" />
    </div>
    <div style="margin:10px;">
        <span style="display:inline-block;width:80px;">密  码：</span>
        <input type="password" name="password" />
    </div style="margin:10px;">
    <div style="margin:10px;">
        <input type="submit" name="submit" value="登录" />
    </div>
</form>
```
- 再修改HomeController
```c#
    public IActionResult Index()
    {
        var username = HttpContext.Session.GetString("username");
        ViewBag.Username = username;
        return View();
    }
```
- 最后修改Home下的Index视图，获取VieBag传入的Username值
```c#
@{
    ViewData["Title"] = "Home Page";
}

<div class="text-center">
    <h1 class="display-4">Welcome @ViewBag.Username</h1>
    <p>Learn about <a href="https://docs.microsoft.com/aspnet/core">building Web apps with ASP.NET Core</a>.</p>
</div>
```
访问localhost:5228/Login，输入用户名密码admin/admin，点击登录会跳转至首页，可以查看到Welcome admin

## Session唯一标识
每个浏览器打开的Session都有一个唯一标识，在控制器中，可以通过HttpContext.Session.Id进行区分，可在Program.cs中添加服务到容器时配置相关参数
```c#
// Program.cs
builder.Services.AddSession(option =>
{
    option.IdleTimeout = TimeSpan.FromMinutes(10);
    option.Cookie.Name = "MvcDemo";
});
```
设置Session选项中的Cookie名称后，会在浏览器客户端创建对应的值