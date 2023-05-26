---
title: PHP之自动类型转换
date: 2019-08-015
tags: 
- PHP
categories: 
- PHP
---

自动类型转换就是数据类型在某些情况下，会自动变为其它类型参与运算。
运算和判断的时候，某些值会自动进行转换。

## 布尔值判断时的自动类型转换

1. 整型0为假，其它均为真。

   ```php
   <?php    
       function bool_value($str)
   	{        
       if($str){            
           echo "This is true.";            
       }
       else{            
           echo "This is false.";            
       	}     
   	}     
   	bool_value(0);    
   	bool_value(1);?>
   ```

<!--more-->

2. 浮点型0.0为假，小数点后有一个非零的数值即为真。

   ```php
   <?php
       function bool_value($str)
       {
           if ($str) {
               echo "This is true.";
           } else {
               echo "This is false.";
           }
       }
       bool_value(0.0);
       echo "<br>";
       bool_value(0.1);
   ?>
   ```

3. 空字符串为假，有一个空格即算真。

   ```php
   <?php
       function bool_value($str)
       {
           if ($str) {
               echo "This is true.";
           } else {
               echo "This is false.";
           }
       }
       bool_value(' ');
   ?>
   ```

4. 字符串的0为假，其它均为真。

   ```php
   <?php
       function bool_value($str)
       {
           if ($str) {
               echo "This is true.";
           } else {
               echo "This is false.";
           }
       }
       bool_value('0');
   ?>
   ```

5. 空数组为假，只要有一个值，即为真。

   ```php
   <?php
       function bool_value($str)
       {
           if ($str) {
               echo "This is true.";
           } else {
               echo "This is false.";
           }
       }
       bool_value(array());
       echo "<br>";
       bool_value(array('1'));
   ?>
   ```

6. 空（NULL）为假。

   ```php
   <?php
       function bool_value($str)
       {
           if ($str) {
               echo "This is true.";
           } else {
               echo "This is false.";
           }
       }
       bool_value(null);
   ?>
   ```

7. 未声明成功的资源为假。

   ```php
   <?php
       function bool_value($str)
       {
           if ($str) {
               echo "This is true.";
           } else {
               echo "This is false.";
           }
       }
       bool_value(fopen('test.txt', 'r'));
       //该代码运行会提示warning，忽略即可。
   ?>
   ```

## 运算过程中的自动类型转换

```php
<?php
     $foo = true;
     $result = $foo + 10;
     var_dump($result);	//print int(11)
     $str = '520我爱你';
     $result = $str + 1;
     var_dump($result);	//print int(521)
     //将520放在我爱你的中间尝试一下，会出现什么样的结果？
     //print int(1)
?>
```

总结：

1. 布尔值的true参与运算是会变成整型或者浮点数的1，布尔值的false参与运算是会变成整型或者浮点数的0；
2. 字符串开始处是整型或者浮点类型的会转换成对应的类型参与运算，而其它则为0。

## 强类型转换

1. 使用函数进行强类型转换：intval()、floatval()、strval()

   ```php
   <?php
        $float = 1.11;
        $result = intval($float);
        var_dump($result);
        $int = 11;
        $result = floatval($int);
        var_dump($result);
        $result = strval($int);
        var_dump($result);
   ?>
   ```

2. 变量前加上括号，里面写上要转换的类型，将转换后赋值给其它变量。

   ```php
   <?php
        $float = 1.11;
        $result = (int)$float;
        var_dump($result);
        $result = (bool)$float;
        var_dump($result);
        $result = (array)$float;
        var_dump($result);
        $bool = true;
        $result = (int)$bool;
        var_dump($result);
   ?>
   ```

3. settype(变量, 类型)直接修改变量自身。

   ```php
   <?php
        $float = 1.11;
        settype($float, 'int');
        var_dump($float);
        unset($float);
        settype($float, 'string');
        var_dump($float);
   ?>
   ```

