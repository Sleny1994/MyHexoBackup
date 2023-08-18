---
title: ASP.NET Core MVC 布局
date: 2023-08-17
tags:
- CSharp
- NET
categories:
- CSharp
---

## 优势
- 可使页面在不同的请求之间保持一致的用户体验
- 可减少视图中的重复代码

## 分类
按照约定，默认的布局名为_Layout.cshtml
- 基于页面的布局文件，Razor页面：Pages/Shared/_Layout.cshtml
- 基于视图控制器的布局文件：Views/Shared/_Layout.cshtml

## 默认布局
模板生成的MVC项目中，会默认生成布局视图，主要包括三部分：
1. 引入公共的JavaScript脚本，CSS样式等资源文件
2. 定义公共的Header，Footer，Left Navigation等用户页面元素
3. 定义Content区域，通过`@RenderBody()`来提供Content占位符

【注】：默认情况下，每个布局必须调用RenderBody，无论在何处调用RenderBody，都会显示视图的内容。

## 指定视图
视图具有Layout属性，可以指定使用不同的布局视图，指定布局可以使用完整路径或部分名称
```cs
// 完整路径：/Views/Shared/_Layout.cshtml 或 /Pages/Shared/_Layout.cshtml

// 部分名称：_Layout

// _ViewStart.cshtml下指定默认的布局视图
@{
    Layout = "_Layout";
}
```

## 导入共享指令
视图和页面可以使用Razor指令来导入命名空间并使用依赖项注入，一般在文件_ViewImports.cshtml下。该文件支持以下指令：
- @addTagHelper
- @removeTagHelper
- @tagHelperPrefix
- @using
- @model
- @inherits
- @inject
- @namespace

【注】：该文件不支持函数和节定义等其它Razor功能
```cs
// _ViewImports.cshtml默认内容如下
@using MvcDemo
@using MvcDemo.Models
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
```
_ViewImports.cshtml文件一般放置在Pages/Views文件夹下，但其可以放置在任何文件夹中，在此情况下，该文件仅应用于当前文件夹及其子文件夹中的页面或视图。

如果在文件层次结构中找到多个_ViewImports.cshtml文件，则指令的组合行为如下所示：
- @addTagHelper/@removeTagHelper：按顺序全部运行
- @tagHelperPrefix：最接近视图的文件会替代任何其它文件
- @model：最接近视图的文件会替代任何其他文件
- @inherits：最接近视图的文件会替代任何其他文件
- @using：全部包括在内；忽略重复项
- @inject：针对每个属性，最接近视图的属性会替代具有相同属性名的任何其他属性

## 取消布局
在视图中，通过指定Layout属性可以取消或替换布局
```cs
@{
    ViewData["Title"] = "Home Page";
    Layout = null;
}

<div class="text-center">
    <h1 class="display-4">Welcome</h1>
    <p>Learn about <a href="https://docs.microsoft.com/aspnet/core">building Web apps with ASP.NET Core</a>.</p>
</div>
```
取消布局后，所有的效果均会消失，包括CSS样式等。