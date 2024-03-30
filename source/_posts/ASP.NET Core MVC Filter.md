---
title: ASP.NET Core MVC Filter
date: 2023-08-24
tags:
- CSharp
- NET
categories:
- CSharp
---

## Filter定义
Filter又被称为筛选器、过滤器。在ASP.NET Core MVC项目中，使用Filter可以在请求处理管道的特定位置之前或之后运行代码。可以创建自定义Filter，用于处理横切关注点，相当于于AOP面向切面编程。对于创建Filter，可以减少代码的复制，如：错误处理异常筛选器可以合并错误处理。

## Filter工作原理
从请求开始，到请求结束，经过一系列的节点，组成了调用管道，其在调用管道内运行，过滤器相当于在管道中设置了几个钩子，用于执行特定的代码。

请求 - 路由 - 过滤器 - Model绑定和验证 - 过滤器 - Controller/Action - 过滤器 - View - 过滤器 - HTML

## Filter类型
根据不同的处理功能，主要分类如下所示：
- 授权筛选器Authorization Filter：
    - 首先运行
    - 确定用户是否获得请求授权
    - 如果请求未获授权，可以让管道短路
- 资源筛选器Resource Filter：
    - 授权后运行
    - OnResourceExecuting在筛选器管道的其余阶段之前运行代码，如：OnResourceExecuting在模型绑定之前运行代码
    - OnResourceExecuted在管道的其余阶段完成之后运行代码
- 操作筛选器Action Filter：
    - 在调用操作方法之前和之后立即运行
    - 可以更改传递到操作中的参数
    - 可以更改从操作返回的结果
    - 不可在 Razor Pages 中使用
- 异常筛选器Exception Filter在向响应正文写入任何内容之前，对未经处理的异常应用全局策略
- 结果筛选器Result Filter：
    - 在执行操作结果之前和之后立即运行
    - 仅当操作方法成功执行时才会运行
    - 对于必须围绕视图或格式化程序的执行的逻辑会有更大的作用

Filter筛选器类型在筛选器管道中的交互方式：</br>
Authorization Filters -> Resource Filters -> (Model Binding) -> Action Filters -> (Action Execution -> Action Result Conversion) -> Exception Filters -> Result Filters -> (Result Execution) -> Result Filters -> Resource Filters 

## Filter实现
所有的Filter都实现接口IfilterMetadata，根据不同的业务类型，派生出了五个接口，分别对应五大类Filter：
- IAuthorizationFilter: OnAuthorization(AuthorizationFilterContext)
- IExceptionFilter: OnException(ExceptionContext)
- IResourceFilter: OnResourceExecuted(ResourceExecutedContext)&OnResourceExecuting(ResourceExecutingContext)
- IActionFilter: OnActionExecuted(ActionExecutedContext)&OnActionExecuting(ActionExecutingContext)
- IResultFilter: OnResultExecuted(ResultExecutedContext)&OnResultExecuting(ResultExecutingContext)

## Filter作用域
Filter可以作用在Controller、Action、全局，其在同步操作筛选器运行筛选器方法的顺序：</br>
全球(OnActionExecuting) > 控制器(OnActionExecuting) > 操作(OnActionExecuting) > 操作(OnActionExecuted) > 控制器(OnActionExecuted) > 全球(OnActionExecuted)

## 授权Filter
授权筛选器具有以下特征：
- 是筛选器管道中运行的第一个筛选器
- 控制对操作方法的访问
- 具有在它之前的执行的方法，但没有之后执行的方法

例如：常用的RequireHTTPS就是授权筛选器，其实现了IAuthorizationFilter接口，并继承了Attirbute，所以可以作用于Controller或Action中，以限制请求的方式。
```c#
using Microsoft.AspNetCore.Mvc.Filters;
using System;

namespace Microsoft.AspNetCore.Mvc
{
    //
    // 摘要:
    //     An authorization filter that confirms requests are received over HTTPS.
    [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = false)]
    public class RequireHttpsAttribute : Attribute, IAuthorizationFilter, IFilterMetadata, IOrderedFilter
    {
        public RequireHttpsAttribute();

        public bool Permanent { get; set; }

        public int Order { get; set; }

        public virtual void OnAuthorization(AuthorizationFilterContext filterContext);

        protected virtual void HandleNonHttpsRequest(AuthorizationFilterContext filterContext);
    }
}
```

## 资源Filter
资源Filter在授权Filter之后执行，需要实现IResourceFilter接口。
```c#
// Filter/logResourceFilter.cs
using Microsoft.AspNetCore.Mvc.Filters;

namespace MvcDemo.Filter
{
    /// 同步版本
    public class LogResourceFilter :Attribute, IResourceFilter
    {
        public void OnResourceExecuted(ResourceExecutedContext context)
        {
            //Action执行完成后执行
            Console.WriteLine("********************On Resource Filter Executed********************");
        }

        public void OnResourceExecuting(ResourceExecutingContext context)
        {
            //授权Filter执行后执行。
            Console.WriteLine("********************On Resource Filter Executing********************");
        }
    }

    /// 异步版本
    public class AsynLogResouceFilter : Attribute, IAsyncResourceFilter
    {
        public async Task OnResourceExecutionAsync(ResourceExecutingContext context, ResourceExecutionDelegate next)
        {
            Console.WriteLine("********************On Aysnc Resource Filter Executing********************");
            var exceutedContext =  await next();
            Console.WriteLine("********************On Async Resource Filter Executed********************");
        }
    }
}
```
如果要使大部分管道短路，资源筛选器会很有用。如：如果缓存命中，则缓存筛选器可以绕开管道的其余阶段。

## 操作Filter
操作筛选器不应用于 Razor Pages。</br>
Razor Pages 支持 IPageFilter 和 IAsyncPageFilter。</br>

操作筛选器具有以下特征：
- 实现 IActionFilter 或 IAsyncActionFilter 接口
- 它们的执行围绕着操作方法的执行
```c#
// Filter/DoDoActionFilter.cs
using Microsoft.AspNetCore.Mvc.Filters;

namespace MvcDemo.Filter
{
    public class DoDoActionFilter : Attribute, IActionFilter
    {
        public void OnActionExecuted(ActionExecutedContext context)
        {
            
            Console.WriteLine("********************On Action Executed********************");
        }

        public void OnActionExecuting(ActionExecutingContext context)
        {
            Console.WriteLine("********************On Action Executing********************");
        }
    }

    public class AsyncDoDoActionFilter : IAsyncActionFilter
    {
        public async Task OnActionExecutionAsync(ActionExecutingContext context, ActionExecutionDelegate next)
        {
            
            Console.WriteLine("********************On Async Action Executing********************");
            await next();
            Console.WriteLine("********************On Async Action Executed********************");
        }
    }
}
```
ActionExecutingContext的属性：
- ActionArguments：用于读取操作方法的输入
- Controller：用于处理控制器实例
- Result：设置 Result 会使操作方法和后续操作筛选器的执行短路

ActionExecutedContext提供 Controller 和 Result 以及以下属性：
- Canceled：如果操作执行已被另一个筛选器设置短路，则为 true
- Exception：如果操作或之前运行的操作筛选器引发了异常，则为非 NULL 值，将此属性设置为 null：
    - 有效地处理异常
    - 执行 Result，从操作方法中将它返回

对于IAsyncActionFilter，一个向 ActionExecutionDelegate 的调用可以达到以下目的：
- 执行所有后续操作筛选器和操作方法
- 返回 ActionExecutedContext

## 异常Filter
异常筛选器具有以下特征：
- 实现 IExceptionFilter 或 IAsyncExceptionFilter
- 可用于实现常见的错误处理策略
```c#
// Filter/DoExceptionFilter.cs
using Microsoft.AspNetCore.Mvc.Filters;

namespace MvcDemo.Filter
{
    public class DoExceptionFilter :Attribute, IExceptionFilter
    {
        public void OnException(ExceptionContext context)
        {
            Console.WriteLine("********************On Exception********************");
        }
    }

    public class DoAsyncExceptionFilter : Attribute, IAsyncExceptionFilter
    {
        public async Task OnExceptionAsync(ExceptionContext context)
        {
            await Task.Run(() =>
            {
                Console.WriteLine("********************On Exception Async********************");
            });

        }
    }
}
```
异常筛选器的特点：
- 没有之前和之后的事件
- 实现 OnException 或 OnExceptionAsync
- 处理 Razor 页面或控制器创建、模型绑定、操作筛选器或操作方法中发生的未经处理的异常
- 请不要捕获资源筛选器、结果筛选器或 MVC 结果执行中发生的异常
- 非常适合捕获发生在操作中的异常
- 并不像错误处理中间件那么灵活

如果要处理异常，请将 ExceptionHandled 属性设置为 true 或分配 Result 属性，这将停止传播异常。

异常筛选器无法将异常转变为“成功”，只有操作筛选器才能执行该转变。

一般建议使用中间件处理异常，基于所调用的操作方法，仅当错误处理不同时，才使用异常筛选器。
如：应用可能具有用于 API 终结点和视图/HTML 的操作方法。API 终结点可以将错误信息返回为 JSON，而基于视图的操作可能会以 HTML 形式返回错误页。

## 结果Filter
结果筛选器的特征：
- 实现接口：
    - IResultFilter 或 IAsyncResultFilter
    - IAlwaysRunResultFilter 或 IAsyncAlwaysRunResultFilter
- 它们的执行围绕着操作结果的执行
```c#
// Filter/DoResultFilters.cs
using Microsoft.AspNetCore.Mvc.Filters;

namespace MvcDemo.Filter
{
    public class DoResultFilter :Attribute, IResultFilter
    {
        public void OnResultExecuted(ResultExecutedContext context)
        {
            Console.WriteLine("********************On Result Executed********************");
        }

        public void OnResultExecuting(ResultExecutingContext context)
        {
            Console.WriteLine("********************On Result Executing********************");
        }
    }

    public class DoAysncResultFilter :Attribute, IAsyncResultFilter
    {
        public async Task OnResultExecutionAsync(ResultExecutingContext context, ResultExecutionDelegate next)
        {
            Console.WriteLine("********************On Result Execution Async Executing********************");
            await next();
            Console.WriteLine("********************On Result Execution Async Executed********************");
        }
    }

}
```

## Filter测试
将编辑好的过滤器，添加在Home/Index上。
```c#
// Views/Home/Index.cshtml
[DoExceptionFilter]
[LogResourceFilter]
[DoResultFilter]
[DoDoActionFilter]
public IActionResult Index()
{
    _logger.LogInformation("Hello, This is Index！");
    return View();
}
```
【注】：异常过滤器没有输出内容，是因为没有异常产生；授权过滤器没有添加，在所有过滤器之前开始，所有过滤器之后结束。

## Filter全局应用
Filter可以应用在单个Controller或Action上，也可以进行全局应用。
```c#
// Program.cs
builder.Services.AddControllersWithViews(option =>
{
    option.Filters.Add<LogResourceFilter>();
    option.Filters.Add<DoExceptionFilter>();
    option.Filters.Add<DoResultFilter>();
    option.Filters.Add<DoDoActionFilter>();
});
```