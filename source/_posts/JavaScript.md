---
title: JavaScript基础知识
tags:
- JavaScript
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