---
title: TypeScript和JavaScript的区别
date: 2023-06-01
tags: 
- JavaScript
- TypeScript
categories:
- TypeScript
---

## 什么是JavaScript？
JavaScript是由ECMAScript、DOM、BOM组成。
- ECMAScript 描述了JS的语法和基本对象，是一种标准
- DOM 文档对象模型（Document Object Model）
- BOM 浏览器对象模型（Browser Object Model）

## 什么是TypeScript？
TypeScript是Microsoft开发和维护的一种面向对象的编程语言。<br>
TypeScript是JavaScript的超集，包含了JavaScript的所有元素，扩展了JavaScript的语法。

## TypeScript的特点
- 增加了静态类型、类、模块、接口和类型注解
- 一般用于大型项目，易于维护

## TS与JS的差异
- TS是JS的扩展，主要在核心语言方面和类概念的模塑方面
- JS可以在无需任何修改的情况下与TS一同工作，也可以将TS转换成JS
- TS通过类型注解提供编译时的静态类型检查
- TS中的数据要求带有明确的类型，JS不要求
- TS为函数提供了缺省参数值
- TS引入了类、模块的概念，可以把声明、数据、函数和类封装在模块中

> 类型注解：TS提供了很多数据类型，通过类型对变量进行限制，就称之为类型注解，使用后，就不能随意改变变量的类型。<br>
> 缺省参数：调用时只能从最后一个参数开始省略，即如果要省略一个参数，则必须省略其后面的所有参数

## JS的优势
- 更适合简单的应用程序
- 跨平台性更好且非常轻量级

## TS的优势
- 更容易做代码重构
- 更强调显示类型，易于掌握各种组件的交互方式
- 适合代码的大小、复杂性和出错率增加时使用

## TypeScript的引用
```TypeScript
<html>
<head>
  <title>demo</title>
</head>
<body>
  <script type="text/typescript">
     // TypeScript代码
  </script>
  <script src="lib/typescript.min.js"></script>
  <script src="lib/typescript.compile.min.js"></script>
</body>
</html>
```

## TypeScript的基本数据类型
- boolean
```ts 
var isbool: boolean = true;
```
- number
```ts
// JS和TS的所有数值都是浮点型
// TS中定义为number
var isInt:number = 1;
var isFloat:number = 1.11;
```
- string
```ts
// 使用一对双引号或一对单引号来表示字符串
var name:string = 'Sam';
var school:string = "NNU";
```
- array
```ts
// 数组使用[]声明
var num:number[] = [1, 2, 3];
var name:string[] = ['Lilei', 'Wangwu', 'Hanmeimei'];

// 访问数组中的元素
alter(num[0]);  // 1

// 定义任何类型的数组，关键字Array
var arr:Array = [1, 2, 3, 'a', 'b', 'c'];
```
- enum
```ts
// 枚举类型是TS中新增的
enum Color{
    Blue,
    Yellow,
    Red
};

var col:Color = Color.Blue;
```
- any
```ts
// 和JS中变量的默认类型一样，指代动态的能够赋予任意类型
var ex:any = 1;
ex = "Hello";
ex = false;
// any可以配合数组使用
var arr:any = [1, 2, "a", false];
arr[1] = 100;

// Tips:不要随意使用any类型，会导致数据类型泛滥
```
- void
```ts
// void表示没有任何类型，类似于空类型
// 一般用于定义空函数时，即当函数没有返回值时
const consoleText = (text:string) : void => {
    console.log(text);
};
// void类型的变量只能赋值为undefined和null
```
- never
```ts
// never指那些用不存在值的类型
// 是总会抛出异常或根本不会有返回值的函数表达式的返回值类型
// 当变量被永不为真的类型保护约束时，该变量也是never类型
const errFunc = (message:string) : never => {
    throw new Error(message);
};

const infFunc = () : never => {
    while(true){}
};
```

## TS中函数的定义与调用
```ts
// 常规函数
function func_name(arg1:number, arg2:number, ...) : return_type{
    function_code;
    return {data};
};
// function函数声明的关键字
// func_name函数名
// arg1,arg2,...参数
// return_type返回值的类型
// function_code函数的主体执行代码
// data返回的数据
function add(x:number, y:number) : number {
    return x + y;
}
add(1,1);

//匿名函数
//没有名称只有主体的函数，不需要指定返回类型，其返回值由主题内的return而定
var nmadd(x:number, y:number) {
    return x + y;
}
nmadd(1,1);

//可选参数
//在参数名后面，冒号前面添加一个问号
function name(firstName:string, lastName?:string){
    if(lastName) return firstName + " " + lastname;
    else return firstName;
}
var name1 = name("Steven");           // Steven
var name2 = name("Steven", "Job");    // Steven Job

//默认参数
//在参数名后直接给定一个值，如果此参数没有传入值，则默认赋予该值
function name(firstName:string, lastName = "Meimei"){
    return firstName + " " + lastName;
}
var name1 = name("Steven");           // Steven Meimei
var name2 = name("Steven", "Job");    // Steven Job
```

## TypeScript的类
### 类的定义
```ts
class 类名{
    name:string;    // 定义类的属性
    fun(){          // 定义类的方法
                    // 定义方法所要实现的功能       
    }
}
```
- 类可以被继承，其方法和属性都可以在子类中被继承
- 未定义任何方法的空类可以作为泛型类
### 实例化及构造函数
```ts
class student{                     // 定义student类
    name:string;
    constructor(myname:string){    // 定义构造函数
        this.name = myname;
    }
    study(){                       // 定义类的方法
        document.write("<h1> My name is " + this.name + ".</h1>");
    }
    write() : string{
        return "write name: " + this.name;
    }
}

var s1 = new student("Bob");       // 类的实例化
document.write("<h1>" + s1.name + "</h1>");
s1.study();                        // 调用类的方法
document.write("<h1>" + s1.write() + "</h1>");
```
## TypeScript的模块
### 模块化的特点
- 提高代码的可复用性
- 分离类的内部类和类的内部方法，强化面向对象的特征
- 使用module关键字定义模块
- 使用export关键字使接口、类等成员对模块外可见
```ts
// 未模块化时的代码：
// 验证的封装
interface StringValidator {  
//定义验证接口
  isAcceptable(s:string) : boolean;
}

var lettersRegexp = /^[A-Za-z]+$/;
var numberRegexp = /^[0-9]+$/;

class LettersOnlyValidator implements StringValidator {　
//实现接口
  isAcceptable(s:string) {
    return lettersRegexp.test(s);
  }
}

class ZipCodeValidator implements StringValidator {   
//实现接口
  isAcceptable(s:string) {
    return s.length === 5 && numberRegexp.test(s);
  }
}

// 验证的过程
var strings = ['Hello', '98052', '101'];
var validators: { [s:string]: StringValidator; } = {};
validators['ZIP code'] = new ZipCodeValidator();  
//实例化类
validators['Letters only'] = new LettersOnlyValidator(); 
//实例化类
for(var i=0;i&ltstrings.length;i++){
    for (var name in validators) {
       document.write('"' + strings[i] + '" ' + (validators[name].isAcceptable(strings[i]) ? ' matches ' : ' does not match ') + name+"<br>"); 
        //调用类的方法
    }
}

// 模块化后的代码：
module Validation {   
//定义模块
  export interface StringValidator {  
  //声明接口对外部可以使用
    isAcceptable(s:string) : boolean;
  }

  var lettersRegexp = /^[A-Za-z]+$/;
  var numberRegexp = /^[0-9]+$/;

  export class LettersOnlyValidator implements StringValidator{  
  //声明类对外部可用
    isAcceptable(s:string) {
      return lettersRegexp.test(s);
    }
  }

  export class ZipCodeValidator implements StringValidator {
    isAcceptable(s:string) {
      return s.length === 5 && numberRegexp.test(s);
    }
  }
}

var strings = ['Hello', '98052', '101'];
var validators: { [s:string] : Validation.StringValidator; } = {};
validators['ZIP code'] = new Validation.ZipCodeValidator();  
//使用模块中的类
validators['Letters only'] = new Validation.LettersOnlyValidator();
// 显示匹配结果
for(var i=0;i&ltstrings.length;i++){
  for (var name in validators) {
     document.write('"' + strings[i] + '" ' + (validators[name].isAcceptable(strings[i]) ? ' matches ' : ' does not match ') + name+"<br>"); // 使用方法
    }
}
```
### 分隔模块至多个文件
- 文件1：Validation.ts
```ts
module Validation {
  export interface StringValidator {
      isAcceptable(s: string): boolean;
  }
}
```
- 文件2：LettersOnlyValidator.ts
```ts
/// <reference path="Validation.ts" />
module Validation {
  var lettersRegexp = /^[A-Za-z]+$/;
  export class LettersOnlyValidator implements StringValidator {
      isAcceptable(s: string) {
        return lettersRegexp.test(s);
      }
  }
}
```
- 文件3：ZipCodeValidator.ts
```ts
/// <reference path="Validation.ts" />
module Validation {
  var numberRegexp = /^[0-9]+$/;
  export class ZipCodeValidator implements StringValidator {
    isAcceptable(s: string) {
      return s.length === 5 && numberRegexp.test(s);
    }
  }
}
```
- 文件4：Main.ts
```ts
/// <reference path="Validation.ts" />
/// <reference path="LettersOnlyValidator.ts" />
/// <reference path="ZipCodeValidator.ts" />

var strings = ['Hello', '98052', '101'];
var validators: { [s: string]: Validation.StringValidator; } = {};
validators['ZIP code'] = new Validation.ZipCodeValidator();
validators['Letters only'] = new Validation.LettersOnlyValidator();
for(var i=0;i&ltstrings.length;i++){
  for (var name in validators) {
     document.write('"' + strings[i] + '" ' + (validators[name].isAcceptable(strings[i]) ? ' matches ' : ' does not match ') + name+"<br>"); //调用类的方法
    }
}
```
```ts
/// < reference path=“Validation.ts” /><br>
/// < reference path=“LettersOnlyValidator.ts” /><br>
/// < reference path=“ZipCodeValidator.ts” />

//文档注释一般用于告知TS编译器该文件依赖于哪些文件，若依赖文件不存在则编译失败，一般建议改注释最好加上
```
## TypeScript的编译
.js文件可以直接在浏览器内运行，但.ts则不行，所以Ibanez建议将稳定的module提前编译成js文件放至工程中，同时在引用编译生成的文件时，还需注意顺序。