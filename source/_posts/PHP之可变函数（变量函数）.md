---
title: PHP之可变函数（变量函数）
date: 2019-08-01
tags: 
- PHP
categories: 
- PHP
---

## 定义

如果一个变量后面有圆括号，PHP将寻找与变量的值同名的函数，并尝试执行它。

可变函数可以用来实现包括回调函数，函数表在内的一些用途。

可变函数不能用于例如`echo`,`print`,`unset()`,`isset()`,`empty()`,`include`,`require`以及类似的语言结构，需要使用自己的包装函数来将这些结构用作可变函数。

## 示例一：可变函数调用

```php
<?php
function foo() {
    echo "In foo()<br />\n";
}

function bar($arg = '') {
    echo "In bar(); argument was '$arg'.<br />\n";
}

// 使用 echo 的包装函数
function echoit($string)
{
    echo $string;
}

$func = 'foo';
$func();        // This calls foo()

$func = 'bar';
$func('test');  // This calls bar()

$func = 'echoit';
$func('test');  // This calls echoit()
?>
```

## 示例二：调用一个对象方法

```php
<?php
class Foo{
    function Variable(){
        $name = 'Bar';
        $this -> $name();
    }

    function Bar(){
        echo 'This is Bar.';
    }
}

$foo = new Foo();
$funcname = 'Variable';
$foo -> $funcname();
?>
```

## 示例三：函数调用比静态属性优先

```php
<?php
class Test{
    static $variable = 'static properly';
    static function Variable(){
        echo 'Method Variable called.';
    }
}

echo Test::$variable;   //输出静态变量
$variable = "Variable";
Test::$variable();    //函数调用
?>
```