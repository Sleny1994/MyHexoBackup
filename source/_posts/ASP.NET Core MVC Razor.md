---
title: ASP.NET Core MVC Razor
date: 2023-08-17
tags:
- CSharp
- NET
categories:
- CSharp
---

## Razor视图
- 以.cshtml作为扩展名
- 提供一种巧妙地方法来使用C#创建HTML输出

## Razor介绍
Razor是一种标记语法，用于将基于.NET地代码嵌入网页中，其由Razor标记、C#和HTML组成。和纯粹地HTML文件呈现地效果没有什么不同。

## Razor语法
Razor支持C#，并使用@符号从HTML转换为C#。Razor会计算C#表达式，并将其呈现在HTML输出中，当符号@后面连接Razor保留关键字时，它将转换为Razor特定地标记，否则会转换为纯粹地HTML，当然EMail中的@除外。

## Razor表达式
### 隐式
- 以@开头，后连接C#代码
```c#
<p>今天是：@DateTime.Now</p>
<p>昨天是：@DateTime.Now.AddDays(-1)</p>
```
- 隐式表达式不能包含空格，但C# `await`除外，如果C#语句具有明确结束的标记，则可以混用空格
```c#
@{
    ViewData["Title"] = "Index";

    async Task<string> DoSomething(string left,string right)
    {
        return left + right;

    }
}


<h1>Index</h1>
<p>@await DoSomething("hello", "world")</p>
```
- 隐式表达式不能包含C#泛型，因为尖括号`<>`内的字符会被解释为HTML标记
```c#
// 以下代码无效
@{
    ViewData["Title"] = "Index";

    string? GenericMethod<T>(T t)
    {
        return t?.ToString();
    }
}


<h1>Index</h1>
<!--不能使用泛型-->
<p>@GenericMethod<int>(0)</p>
```
【注】：泛型方法调用必须包装在显示Razor表达式或Razor代码块中

### 显示
- 由@符号和一对小括号()组成，将计算`@()`中的所有内容
```c#
@{
    ViewData["Title"] = "Index";
    int a=1,b=2,c=3,d=4;
}


<h1>Index</h1>
<!--显式Razor表达式-->
<p>@(a+b+c+d)</p>
// ---> 10
<!--隐式Razor表达式-->
<p>@a+b+c+d</p>
// ---> 1+b+c+d
```
- 显示表达式支持泛型
```c#
<p>@(GenericMethod<int>(10))</p>
```

## Razor编码
```html
<!--输出原生标签并进行转义-->
<div>@("<span>Hello World</span>")</div>
<!--输入标签中的内容-->
<div>@Html.Raw("<span>Hello World</span>")</div>
```

## Razor代码块
- 以@开始，并括在{}中
- 代码块中的C#代码不会显示
- 一个视图中的代码块和表达式共享相同的作用域并按顺序进行定义
### 隐式转换
C# -> HTML
```C#
@{
    var name = "Sleny";
    <p>Now in HTML, was in C# @name</p>
}
```
### 显示转换
需要在代码块中以HTML形式显示整个行的其余内容，要使用`@:`
```c#
@for (var i = 0; i < students.Length; i++)
{
    var student = students[i];
    @:student: @student.Name
}
```
如果没有会导致Razor运行错误，在将多个隐式表达式和显示表达式合并到单个代码块后很常见
### 控制结构
1. 条件控制语句
@if,else if,else
```c#
@{
    int value = 10;
}

@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
else if (value >= 1337)
{
    <p>The value is large.</p>
}
else
{
    <p>The value is odd and small.</p>
}
```
@switch
```c#
@switch (value)
{
    case 1:
        <p>The value is 1!</p>
        break;
    case 1337:
        <p>Your number is 1337!</p>
        break;
    default:
        <p>Your number wasn't 1 or 1337.</p>
        break;
}
```
2. 循环控制语句
@for
```c#
@{
    var students = new Student[]
    {
        new Student(){Id=1,Name="1"},
        new Student(){Id=2,Name="2"},
        new Student(){Id=3,Name="3"},
        new Student(){Id=4,Name="4"},
    };
}
@for (var i = 0; i < students.Length; i++)
{
    var student = students[i];
    <div>
        <span>Id: @student.Id</span>
        <span>Name: @student.Name</span>
    </div>
}
```
@foreach
```c#
@foreach (var student in students)
{
    <div>
        <span>Id: @student.Id</span>
        <span>Name: @student.Name</span>
    </div>
}
```
@while
```c#
@{ var i = 0; }
@while (i < students.Length)
{
    var student = students[i];
    <div>
        <span>Id: @student.Id</span>
        <span>Name: @student.Name</span>
    </div>
    i++;
}
```
@do while
```c#
@{ var i = 0; }
@do
{
    var student = students[i];
    <div>
        <span>Id: @student.Id</span>
        <span>Name: @student.Name</span>
    </div>

    i++;
} while (i < students.Length);
```

## Razor异常
@try,catch,finally
```c#
@try
{
    throw new InvalidOperationException("无效操作.");
}
catch (Exception ex)
{
    <p>异常信息: @ex.Message</p>
}
finally
{
    <p>finally code.</p>
}
```

## Razor注释
支持C#和HTML注释，Razor使用`@* *@`来分隔注释，被注释的代码不会呈现任何标记

## Razor关键字
1. Razor相关
- page
- namespace
- functions
- inherits
- model
- section
- helper(ASP.NET Core当前不支持)
使用语法：`@(Razor Keyword)`进行转移，如：`@(functions)`
2. C#相关
- do while
- default
- for foreach
- lock
- switch case
- try catch finally
- using
使用语法：`@(@C# Razor Keyword)`需要进行双转义，如：`@(@case)`,第一个@对Razor转义，第二个@对C#转义
3. 未使用的保留关键字
- class