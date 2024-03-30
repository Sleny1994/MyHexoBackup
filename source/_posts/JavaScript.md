---
title: JavaScript基础知识
date: 2023-12-15
tags:
- JavaScript
- jQuery
categories:
- JavaScript
---

## 基础语法

- 在head与body标签内可以调用执行JavaScript语言
- 调用使用方式如下所示：
```JavaScript
<script src="xx.js">
	// JavaScript Codes
	// src调用外部js文件
</script>
```
- 变量的定义使用var关键字，分号";"结尾
- 变量区分大小写

<!--more-->

## DOM操作
- DOM(Document Object Model)
- document.getElementById("id属性的值"); 通过id的值获取元素，返回值为id所在的HTML代码
- document.getElementsByTagName("标签名字"); 通过标签名获取元素，返回值为一个集合
- document.getElementsByName("name属性的值"); 通过name属性的值获取元素
- document.getElementsByClassName("类的名字"); 通过类的名字获取元素，返回值为一个集合
- document.querySelector("选择器"); 获取单个选择器所在的HTML代码
- document.querySelectorAll("选择器"); 获取多个选择器返回一个集合
```JavaScript
var wrap = document.querySelector(".wrap");
wrap.onclick = function(){
	wrap.style.cssText = "width:500px; height:800px; background-color:red;")	
}
wrap.onmousemove = function(){} //鼠标滑动时出现效果
wrap.onkeydown = function(){} //键盘按压时出现效果
``` 

## 输出数据
- document.write("xxx")
- console.log() 控制台输出日志
- window.alert("xxx") 弹窗

## 写入内容
- innerHTML
- innerText
```JavaScript
var box = document.getElementsByTagName("div");
	box[0].innerHTML = "<p></p>";    //要注意box是一个集合
	box[0].innerText = "修改的内容";
```
- 两者的区别在于，HTML会将写入的标签进行解析，而Text则是以文本方式直接输出

## 函数
- 关键字：function
- 有名函数：
```JavaScript
function name(){ 
	//codes 
	}
name()
```
- 函数表达式：
```JavaScript
var test = function(){

}
test()
```
- 匿名函数：
```JavaScript
function(){
	console.log("这是一个匿名函数")
}; // 该调用会报错

(function(){console.log("这是一个匿名函数") })(); //函数自执行
((function(){console.log("这是一个匿名函数") }));
!function(){console.log("这是一个匿名函数") }();
~function(){console.log("这是一个匿名函数") }();
-function(){console.log("这是一个匿名函数") }();
+function(){console.log("这是一个匿名函数") }();
```
- 形参/实参
- return // 一般函数执行后返回undefined -> 未定义
- arguments //可以用于获取参数的值
### 实用函数
1. 在页面内点击按钮即可插入<input>输入框输入数据
```js
// 可以使用本地jquery，也可以采用cdn环境，确保src路径正确即可
<script src="https://apps.bdimg.com/libs/jquery/2.1.4/jquery.min.js"></script>
function CreateInput(){
	var inputList = document.getElementById("inputList");
	var inputCount = inputList.getElementsByTagName('input').length;
	var newInput = `<form id="myForm">
						<div class="form-group">
							<th>
								<input class="form-control" id="inputText" name="inputText[${inputCount}]" type="text">
							</th>
							<th>
								<input class="form-control" id="inputDate" name="inputDate[${inputCount}]" type="date"> 
							</th>
							<th>
								<input class="form-check-input" id="inputCheck" name="inputCheck[${inputCount}]" type="checkbox">
							</th>
							<th>
								<button class="btn btn-success" id="button" name="btn_add" onclick="Add()">添加</button>
							</th>
						</div>
					</form>
					<br/>`;
	var element = document.createElement("thead");
	element.innerHTML = newInput;
	inputList.appendChild(element);
}
```
2. 通过xhr或者ajax对数据进行Post方法操作
```js
function Add(){
	var dataText = document.getElementById("inputText").value;
	var dataTime = document.getElementById("inputDate").value;
	var dataCheck = document.getElementById("inputCheck").checked;
	// 使用JSON格式进行数据传递
	var data = JSON.stringify({
		Item:dataText,
		CreateTime:dataTime,
		IsDone:dataCheck
	});

	// 方法一：
	var xhr = new XMLHttpRequest;
	xhr.open("POST","/Home/Create",true);  // POST所对应的后端Action的路由url
	xhr.setRequestHeader("Content-type","application/json");
	xhr.send(data);
	
	xhr.onreadystatechange = function () {
		if (xhr.readyState == 4 && xhr.status == 200) {
			// readyState有5种状态：
			//   0 - ajax开始初始化
			//   1 - 开始发送ajax请求
			//   2 - ajax请求发送完成
			//   3 - 开始解析响应的资源
			//   4 - ajax请求的步骤全部完成
			//var s = xhr.responseText;
			//document.getElementById("result").innerHTML = JSON.parse(s).result;
			window.location.reload();  // 需要加上window，否则不会刷新页面
			}
	}
	
	// 方法二：
	$.ajax({
	   url:"/Home/Create",
	   data:data,
	   contentType:"application/json",
	   type:"post",
	   cache:false,
	   datatype:"json",
	   success:function(data){alert("添加成功");location.reload()}, // ajax中使用location.reload()即可刷新页面
	   error:function(){alert("添加失败");}
	})
}
```

## 操作元素的自有属性
```JavaScript
var old_img = document.querySelector("img");
var old_div = document.querySelector("div");
old_img.src = "test2.jpg";
old_div.className = "new_name";
```
- 通用获取方法：obj.getAttribute("name"), obj.setAttribute("name", "1"), obj.removeAttribute("name")
- 自定义属性获取方式采用通用获取方法即可

## 操作样式
- 行内样式（定义在标签内）
```JavaScript
var box = document.querySelector(".box");
box.style.width = 500+"px"; //操作单个样式
box.style.cssText = "width:500px;height:500px;" //操作多个样式
```
- 内嵌样式（在style标签内）
```JavaScript
var box = document.querySelector(".box");
box.getComputedStyle(box,null)["样式名"]
```
- 外部样式（外部的css文件），与内嵌相同

## 浏览器事件
- BOM（Browser Object Model）
- onload,onerror,onresize
```JavaScript
window.onload = function(){
	console.log("页面已经加载完毕")
}
window.onreize = function(){
	console.log("页面窗口发生变化")
}
window.onscrool = function(){
	console.log("页面滚动条发生变化")
}
```

## 注意
1. 若从页面中获取了日期文本，需要注意格式是否与后端相匹配，如不匹配需要对文本进行格式化处理
```js
// 页面内获取的日期格式为：2023/12/15
// 后端内的日期格式为：2023-12-15
// 前后端不一致在.net core web mvc环境下会导致报错：视图页面不存在的错误
// 可以采用split分割后再使用join进行拼接处理
var date = document.getElementById('id').innerText.toString().split("/").join("-");
```
2. 获取元素所对应的值
```js
$('button[name=edit]').click(function(e)
{
	var x = $(e.target).attr("id");
	Edit(this,x);
})
```
3. 修改<input>输入框的内容，将不可编辑变成可编辑
```js
$('.input_1').each(function()
{
	// 未修改前的文本内容 
	var old_value = $(this).html().trim(" "); 
	$(this).html("<input type='text' name=" + inputName + " class='form-control' value=" + old_value + " >");
})

// 获取修改后的文本内容
$('input[name='input']').each(function()
{ 	
	var new_value = $(this).parent('td').parent('tr').children().find('input').val(); 
})
```