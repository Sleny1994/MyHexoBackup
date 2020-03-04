---
title: HTML基础知识
tags:
- HTML
categories:
- HTML
---

## 规范
- 必须以如下代码开头 
```HTML
<!doctype html>
```

<!--more-->

## 标签
```html
<h1></h1> <!--标题标签，由1-6-->
<i></i> <!--斜体-->
<hr> <!--分割线-->
<p></p> <!--换行（或者<br>)-->
<b></b> <!--加粗-->
&nbsp; <!--空格标识符，需要几个就写几个-->

<title></title> <!--网页标题标签-->
<body></body> <!--正文标签-->

<ul type="disc"> <!--无序列表,circle空心圆，square实心方块-->
    <li>
    	<a href="https://www.baidu.com" targer="_blank"></a> <!--超链接,_blank新建标签页打开-->
    </li> <!--列表项-->
</ul>

<ol type="1">
    <!--有序列表,a小写字母排序，A大写字母排序，i小写罗马字母排列，I大写罗马字母排列-->
</ol>

<img src="images/1.jpg" width="100px" height="100px" title="xxx" alt="xxx">
<!--图片标签，图片自动进行等比例的放大或缩小，宽高同时设置时需要注意图片的宽高比例，否则图片会变形，title鼠标放上去后的提示文字，alt图片加载失败后的提示文字-->

<del></del> <!--删除线-->
<sup></sup> <!--将文字变成上标-->
<u></u> <!--给文字添加下划线-->

<table border="1px" cellspacing="0"> <!--表格标签，border边框属性，cellspacing边框大小-->
    <thead></thead> <!--表格的页眉-->
    <tbody></tbody> <!--表格的主体-->
    <tfoot></tfoot> <!--表格的页脚-->
    <clo width="200px"></clo> <!--批量设定列属性，clogroup-->
    <tr align="center"> <!--行-->
    	<td width="200px" align="center" colspan="数字" rowspan="数字"></td> <!--列，colspan合并列，rowspan合并行-->
    </tr>
</table>

<form action="http://www.zlcode.pub" method="get/post"> <!--表单，action动作，method提交方法-->
    <input type="text" name="login_name" style="color:red;"> <!--type属性值有text,password,button...，style样式：width,height,color,background-color-->
    <input type="password" name="login_pwd">
    <input type="button" value="xxx">
    <input type="submit" value="xxx">
    <input type="reset" value="xxx"> <!--重置--->
    <input type="radio" value="xxx"> <!--单选框--->
    <input type="checkbox" value="xxx"> <!--复选框--->
    <input type="file" value="xxx"> <!--文件选择框--->
</form>

<span></span> <!--容器标签，无其它特殊作用，仅用作装饰文字-->

<div margin:auto font-size:24px></div> <!--容器标签，无其它特殊作用，仅用作包裹任何内容-->
```

## CSS
### 基础语法
```HTML
<link href="mycss.css" type="text/css" rel="stylesheet"> <!--需要在head头部添加引入css的语句-->

selector{
	property:value;
	}

<!--例如
	h1{color:red;font-size:16px;}
-->
```

### 高级语法
- h1,h2,h3,h4,h5{...} 选择器分组
- body{color:green;} 继承

### 选择器
- 派生选择器
- id选择器，‘#’号键获取id
- 类选择器，‘.’号键获取
- 属性选择器