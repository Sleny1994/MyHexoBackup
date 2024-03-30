---
title: ASP.NET Core MVC 数据库
date: 2023-08-21
tags:
- CSharp
- NET
- SQL
categories:
- CSharp
---

[*注：本文以DB First为例*]

## EntityFrameworkCore
Entity Framework(EF) Core是轻量化、可扩展、开源和跨平台版的常用数据库访问技术</br>
特点：
- 使.NET开发者可以使用.NET对象处理数据库
- 无需像以往那样编写大部分数据访问代码

## 数据库的创建
一般分为两种方式：
1. Code First
2. DB First
```sql
CREATE TABLE [dbo].[Demo](
    [Id] [bigint] IDENTITY(1,1) NOT NULL,
    [Name] [varchar](200) NULL,
    [ReleaseDate] [datetime] NULL,
    [LeadingRole] [varchar](100) NULL,
    [Genre] [varchar](100) NULL,
    [Price] [money] NULL,
    [CreateTime] [datetime] NULL,
    [CreateUser] [varchar](50) NULL,
    [LastEditTime] [datetime] NULL,
    [LastEditUser] [varchar](50) NULL,
 CONSTRAINT [PK_Movie] PRIMARY KEY [Id]);
```

## 构造测试数据
```sql
insert into Demo (Name, ReleaseDate, LeadingRole, Genre, Price, CreateTime, CreateUser, LastEditTime, LastEditUser)
values ('满江红', '2023-03-03T00:00:00', '易烊千玺', '剧情，悬疑，搞笑', 56, '2023-05-01T00:00:00', 'Sleny', '2023-05-01T00:00:00', 'Sleny')
```

## 创建数据实体
数据库和数据表创建后，需要在项目中创建与其对应的实体类DemoEntity.cs
```c#
// 一般建议与数据库中的表的Colum名称所对应
// Entities/DemoEntity.cs
namespace MvcDemo.Entities
{
    public class DemoEntity
    {
        public long Id { get; set; }
        public string Name { get; set; }
        public DateTime ReleaseDate { get; set; }
        public string LeadingRole { get; set; }
        public string Genre { get; set; }
        public decimal Price { get; set; }
        public DateTime CreateTime { get; set; }
        public string CreateUser { get; set; }
        public DateTime LastEditTime { get; set; }
        public string LastEditUser { get; set; }
    }
}
```

## 创建业务模型
数据实体用于和数据表进行映射，业务模型用于在控制器和视图之间进行数据交互，此处的字段名称可以和Entity中的不一致。
```c#
namespace MvcDemo.Models
{
    public class Demo
    {
        public long Id { get; set; }
        public string Name { get; set; }
        public DateTime ReleaseDate { get; set; }
        public string LeadingRole { get; set; }
        public string Genre { get; set; }
        public decimal Price { get; set; }
    }
}
```

## EF的安装与配置
1. 通过Nuget包管理器进行EF依赖包的安装
    - Microsoft.EntityFrameworkCore
    - Microsoft.EntityFrameworkCore.SqlServer
2. 配置SQL Server连接字符串
```json
// 在appsettings.json中添加数据库连接字符串配置
{
  "ConnectionStrings": {
    "Default": "Server=192.168.152.135;Database=DemoDB;User Id=sa;Password=admin@123;Trusted_Connection=True;TrustServerCertificate=true;MultipleActiveResultSets=true"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```
3. 创建DbContext数据上下文
EF通过DbContext操作数据，所以需要创建属于整个项目的数据上下文，并继承于DbContext
```c#
// Entities/DemoDbContext
using Microsoft.EntityFrameworkCore;

namespace MvcDemo.Entities
{
    public class DemoDbContext:DbContext
    {
        public DemoDbContext(DbContextOptions<DemoDbContext> options)
            : base(options)
        {
        }

        public DbSet<DemoEntity> Demo { get; set; }

        // 将实体和数据表进行映射
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            base.OnModelCreating(modelBuilder);
            modelBuilder.Entity<DemoEntity>().ToTable("Demo");
        }
    }
}
```
4. 在Program.cs中，注入EF框架，并将appSetting.json配置的数据库连接字符串传递进去
```c#
var builder = WebApplication.CreateBuilder(args);
//注入数据库框架
builder.Services.AddDbContext<DemoDbContext>(options => options.UseSqlServer(builder.Configuration.GetConnectionString("Default")));
```

## 创建控制器DemoController
创建DemoController，在Index中查询信息，并将数据传递给视图
```c#
// Controllers/DemoController.cs
using Microsoft.AspNetCore.Mvc;
using MvcDemo.Entities;
using MvcDemo.Models;

namespace MvcDemo.Controllers
{
    public class DemoController : Controller
    {
        private DemoDbContext demoDb;

        public DemoController(DemoDbContext demoDb)
        {
            this.demoDb = demoDb;
        }
        public IActionResult Index()
        {
            //1.获取数据库实体
            var entities = demoDb.Demo.Skip(0).Take(20).ToList();
            //2.将实体转换成业务模型
            var Demos = entities.Select(e => new Demo()
            {
                Id = e.Id,
                Name = e.Name,
                Genre = e.Genre,
                LeadingRole = e.LeadingRole,
                Price = e.Price,
                ReleaseDate = e.ReleaseDate,
            }).ToList();
            ViewData.Add("Demos", Demos);
            return View();
        }
    }
}
```

## 创建视图Index.cshtml
```c#
// Views/Demo/Index.cshtml
@{
    ViewData["Title"] = "Index";
    var demos = ViewData["Demos"] as List<Demo>;
}

<h1>Index</h1>
<table class="table">
    <thead>
        <tr>
            <td>序号</td>
            <td>电影名称</td>
            <td>类型</td>
            <td>主演</td>
            <td>上映时间</td>
            <td>票价</td>
            <td>功能</td>
        </tr>
    </thead>
@for (var i = 0; i < demos.Count; i++)
    {
        var demo = demos[i];
        <tr>
        <td>@demo.Id</td>
        <td>@demo.Name</td>
        <td>@demo.Genre</td>
        <td>@demo.LeadingRole</td>
        <td>@demo.ReleaseDate</td>
        <td>@demo.Price</td>
        <td><a href="/Movie/Edit/@demo.Id">编辑</a> | <a href="/Movie/Delete/@demo.Id">删除</a></td>
    </tr>
}
</table>
```