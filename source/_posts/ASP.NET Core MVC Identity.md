---
title: ASP.NET Core MVC Identity
date: 2023-08-24
tags:
- CSharp
- NET
categories:
- CSharp
---

## Identity定义
ASP.NET Core Identity是用于构建ASP.NET Core Web应用程序的身份认证系统，包括用户数据，用户身份以及注册登录信息数据存储，可以让您的应用拥有登录功能以及持续化存储登录用户相关的数据。
- 一个API，支持用户界面UI登录功能
- 管理用户、密码、配置文件数据、角色、声明、令牌、电子邮件确认等

用户可使用存储在 Identity 中的登录信息创建帐户，或者可使用外部登录提供程序。支持的外部登录提供程序包括 Facebook、Google、Microsoft 帐户和 Twitter。

Identity 通常使用 SQL Server 数据库进行配置，以存储用户名、密码和配置文件数据。或者，可使用其他持久性存储，例如 Azure 表存储。

## Identity应用步骤
1. 通过模板创建项目 - ASP.NET Core Web应用（MVC），在其它信息页面，身份验证类型栏选择【个人账户】
    - 生成的项目将 ASP.NET Core Identity作为Razor类库提供。IdentityRazor 类库公开具有 Identity 区域的终结点
2. 创建数据库并迁移
3. 修改数据库连接字符串
4. 数据库更新迁移，在VS中打开视图-其它窗口-程序包管理器控制台，输入`Update-Database`，进行数据库迁移，再完成后打开数据库，会发现多出了几张表：
    - dbo._EFMigrationsHistory
    - dbo.AspNetRoleClaims
    - dbo.AspNetRoles
    - dbo.AspNetUserClaims
    - dbo.AspNetUserLogins
    - dbo.AspNetUserRoles
    - dbo.AspNetUsers
    - dbo.AspNetUserTokens
5. 配置Identity服务
```c#
// Program.cs
using DemoCoreIdentity.Data;
using Microsoft.AspNetCore.Identity;
using Microsoft.EntityFrameworkCore;

var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
var connectionString = builder.Configuration.GetConnectionString("DefaultConnection");
builder.Services.AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(connectionString));
builder.Services.AddDatabaseDeveloperPageExceptionFilter();

#region Identity

builder.Services.AddDefaultIdentity<IdentityUser>(options => options.SignIn.RequireConfirmedAccount = true)
    .AddEntityFrameworkStores<ApplicationDbContext>();
builder.Services.AddControllersWithViews();

builder.Services.Configure<IdentityOptions>(options =>
{
    // Password settings.
    options.Password.RequireDigit = true;
    options.Password.RequireLowercase = true;
    options.Password.RequireNonAlphanumeric = true;
    options.Password.RequireUppercase = true;
    options.Password.RequiredLength = 6;
    options.Password.RequiredUniqueChars = 1;

    // Lockout settings.
    options.Lockout.DefaultLockoutTimeSpan = TimeSpan.FromMinutes(5);
    options.Lockout.MaxFailedAccessAttempts = 5;
    options.Lockout.AllowedForNewUsers = true;

    // User settings.
    options.User.AllowedUserNameCharacters =
    "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789-._@+";
    options.User.RequireUniqueEmail = false;
});

builder.Services.ConfigureApplicationCookie(options =>
{
    // Cookie settings
    options.Cookie.HttpOnly = true;
    options.ExpireTimeSpan = TimeSpan.FromMinutes(5);

    options.LoginPath = "/Identity/Account/Login";
    options.AccessDeniedPath = "/Identity/Account/AccessDenied";
    options.SlidingExpiration = true;
});

#endregion

var app = builder.Build();

// Configure the HTTP request pipeline.
if (app.Environment.IsDevelopment())
{
    app.UseMigrationsEndPoint();
}
else
{
    app.UseExceptionHandler("/Home/Error");
    // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
    app.UseHsts();
}

app.UseHttpsRedirection();
app.UseStaticFiles();

app.UseRouting();

app.UseAuthentication();
app.UseAuthorization();

app.MapControllerRoute(
    name: "default",
    pattern: "{controller=Home}/{action=Index}/{id?}");
app.MapRazorPages();

app.Run();
```
    上述代码用默认选项值来配置 Identity，可通过依赖关系注入向应用提供服务。通过调用 UseAuthentication 启用 Identity， UseAuthentication 向请求管道添加身份验证中间件。

## Identity测试
1. 运行程序，默认打开Home/Index页面
2. 注册用户，点击注册链接（右上角Register），打开注册窗口，输入用户名/密码，点击注册按钮即可（当注册校验不通过时，会有错误提示）
3. 用户登录，点击登录链接（右上角Login），登录成功后会显示用户名
4. 用户登出，点击登出链接（右上角Logout），会重新返回Home/Index页面并显示未登录状态

## 身份验证
通过模板创建的项目，默认情况下，Home/Index是没有身份验证的，可以在HomeController增加Authorize特性，增加身份验证，当添加成功后，再次打开首页时，会自动跳转到登录页面。
```c#
// Controllers/HomeController.cs
using DemoCoreIdentity.Models;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using System.Diagnostics;

namespace DemoCoreIdentity.Controllers
{
    [Authorize]
    public class HomeController : Controller
    {
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger)
        {
            _logger = logger;
        }

        public IActionResult Index()
        {
            return View();
        }

        public IActionResult Privacy()
        {
            return View();
        }

        [ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, NoStore = true)]
        public IActionResult Error()
        {
            return View(new ErrorViewModel { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
        }
    }
}
```
【注】：如果要对某一个Action增加验证，可以将Authorize特性添加在action上，进行更详细的身份验证。

## 添加基架项目
1. 右键项目-添加-新搭建基架的项目
2. 选择标识，点击添加
3. 勾选替代所有文件，数据上下文类：ApplicationDbContext，选择添加
4. 等待程序自动运行结束后，可以查看Account目录下多了很多文件，其中就包括Login.cshtml/Register.cshtml等
5. 可以根据自己的需求修改定制开发身份验证类页面