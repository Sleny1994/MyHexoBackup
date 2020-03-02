---
title: PHP-二维数组根据键值排序
date: 2019-08-01
tags: 
- PHP
categories: 
- PHP
---

## 函数流程

1. 根据需要的键值整理出需要排序的数组
2. 设定是升序排序还是降序排序
3. 根据排序后的数组重新排列原有数组

## 代码实现

```php
<?php
    /**
     * 根据数组中某个键值的大小进行排序，仅支持二维数组。
     * 
     * @param array $array 排序数组
     * @param string $key 键值
     * @param bool $asc 默认正序，false为降序
     * @return array 排序后的数组
     */

    function arraySortByKey($array = array(), $key = " ", $asc = true){
        $result = array();
        /** 根据需要的键值整理出需要排序的数组 */
        foreach ($array as $k => $v) {
            # code...
            $values[$k] = isset($v[$key]) ? $v[$key] : " ";
        }
        //print_r($values);
        //echo '<br>';
        unset($v);  //销毁变量
        $asc ? asort($values) : arsort($values);  
        //对需要排序的键值进行排序 $asc = true -> asort(); $asc = false -> arsort()
        //print_r($values);

        /** 重新排列原有数组 */
        foreach ($values as $k => $v){
            $result[$k] = $array[$k];
        }
        return $result;
    }

    /** 定义数组 */
    $data = array(
        array(
            'post_id' => 1,
            'title' => '如何学好PHP',
            'reply_num' => 582
        ),
        array(
            'post_id' => 2,
            'title' => 'PHP数组常用函数汇总',
            'reply_num' => 182
        ),
        array(
            'post_id' => 3,
            'title' => 'PHP字符串常用函数汇总',
            'reply_num' => 982
        )
    );

    $new_array = arraySortByKey($data, 'reply_num', false);
    echo '<pre>';
    print_r($new_array);
?>
```

## 结果显示

```
Array
(
    [2] => Array
        (
            [post_id] => 3
            [title] => PHP字符串常用函数汇总
            [reply_num] => 982
        )

    [0] => Array
        (
            [post_id] => 1
            [title] => 如何学好PHP
            [reply_num] => 582
        )

    [1] => Array
        (
            [post_id] => 2
            [title] => PHP数组常用函数汇总
            [reply_num] => 182
        )

)
```

