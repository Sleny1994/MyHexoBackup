---
title: Entity Framework 使用
date: 2023-08-18
tags:
- CSharp
- NET
- SQL
- EF
categories:
- CSharp
---

[*注：本文以Code First为例*]

## 安装
从NuGet包管理器内搜索EF进行安装，涉及两个包`Microsoft.EntityFrameworkCore` | `Microsoft.EntityFrameworkCore.SqlServer`

## 定义连接字符串
```c#
// appsettings.json
  "ConnectionStrings": {
    "DemoDbConnection": "Server=(localdb)\\MSSQLLocalDB;
    Database=DemoDb;MultipleActiveResultSets=true"
}
```

## 创建数据库上下文
```c#
// Models/DemoDbContext.cs
using Microsoft.EntityFrameworkCore;
 
namespace Demo.Models {
 
    public class DemoDbContext: DbContext {
        public DemoDbContext (DbContextOptions< DemoDbContext > options)
            : base(options) { }
        public DbSet<User> Users { get; set; }
    }
}
```
- DbContext基类提供对Entity Framework Core底层功能的访问，而Users属性将提供User对数据库中对象的访问
- DemoDbCOntext派生自DbContext并添加了将用于读写应用程序数据的属性

## 配置实体框架核心
```c#
// Startup.cs在最新版中已替换为Program.cs
using BooksStore.Models;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
...
public void ConfigureServices(IServiceCollection services)
        {
            services.AddControllersWithViews();
            services.AddDbContext<DemoDbContext>(opts => {
                opts.UseSqlServer(
                   Configuration["ConnectionStrings:DemoDbConnection"]);
            });
        }
...
```
- Entity Framework Core配置AddDbContext方法，注册数据库上下文类，配置与数据库的关系
- UseSQLServer方法声明使用的数据库，并通过IConfiguration对象读取连接字符串

## 创建数据库
存储库模式是使用最广泛的模式之一，提供了一种一致的方式来访问数据库上下文类提供的功能。
```c#
// Models/DemoRepository.cs
using System.Linq;
 
namespace Demo.Models {
 
    public interface IDemoRepository {
        IQueryable<User> Users { get; }
    }
}
```
- 接口使用IQueryable<T>允许调用者获取User对象序
列
```c#
// Models/EFUsersRepository.cs
using System.Linq;
 
namespace Demo.Models {
 
    public class EFDemoRepository : IDemoRepository {
        private DemoDbContext context;
        public EFDemoRepository (DemoDbContext ctx) {
           context = ctx;
        }
        public IQueryable<User> Users => context.Users;
    }
}
```
- 在Program.cs中修改代码，为用作实现EFUsersRepository类的IDemoRepository接口创建服务
```c#
// Program.cs
public void ConfigureServices(IServiceCollection services)
        {
            services.AddControllersWithViews();
            services.AddDbContext<DemoDbContext>(opts => {
                opts.UseSqlServer(
                   Configuration["ConnectionStrings:DemoDbConnection"]);
            });
            services.AddScoped<IDemoRepository, EFDemoRepository>();
        }
```
- 该AddScoped方法创建一个服务，其中每个HTTP请求都获取自己的存储库对象

## 创建数据库迁移
从工具-NuGet包管理器-包管理器控制台PMC中输入命令，即可完成数据库及表的创建
```bash
Add-Migration InitialCreate
Update-Database
```
注：需要安装Microsoft.EntityFrameworkCore.Tools软件包

## 常见数据库
<table>
    <tr>
        <th> SQL </th>
        <th> Demo </th>
        <th> Package </th>
    </tr>
    <tr>
        <th> SQL Server | Azure SQL </th>
        <th> .UseSqlServer(connectionString) </th>
        <th> Microsoft.EntityFrameworkCore.SqlServer </th>
    </tr>
    <tr>
        <th> Azure Cosmos DB </th>
        <th> .UseCosmos(connectionString, DatabaseName) </th>
        <th> Microsoft.EntityFrameworkCore.Cosmos </th>
    </tr>
    <tr>
        <th> SQLite </th>
        <th> .UseSqlite(connectionString) </th>
        <th> Microsoft.EntityFrameworkCore.Sqlite </th>
    </tr>
    <tr>
        <th> EF Core In Memory DB </th>
        <th> .UseInMemoryDatabase(databaseName) </th>
        <th> Microsoft.EntityFrameworkCore.InMemory </th>
    </tr>
    <tr>
        <th> PostgreSQL </th>
        <th> .UseNpgsql(connectionString) </th>
        <th> Microsoft.EntityFrameworkCore.PostgreSQL </th>
    </tr>
    <tr>
        <th> MySQL | MariaDB </th>
        <th> .UseMySql(connectionString) </th>
        <th> Microsoft.EntityFrameworkCore.MySql </th>
    </tr>
    <tr>
        <th> Oracle </th>
        <th> .UseOracle(connectionString) </th>
        <th> Oracle.EntityFrameworkCore </th>
    </tr>
</table>
