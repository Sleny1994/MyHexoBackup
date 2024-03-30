---
title: ASP.NET Core MVC 鉴权授权
date: 2023-08-24
tags:
- CSharp
- NET
categories:
- CSharp
---

在实际开发中，几乎所有程序都是需要进行身份验证，才能访问相关数据的，若无身份验证则安全性就会降低，很容易被窃取和利用。

示意图：
1. 客户端 --访问--> 服务端
2. 客户端 --验证--> 身份验证 --访问--> 服务端

## 鉴权与授权
- 鉴权：即身份验证，是确定用户身份的过程
- 授权：确定用户是否有权访问资源的过程

在ASP.NET中，身份验证是由`IAuthenticationService`负责，被身份验证中间件使用。身份验证服务会使用已注册的身份验证处理程序来完成与身份验证相关的操作。
- 对用户进行身份验证
- 在未经身份验证的用户视图访问受限资源时做出响应

已注册的身份验证处理程序及其配置被称为“方案”，身份验证负责提供ClaimsPrincipal进行授权，以针对其进行权限决策。

鉴权是一个承上启下的环节，上游接受授权的输出，校验真实性后，获取权限，再将为下游的权限控制做好准备。</br>
授权 --Cookie/Session/Token--> 鉴权 --Permission--> 权限控制

## 基本步骤
1. 添加鉴权服务
```c#
// Program.cs
// 示例采用Cookie方式做身份验证
//添加鉴权服务
builder.Services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
}).AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options =>
{
    options.LoginPath = "/Login/Index";
    options.LogoutPath = "/Login/Logout";
});
```
【注】：添加服务时，指定了默认的scheme，以及在没有授权时，需要跳转的登录的页面
2. 添加鉴权、授权中间件
```c#
// Program.cs
//使用鉴权
app.UseAuthentication();
//使用授权
app.UseAuthorization();
```
3. 添加Authorize权限控制
- 在需要进行权限控制的地方[Controller/Action/Views]，添加Authorize特性
```c#
// Controllers/HomeController
namespace MvcDemo.Controllers
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
            var username = HttpContext.Session.GetString("username");
            ViewBag.Username = username;
            return View();
        }

        public IActionResult Privacy()
        {
            return View();
        }

    }
}
```
- 当添加Authorize特性后，在访问Home/Index首页时，如果没有鉴权，则会跳转到登录页面。
4. 鉴权
- 在用户登录后，通过HttpContext.SignlnAsync进行鉴权，并设置ClaimsPrincipal
```c#
// Controllers/LoginController.cs
namespace MvcDemo.Controllers
{
    public class LoginController : Controller
    {
        public IActionResult Index()
        {
            return View();
        }

        public async  Task<IActionResult> Login()
        {
            var username = Request.Form["username"];
            var password = Request.Form["password"];
            if(username=="admin" && password == "admin")
            {
                HttpContext.Session.SetString("username", username);
            }
            var claimsIdentity = new ClaimsIdentity(CookieAuthenticationDefaults.AuthenticationScheme);
            claimsIdentity.AddClaim(new Claim(ClaimTypes.Name,username));
            claimsIdentity.AddClaim(new Claim(ClaimTypes.Role,"Admin"));
            // Name和Role可以为后续授权使用
            var claimPrincipal = new ClaimsPrincipal(claimsIdentity);
            await HttpContext.SignInAsync(claimPrincipal);
            return Redirect("/Home");
        }

        public async Task<IActionResult> Logout()
        {
            await HttpContext.SignOutAsync();
            return Redirect("/Login");
        }
    }
}
```

## 授权区分
在实际应用中，会区分管理员权限与普通用户权限，要实现该功能，则可以在Authorize特性中加以区分，如：`[Authorize(Roles = "Admin,SuperAdmin")]`
```c#
// 有关Authorize特性的属性说明
namespace Microsoft.AspNetCore.Authorization
{
    //
    // 摘要:
    //     Specifies that the class or method that this attribute is applied to requires
    //     the specified authorization.
    [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, AllowMultiple = true, Inherited = true)]
    public class AuthorizeAttribute : Attribute, IAuthorizeData
    {
        //
        // 摘要:
        //     Initializes a new instance of the Microsoft.AspNetCore.Authorization.AuthorizeAttribute
        //     class.
        public AuthorizeAttribute();
        //
        // 摘要:
        //     Initializes a new instance of the Microsoft.AspNetCore.Authorization.AuthorizeAttribute
        //     class with the specified policy.
        //
        // 参数:
        //   policy:
        //     The name of the policy to require for authorization.
        public AuthorizeAttribute(string policy);

        //
        // 摘要:
        //     Gets or sets the policy name that determines access to the resource.
        public string? Policy { get; set; }
        //
        // 摘要:
        //     Gets or sets a comma delimited list of roles that are allowed to access the resource.
        public string? Roles { get; set; }
        //
        // 摘要:
        //     Gets or sets a comma delimited list of schemes from which user information is
        //     constructed.
        public string? AuthenticationSchemes { get; set; }
    }
}
```