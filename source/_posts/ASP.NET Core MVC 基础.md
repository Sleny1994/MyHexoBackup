---
title: ASP.NET Core MVC 基础
date: 2023-08-16
tags:
- CSharp
- NET
categories:
- CSharp
---

## MVC的定义
MVC（Model View Controller）模型-视图-控制器。
- M：处理数据的存取 - 包含一组数据的类和管理该数据的逻辑信息
- V：处理数据的显示 - 包含显示逻辑，用于显示Controller提供给它的模型中的数据
- C：处理用户的交互 - 处理HTTP请求，调用模型，选择一个视图来呈现该模型
### Model
模型是描述用户界面上需要渲染的数据或是部分数据，但需要区分实体和模型的概念。</br>
Entity-实体，是业务逻辑中使用的数据结构，一般与数据库中对应的表一致；</br>
Model-模型，是经过转化处理的页面可接收的数据，比如数据库中的ID等敏感信息、bool类型、日期类型的转换等，均不适合直接绑定实体到页面，而需要转换成模型进行展示。
#### 命名规则
- 符合类命名规范
- 标识符必须以字母或下划线_开头
- 使用Pascal规则命名类型，即首字母大写
- 使用能够反应类功能的词或词语
- 不要使用I、C、_等特定含义的前缀
- 自定义异常类应以Exception结尾
- 文件名要能反应类的内容，最好是和类同名
### View
#### 视图绑定模型
```c#
@model  MvcDemo.Models.Student
@{

}
<div>
    <span>学号：</span>
    <span>@Model.Id</span>
</div>
<div>
    <span>姓名：</span>
    <span>@Model.Name</span>
</div>
<div>
    <span>年龄：</span>
    <span>@Model.Age</span>
</div>
<div>
    <span>性别：</span>
    <span>@Model.Sex</span>
</div>
```

### Controller
一般不负责具体的逻辑业务和逻辑实现，但负责接收和响应请求。</br>
主要作用：
- 接收请求并解析参数
- 调用Service执行具体的业务代码（可能包含参数校验）
- 捕获业务逻辑异常做出反馈
- 业务逻辑执行成功做出响应
#### Action
控制器中每个Public方法均可作为HTTP终结点调用，称之为Action，通常表示一个请求响应，默认返回一个IActionResult，表示一个页面，同时也可以返回其它数据类型，如String、int等。
```c#
using Microsoft.AspNetCore.Mvc;

namespace MvcDemo.Controllers
{
    public class HelloController : Controller
    {
        public IActionResult Index()
        {
            return View();
        }

        public string Welcome()
        {
            return "Hello World.";
        }
    }
}
```
#### 控制器视图的数据传递
常见的方法：
- ViewData，一个Key/Value键值对集合，通过ViewData可以方便的进行数据对象的存储和获取，只是ViewData获取的对象是Object类型需要进行一定的类型转换
- ViewBag，一定Dynamic类型对象，需要在运行时进行解析操作
- Model，对强类型视图，既可以接收参数也可以回传数据
- TempData，支持页面跳转数据传递，但也只支持一次页面跳转
##### ViewData
定义：是控制器中一个ViewDataDictionary类型的属性，用来存储Key/Value的字典集合，在控制器中可以直接使用。
1. ViewData特征
- ViewData是一个继承自ViewDataDictionary类的Dictionary对象，用来从Controller向对应的View传递值
- ViewData的只在当前的请求中有效，生命周期和View相同，其值不能在多个请求中共享
- 在重定向（Redirection）后，ViewData中存储的变量值将变为Null
- 在取出ViewData中的变量值时，必须进行合适的类型转换（隐式或显示）和空值检查
2. ViewData示例
- 首先在Controller中对ViewData赋值
```c#
public IActionResult Test()
{
    ViewData.Add("Name", "Sleny");
    ViewData.Add("Age", 18);
    return View();
}
```
- 在View中对ViewData中的值进行获取，格式为`@ViewData[Key]`
```c#
<h1>欢迎光临！~</h1>
<div>
    <span>姓名：</span>
    <span>@ViewData["Name"]</span>
</div>
<div>
    <span>年龄：</span>
    <span>@ViewData["Age"]</span>
</div>
```
##### ViewBag
定义：是一个动态类型变量（Dynamic），是C#4.0引入的新特性，变量类型会在运行时进行解析
1. ViewBag特征
- ViewBag基本上是ViewData的包装，也是从Controller向View传值
- ViewBag只在当前的请求中有效
- 在重定向（Redirection）后，ViewBag中存储的变量值将变为Null
- 因为其是动态类型，所以取值时不需要进行类型转换
2. ViewBag示例
- 首先在Controller中对ViewBag进行赋值
```c#
public IActionResult Test2()
{
    ViewBag.Name = "Sleny";
    ViewBag.Age = 25;
    return View();
}
```
- 在View中对ViewBag中的值进行获取，格式为`ViewBag.属性名`
```c#
@{
    var name = ViewBag.Name;
    var age = ViewBag.Age;
    name = name + "Zl";
    age = age + 1;
}
<h1>欢迎光临！~</h1>
<div>
    <span>姓名：</span>
    <span>@ViewBag.Name</span>
</div>
<div>
    <span>年龄：</span>
    <span>@ViewBag.Age</span>
</div>
<div>
    <span>姓名：</span>
    <span>@name</span>
</div>
<div>
    <span>年龄：</span>
    <span>@age</span>
</div>
```
##### Model
定义：主要作用就是在Controller和View之间进行数据交互
1. Model特征
在控制器中初始化模型数据，然后通过View(Model)方法将创建的模型数据传递给视图
```c#
using Microsoft.AspNetCore.Mvc;
using MvcDemo.Models;

namespace MvcDemo.Controllers
{
    public class HelloController : Controller
    {
        public IActionResult Index()
        {  
            //创建的数据模型
            var student = new Student()
            {
                Id = 1,
                Name = "Job",
                Age = 18,
                Sex = "男"
            };
            // 传递给视图
            return View(student);
        }

        public string Welcome()
        {
            return "Hello World.";
        }
    }
}
```
2. Model示例
- 首先创建模型
```c#
// Model下创建模型数据
public class Student
{
    public int Id { get; set; }

    public string Name { get; set; }

    public int Age { get; set; }

    public string Sex { get; set; }
}
```
- 控制器实例化模型
```cs
// Controller下编写实例化代码
public IActionResult Index()
{
    var student = new Student()
    {
        Id = 1,
        Name = "Sleny",
        Age = 22,
        Sex = "男"
    };

    return View(student);
}
```
- 视图指定模型
```cs
// Views创建视图，并指定模型
// @Model 完整类名
// @Model.属性名

@model  MvcDemo.Models.Student
<h1>欢迎光临！~</h1>
<div>
    <span>学号：</span>
    <span>@Model.Id</span>
</div>
<div>
    <span>姓名：</span>
    <span>@Model.Name</span>
</div>
<div>
    <span>年龄：</span>
    <span>@Model.Age</span>
</div>
<div>
    <span>性别：</span>
    <span>@Model.Sex</span>
</div>
```
##### TempData
定义：ViewData和ViewBag都是一次性传递数据，若跳转其它页面，则无法进行获取，但TempData支持页面跳转后仍可获取内容
1. TempData特征
- 类型为TempDataDictionary
- 用于把数据从一个Action方法传到另一个Action方法，两个Action可以不在同一个Controller中，也可在同一个中
2. TempData示例
- 首先在Controller中创建两个视图Test3和Test4，分别创建ViewData和TempData，并赋予Name名称的值，再让页面从Test3 -> Test4，最后在Test4中分别获取两个值
```cs
public IActionResult Test3()
{
    ViewData.Add("Name", "ViewData");
    TempData.Add("Name", "TempData");
    return View();
}

public IActionResult Test4()
{
    return View();
}
```
- 其次在Views下创建两个页面
```cs
// Test3.cshtml
<a href="~/Hello/Test4">跳转Test4</a>

// Test4.cshtml
<h1>欢迎光临！</h1>

<div>
    <span>姓名：</span>
    <span>@ViewData["Name"]</span>
</div>
<div>
    <span>姓名：</span>
    <span>@TempData["Name"]</span>
</div>
```
- 最终可以发现：(1) ViewData在页面传递后，前一个Action添加的键值已经被清除；(2) TempData中的值在页面跳转后仍然保存；(3) TempData在第二次请求后会被清空，第三次请求则获取不到

## 默认路由
当通过模板创建项目时，默认会支持MVC路由，但如果创建的是空项目时，则需要手动添加，主要有以下三行代码。
1. 注入支持控制器视图服务
```c#
// Add services to the container.
builder.Services.AddControllersWithViews();
```
2. 使用路由
```c#
app.UseRouting();
```
3. 默认路由配置，即缺省配置
```c#
app.MapControllerRoute(
    name: "default",
    pattern: "{controller=Home}/{action=Index}/{id?}");
```

## 默认约定
1. Controller的约定
- 所有的Controller必须放在Controllers文件夹中，并以[Name]Controller的方式命名，如：HomeController
- 每个Controller都对应View中的一个文件夹，文件夹的名称与Controller名相同，如：Home
- Controller中的方法名都对应一个View视图且View的名字和Action的名字相同
- 控制器必须是非静态类，并且要实现IController接口，默认继承自Controller
- Controller类型可以放在其他项目中
2. View的约定
- 所有视图必须放到Views目录下
- 不同控制器的视图用文件夹进行分隔，每个控制器都对应一个视图目录
- 一般视图名字跟控制器的Action相对应
- 多个控制器公共的视图放到Shared，如公共的错误页、列表模板页、表单模板页等

## 页面布局
页眉与页脚均由Layout布局模板生成，如果不需要加载布局模板内容或者指定新的Layout布局模板，可以在页面中进行设置
```c#
@{
    Layout = null; //不加载布局模板的内容
}

<h1>欢迎光临！~</h1>
```

## 参数的传递
### 接收URL参数
1. 参数名称自动匹配</br>
如果Action的形参名称和QueryString的Key一致，则MVC框架会自动绑定参数的值，不用手动获取
```c#
public IActionResult ShowStudent(int id, string name, int age, string sex)
{
    var student = new Student()
    { Id = id, Name = name, Age = age, Sex = sex };
    return Json(student);
}

// URL访问：http://localhost:5228/Hello/ShowStudent?id=2&name=Lily&age=19&sex=%E5%A5%B3

// --->
// {"id":2,"name":"Lily","age":19,"sex":"\u5973"}
// 如若中文被重新编码，则可以通过在Program.cs中添加如下代码来修复
// Add services to the container.
builder.Services.AddControllersWithViews().AddJsonOptions(options =>
{
    options.JsonSerializerOptions.Encoder = JavaScriptEncoder.Create(UnicodeRanges.All);
});
```
【注】：如果参数绑定的名称和QueryString的Key不一致，可以使用FromQueryAttribute强制指定绑定的Key的名称</br>
2. Request.Query获取参数</br>
```c#
public IActionResult ShowStudent2()
{
    var id = Request.Query["id"];
    var name = Request.Query["name"];
    var age = Request.Query["age"];
    var sex = Request.Query["sex"];
    var student = new Student()
    {
        Id = string.IsNullOrEmpty(id) ? 0 : int.Parse(id),
        Name = name,
        Age = string.IsNullOrEmpty(age) ? 0 : int.Parse(age),
        Sex = sex
    };
    return Json(student);
}
```
3. 通过路由获取参数
```c#
[Route("Hello/ShowStudent3/{id}/{name}/{age}/{sex}")]
public IActionResult ShowStudent3(int id, string name, int age, string sex)
{
    var student = new Student()
    {
        Id = id,
        Name = name,
        Age = age,
        Sex = sex
    };
    return Json(student);
}
```
【注】：如果Action的形参名称和RouteAttribute模板中的名称不同，可以使用FromRoute强制指定解析的名称。

### 接收Body参数
Request.Body是一个Stream对象，通过获取流对象中的内容，然后进行转化，就可以获取参数。
```c#

[HttpPost]
public IActionResult TestBody()
{
    Request.EnableBuffering();
    var body = "";
    var stream = Request.Body;
    if (stream != null)
    {
        stream.Seek(0, SeekOrigin.Begin);
        using (var reader = new StreamReader(stream, Encoding.UTF8, true, 1024, true))
        {
            body = reader.ReadToEnd();
        }
        stream.Seek(0, SeekOrigin.Begin);
    }

    var student = JsonConvert.DeserializeObject<Student>(body);
    return Json(student);
}
```
通过Body获取，然后JsonConvert进行反序列化，前提是Body内容是JOSN格式，否则不能进行反序列化，主要应用于接口调用，Ajax方式请求等。

### 接收Form表单
最常用的就是Form表单传递参数，客户端将所有需要传递的内容包括在Form表单内容，在服务端Action中通过Request.Form["Kye"]进行获取。
```c#
// Hello/Form.cshtml
<form action="~/Hello/Save" method="post">
    <div style="margin:10px;">
        <span>学号：</span>
        <input type="text" name="Id" />
    </div>
    <div style="margin:10px;">
        <span>姓名：</span>
        <input type="text" name="Name" />
    </div style="margin:10px;">
    <div style="margin:10px;">
        <span>年龄：</span>
        <input type="text" name="Age" />
    </div>
    <div style="margin:10px;">
        <span>性别：</span>
        <input type="text" name="Sex" />
    </div>
    <div style="margin:10px;">
        <input type="submit" name="submit" value="保存" />
    </div>
</form>

// Controllers/HelloController.cs
// 与Form.cshtml对应，否则会提示页面不存在404
public IActionResult Form()
{
    return View();
}

[HttpPost]
public IActionResult Save()
{

    var id = Request.Form["Id"];
    var name = Request.Form["Name"];
    var age = Request.Form["Age"];
    var sex = Request.Form["Sex"];
    var student = new Student()
    {
        Id = string.IsNullOrEmpty(id) ? 0 : int.Parse(id),
        Name = name,
        Age = string.IsNullOrEmpty(age) ? 0 : int.Parse(age),
        Sex = sex
    };
    return Json(student);
}
```

### 通过模型接收参数
一般通常为了简便，会直接采用模型来接收参数，如果模型的属性名和参数的Key一致，则可以自动匹配
```c#
public IActionResult ShowStudent4(Student student)
{
    return Json(student);
}


[HttpPost]
public IActionResult Save2(Student student)
{
    return Json(student);
}
```
【注】：Get/Post方法均可以采用模型接收参数