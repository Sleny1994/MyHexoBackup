---
title: ASP.NET Core MVC HTML辅助标签
date: 2023-08-23
tags:
- CSharp
- NET
categories:
- CSharp
---

## 超链接
- 超链接在HTML中是a标签，在.NET中可以通过@Html.ActionLink或者@Html.RouteLink实现
- ActionLink方法具有7个重载，各个参数的含义如下：
    1. linkText：超链接显示文本，不可为空
    2. actionName：链接的目标方法名称
    3. controllerName：链接的控制器名称，可跨控制器跳转
    4. routeValues：object类型的路由数据，可以接收匿名对象，如：new{
        id = 1, name = "sleny"
    }
    5. htmlAttributes：超链接Html属性，如：style, class, width, height等；可以接收匿名对象，如：new{
        style = "corlor:red;width:100px", @class = "link"
    }
    6. protocol：协议，如：http、https等
    7. hostname：主机服务器名称
    8. fragment：URL片段名称（定位点名称）
- RouteLink不支持如ActionLink中指定action，controller，只支持指定路由名称，其具有5个重载参数，含义如下：
    1. linkText：超链接显示文本，不可为空
    2. routeName：路由名称
    3. routeValues：object类型的路由数据，可以接收匿名对象，如：new{
        id = 1, name = "Sleny"
    }
    4. htmlAttributes：超链接Html属性，如：style，class，width，height等；可以接收匿名对象，如：new{
        style = "corlor:red;width:100px", @class = "link"
    }	
### 示例
```c#
// ActionLink
@*
    <a class="link" href="/Student/Index/1?name=%E5%85%AC%E5%AD%90%E5%B0%8F%E5%85%AD" style="color:red;width:100px">点击跳转</a>
*@
// ==等价于==>
@Html.ActionLink("点击跳转","Index","Student",new {id=1,name="Sleny"},new {style="color:red;width:100px",@class="link"})

// RouteLink
<!--以下RouteLink和ActionLink输出结果一致-->
@Html.RouteLink("点击跳转","Default",new {controller="Student",action="Index", id=1,name="Sleny"},new {style="color:red;width:100px",@class="link"})
```
【注】：因为class是C#的关键字，所有如果需要表示样式，需要添加@前缀

## Form标签
form标签主要用于表单的提交，form辅助标签用@Html.BeginForm或者@Html.BeginRouteForm表示，且用@using包裹，用于表示form表单的起始和结束
```c#
// 空表单
<form action="/User/Save" method="post">
...
</form>

// Html辅助标签
@using(Html.BeginForm("Save","User",FormMethod.Post))
{
	...
}
```
@Html.BeginForm重载参数如下所示：
1. actionName：链接的目标方法名称
2. controllerName：链接的控制器名称，可跨控制器跳转
3. method：是form请求传递参数的方式，是一个枚举类型FormMethod，有Get和Post两种方式
4. routeVaules：object类型的路由数据，可以接收匿名对象，如：new{id = 1, name = "Sleny"}
5. htmlAttributes：超链接Html属性，如：style，class，width，height等，可以接收匿名对象，如：new {style="color:red;width=100px",@class="link"}
6. antiforgety：是否将身份验证令牌添加到表单中，有助于防止请求伪造
- @Html.BeginRouteForm和@Html.BeginForm一致，主要是接收路由参数，不接受字符串格式的action和controller

## 文本框和Label
- 文本框通过@Html.TextBox方法实现，并且在form表单标签内，共有5个重载，主要参数如下所示：
    1. expression：表达式名称，一般用于表示文本框的name
    2. value：文本框中的值，可以绑定模型中的值
    3. htmlAttributes：超链接Html属性，如：style，class，width，height等，可以接收匿名对象，如：new {style="color:red;width:100px",@class="link"}
    4. format：格式
- label主要用于显示只读文本，共有3个重载，主要参数如下所示：
    1. expression：表达式名称，一般用于表示当时显示的文本是对应于哪个控件
    2. labelText：要显示的文本内容
    3. htmlAttributes：超链接Html属性，如：style，class，width，height等，可以接收匿名对象，如：new {style="color:red;width:100px",@class="link"}
### 示例
```c#
@Html.Label("Id","User Id",new { style="width:90px;"});
@Html.TextBox("Id",Model.Id)
<br />
<br />
@Html.Label("Name","User Name",new { style="width:90px;"})
@Html.TextBox("Name",Model.Name)
<br />
<br />
@Html.Label("Mail","E-Mail",new { style="width:90px;"})
@Html.TextBox("Mail",Model.Email)
```

## 密码框
主要用于输入密码，相当于`input type = "password"`，用@Html.Password辅助标签实现，有2个重载，主要参数如下所示：
1. expression：表达式名称，一般用于表示文本框的name
2. value：密码框中的值，可以绑定模型中的值，也可以为空
3. htmlAttributes：超链接Html属性，如：style，class，width，height等，可以接收匿名对象，如：new { placeHolder="请输入密码"}
### 示例
```c#
@Html.Label("Pwd","Password",new { style="width:90px;"})
@Html.Password("Pwd",null,new {placeHolder="请输入密码"})
```

## 单选框和复选框
- 单选框用@Html.RadioButton表示，有3个重载，主要参数如下所示：
    1. expression：表达式名称，一般用于表示文本框的name
    2. value：单选框的值
    3. isChecked：是否默认选中
    4. htmlAttributes：超链接Html属性，如：style，class，width，height等，可以接收匿名对象
- 复选框用@Html.CheckBox表示，有3个重载，主要参数如下所示：
    1. expression：表达式名称，一般用于表示文本框的name
    2. isChecked：是否默认选中
    3. htmlAttributes：超链接Html属性，如：style，class，width，height等，可以接收匿名对象，如：new { value="123" }
### 示例
```c#
// 单选框
@Html.Label("Sex","性别",new { style="width:90px;"})
@Html.RadioButton("Sex","Male",false) <span>男</span>
@Html.RadioButton("Sex", "FeMale", false) <span>女</span>

// 复选框
@Html.Label("Hobby","爱好",new { style="width:90px;"})
@Html.CheckBox("Hobby",false,new { value="football" }) <span>足球</span>
@Html.CheckBox("Hobby",false,new { value="basketball" }) <span>篮球</span>
@Html.CheckBox("Hobby",false,new { value="pingpang" }) <span>乒乓球</span>
```

## 文本域
主要用于大量文本内容输入，一般显示为多行多列的文本输入框，共有4个重载，参数如下所示：
1. expression：表达式名称，一般用于表示文本框的name
2. value：文本域内容
3. rows：行数
4. columns：列数
5. htmlAttributes：超链接Html属性，如：style，class，width，height等，可以接收匿名对象
### 示例
```c#
@Html.Label("Description","简介",new { style="width:90px;"})
@Html.TextArea("Description",null,3,20,new {})
```

## 综合示例
```c#
@model MvcDemo.Models.User;
@Html.ActionLink("点击跳转","Index","Student",new {id=1,name="Sleny"},new {style="color:red;width:100px",@class="link"})
<br />
@Html.RouteLink("点击跳转","Default",new {controller="Student",action="Index", id=1,name="Sleny"},new {style="color:red;width:100px",@class="link"})
@using (Html.BeginForm("Save","User",FormMethod.Post))
{
    @Html.Label("Id","User Id",new { style="width:90px;"});
    @Html.TextBox("Id",Model.Id)
    <br />
    <br />
    @Html.Label("Name","User Name",new { style="width:90px;"})
    @Html.TextBox("Name",Model.Name)
    <br />
    <br />
    @Html.Label("Mail","E-Mail",new { style="width:90px;"})
    @Html.TextBox("Mail",Model.Email)

    <br />
    <br />
    @Html.Label("Pwd","Password",new { style="width:90px;"})
    @Html.Password("Pwd",null,new {placeHolder="请输入密码"})
    <br />
    <br />
    @Html.Label("Sex","性别",new { style="width:90px;"})
    @Html.RadioButton("Sex","Male",false) <span>男</span>
    @Html.RadioButton("Sex", "FeMale", false) <span>女</span>
    <br />
    <br />
    @Html.Label("Hobby","爱好",new { style="width:90px;"})
    @Html.CheckBox("Hobby",false,new { value="football" }) <span>足球</span>
    @Html.CheckBox("Hobby",false,new { value="basketball" }) <span>篮球</span>
    @Html.CheckBox("Hobby",false,new { value="pingpang" }) <span>乒乓球</span>
    <br />
    <br />
    @Html.Label("Description","简介",new { style="width:90px;vertical-align:top;"})
    @Html.TextArea("Description",null,3,20,new {})
}
```
和上述代码一致的Html原生代码如下所示：
```html
<a class="link" href="/Student/Index/1?name=%E5%85%AC%E5%AD%90%E5%B0%8F%E5%85%AD" style="color:red;width:100px">点击跳转</a>
<br>
<a class="link" href="/Student/Index/1?name=%E5%85%AC%E5%AD%90%E5%B0%8F%E5%85%AD" style="color:red;width:100px">点击跳转</a>
<form action="/User/Save" method="post">
    <label for="Id" style="width:90px;">User Id</label>
    <input data-val="true" data-val-required="The Id field is required." id="Id" name="Id" type="text" value="1">    <br>
    <br>
    <label for="Name" style="width:90px;">User Name</label>
    <input data-val="true" data-val-required="The Name field is required." id="Name" name="Name" type="text" value="Sleny">    <br>
    <br>
    <label for="Mail" style="width:90px;">E-Mail</label>
    <input id="Mail" name="Mail" type="text" value="Sleny.Zl@qq.com">
    <br>
    <br>
    <label for="Pwd" style="width:90px;">Password</label>
    <input id="Pwd" name="Pwd" placeholder="请输入密码" type="password">
    <br>
    <br>
    <label for="Sex" style="width:90px;">性别</label>
    <input id="Sex" name="Sex" type="radio" value="Male"> <span>男</span>
    <input id="Sex" name="Sex" type="radio" value="FeMale"> <span>女</span>
    <br>
    <br>
    <label for="Hobby" style="width:90px;">爱好</label>
    <input id="Hobby" name="Hobby" type="checkbox" value="football"> <span>足球</span>
    <input id="Hobby" name="Hobby" type="checkbox" value="basketball"> <span>篮球</span>
    <input id="Hobby" name="Hobby" type="checkbox" value="pingpang"> <span>乒乓球</span>
    <br>
    <br>
    <label for="Description" style="width:90px;vertical-align:top;">简介</label><textarea cols="20" id="Description" name="Description" rows="3"></textarea><input name="__RequestVerificationToken" type="hidden" value="CfDJ8BFjNYa4u1JEvSFtRevYkrqryxwT0_r_eRNKsK4VToEBDTdl_uU7Qt7Z3_2Eu8xd0eiz_eMhzkSssfX-kTgLnui_qq7uXql9na9LwfkmvViszQE499vE9vrap83T6vhV16A9nEK6PPY6gzpPMlnWiVc"><input name="Hobby" type="hidden" value="false"><input name="Hobby" type="hidden" value="false"><input name="Hobby" type="hidden" value="false">
</form>

```

## 下拉框和多选列表框
### 下拉框
下拉框主要用于显示多个信息，供用户选择单个选项，用@Html.DropDownList表示，有6个重载，参数如下所示：
1. expression：表达式名称，一般用于文本框的name
2. selectList：下拉列表的数据源，IEnumerable<SelectListItem>类型，一般用SelectList实现
3. optionLabel：默认显示的文本
4. htmlAttributes：超链接Html属性，如：style，class，width，height等，可以接收匿名对象
#### 示例
- 首先准备数据源，一般从数据库中获取
```c#
List<City> cityList = new List<City>
{
    new City() { Id = 1, Name = "北京" },
    new City() { Id = 2, Name = "上海" },
    new City() { Id = 3, Name = "广州" },
    new City() { Id = 4, Name = "深圳" }
};
var citys = new SelectList(cityList, "Id", "Name");
ViewBag.Citys = citys;
``` 
- 其中SelectList是IEnumerable<SelectListItem>的实现类，传入list，并指定value和text即可
```c#
@Html.Label("City","城市",new { style="width:90px;"})
@Html.DropDownList("City", ViewBag.Citys,"请选择",new {style="width:120px;"})
```
### 多选列表框
多选列表框主要用于显示多个信息，用户可以选择多个选项，用@Html.ListBox表示，共有3个重载，参数如下所示：
1. expression：表达式名称，一般用于表示文本框的name
2. selectList：下拉列表的数据源，IEnumerable<SelectListItem>类型，一般用于SelectList实现
3. htmlAttributes：超链接Html属性，如：style，class，width，height等，可以接收匿名对象
#### 示例
```c#
@Html.Label("MultiCity","多选城市",new { style="width:90px;vertical-align:top;"})
@Html.ListBox("MultiCity", ViewBag.Citys,new {style="width:120px;"})
```
### 对应的HTML代码
```html
<label for="City" style="width:90px;">城市</label>
<select id="City" name="City" style="width:120px;">
    <option value="">请选择</option>
    <option value="1">北京</option>
    <option value="2">上海</option>
    <option value="3">广州</option>
    <option value="4">深圳</option>
</select>
<br>
<br>
<label for="MultiCity" style="width:90px;vertical-align:top;">多选城市</label>
<select id="MultiCity" multiple="multiple" name="MultiCity" style="width:120px;">
    <option value="1">北京</option>
    <option value="2">上海</option>
    <option value="3">广州</option>
    <option value="4">深圳</option>
</select>
```

## Html转义
有时候在输出特定格式的内容时，C#会进行转义，从而实现不了想要的效果，则需要使用@Html.Raw方法，共有2个重载，主要参数如下：
1. value：需要是输出的内容，有string和TModel两种类型
```c#
@Html.Raw("<font color='red'>大家好，这是测试Raw输出</font>")
```
上述方法会输出原生Html`<font color='red'>大家好，这是测试Raw输出</font>`，在页面上显示红色的文本内容，而不是输出转义后的内容。

## 模型校验
- 在MVC中，验证发生在客户端和服务端
- .NET已经将验证抽象化为验证属性，包含验证代码，可以直接使用

常用的内置验证属性如下所示：
- [CreditCard]：验证属性是否具有信用卡格式。
- [Compare]：验证某个模型中的两个属性是否匹配。
- [EmailAddress]：验证属性是否具有电子邮件格式。
- [Phone]：验证属性是否具有电话格式。
- [Range]：验证属性值是否落在给定范围内。
- [RegularExpression]：验证数据是否与指定的正则表达式匹配。
- [Required]：将属性设置为必需属性。
- [StringLength]：验证字符串属性是否最多具有给定的最大长度。
- [Url]：验证属性是否具有 URL 格式。
### 校验步骤
1. 模型中增加校验特性
```c#
// Models/User.cs
using System.ComponentModel.DataAnnotations;

namespace MvcDemo.Models
{
    public class User
    {
        [Required(ErrorMessage ="Id不能为空")]
        [Range(0, 100,ErrorMessage ="Id必须从0到100之内")]
        public int Id { get; set; }

        [Required(ErrorMessage ="用户名称不能为空")]
        public string Name { get; set; }

        [Required(ErrorMessage ="邮箱不可为空")]
        [EmailAddress(ErrorMessage ="邮件不符合格式")]
        public string Email { get; set; }
    }
}

```
2. 引入校验分部视图
```cs
// <!--Edit.cshtml文件的底部增加以下引入部分视图-->
@section Scripts{
    @(await Html.PartialAsync("_ValidationScriptsPartial"))
}
```
3. 增加校验信息标签</br>
在Edit.cshtml需要显示校验信息的地方，增加@Html.ValidationMessageFor信息展示
```C#
// Views/Edit.cshtml
@using (Html.BeginForm("Save","User",FormMethod.Post))
{
    @Html.Label("Id","User Id",new { style="width:90px;"});
    @Html.TextBox("Id",Model.Id)
    @Html.ValidationMessageFor(p=>p.Id)
    <br />
    <br />
    @Html.Label("Name","User Name",new { style="width:90px;"})
    @Html.TextBox("Name",Model.Name)
    @Html.ValidationMessageFor(p=>p.Name)
    <br />
    <br />
    @Html.Label("Email","E-Mail",new { style="width:90px;"})
    @Html.TextBox("Email",Model.Email)
    @Html.ValidationMessageFor(p=>p.Email)
    <br />
    <br />
    <input type="submit" value="保存" class="btn btn-primary" />
}
```
【注】：模型校验要生效，则@Html.TextBox/@Html.Label/@Html.ValidationMessageFor，三者之间的Expression要相同，否则不会生效