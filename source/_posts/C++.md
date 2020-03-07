---
title: C++学习笔记
date: 2020-03-06
tags:
- C++
categories:
- C
---

## 基础语法

### 输入输出

- cin >> 输入
- cout << 输出
- endl 换行
- 将值赋给表达式，如下所示

```C++
#include <iostream>
using namespace std;
    
int main(){
	int a = 1;
	int b = 2;
	
    (a + b) = 3  // ==> a = 3, b = 2
	(a > b ? a : b) = 4; // ==> a = 1, b = 4
	
	cout << a << endl;
	cout << b << endl;
} 
```

### 函数重载

- C不支持函数重载，C++支持

```C++
int sum(int v1, int v2){
    return v1 + v2;
}

int sum(int v1, int v2, int v3){
    return v1 + v2 + v3;
}

int main(){
    sum(10,20);
    sum(10,20,30);
}
```

- 规则：函数名相同，函数的参数不同、位置不同
- 调用函数时，实参的隐式类型转换可能会产生二义性
- C++ 采用name mangling编译时会将函数名“重命名”，但不同的编译器有不同的规则

### 默认参数

- C++允许函数设置默认参数，在调用时可以根据情况省略实参，但默认参数只能按照右到左的顺序  

### extern C

- 被extern "C"修饰的代码会按照C语言被编译

- 当函数的声明与实现分开时，需要让函数的声明被extern C修饰
- 一般用于C和C++混合开发时，头文件存放函数的声明，源文件存放函数的实现
- ifdef __cplusplus / endif  //如果定义了某个宏，那就编译中间的代码

```C
header.h >>

#ifdef __cplusplus //c++默认定义了__cplusplus宏，所以当检测到该宏时，就认为这是一个c++文件，则编译下列代码
    extern "C" {
#endif

int void();
    
#ifdef __cplusplus 
}
#endif
```

- 防止重复调用外部代码可以使用ifndef实现或者#pragma once，但两者有区别，前者受C\C++标准支持，不受编译器限制，且可以针对部分代码实现，但后者较老的编译器不支持且针对全部文件内的代码

```C
#ifndef ABC //当第一次调用的时候，检测到该文件内确实没有定义ABC，则下列代码会照常编译，当第二次调用的时候，检测到该文件内已经定义了ABC，则下列代码不会再参与编译
#define ABC //一般这种特殊的宏定义可以使用“__”+“文件名”
...
#endif
```

### 内联函数

- inline作为修饰词，修饰声明和实现均可
- 编译器会将函数调用直接展开为函数体代码
- 函数体积不大且需要频繁调用的时候使用，递归函数不可以被内联
- 对比宏替换，多了函数的特性

### 宏替换

- #define add(v1, v2) v1 + v2

### const

- 被它修饰的变量将无法修改
- 在结构体内同样生效
- 指针也同样
```C++
#include <iostream>
using namespace std;
    
struct Date{
    int year;
    int month;
    int day;
}

int main(){
    Date d1 = {2001, 1, 2};
    Date d2 = {2002, 3, 5};
    Date *p = &d1;
    p -> year = 2003;
    (*p).month = 5;
    *p = d2
    count << d1.year << endl;
}
```
- const修饰的是其右边的代码
```C++
#include <iostream>
using namespace std;

int main(){
    int age = 1;
    int weight = 2;
    
    const int *p1 = &age;
    int const *p2 = &age;
    int * const p3 = &age;
    // 此时p3是常量，*p3不是，则p3无法重新赋值
    // p3 = &weight; 这是将weight的地址赋值给了p3,而此时*p3 = 3则是 weight = 3
    const int * const p4 = &age;
    int const * const p5 = &age;
}
```

### 引用

```C++
int age = 10;
int &refAge = age;
refAge = 20;
```
- 类似于上述的代码就是引用，可以起到与指针类似的功能
- 引用相当于是给变量起了一个别名
- 对引用做计算，就是对变量做计算
- 在定义时就必须初始化，一旦指向了某个变量，就不可以再改变了
- 可以利用引用初始化另一个引用，相当于某个变量有多个别名
- 比指针更安全，函数返回值可以被赋值
- 引用的本质就是指针，但编译器削弱了它的功能，即引用就是弱化的指针，从底层汇编代码来看其执行完全一样