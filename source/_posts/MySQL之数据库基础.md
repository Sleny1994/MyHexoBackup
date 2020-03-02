---
title: MySQL之数据库基础
date: 2019-08-04
tags: 
- MySQL
categories: 
- MySQL
---

MySQL是目前最为流行的开源数据库，是完全网络化的跨平台关系型数据库系统。
由MySQL AB公司开发，已从1995年至今，其象征符号是一只小海豚。
如今已被Oracle收购，其创始人从现有MySQL程序代码中，开发出了另一个分支版本，名为MariaDB。
官网地址：https://www.mysql.com/

## 特点

- 功能强大：提供了多种数据库存储引擎
- 支持跨平台：至少20种以上的开发平台，包括Linux、Windows、FreeBSD、IBMAIX等
- 运行速度快：使用了极快的B树磁盘表（MyISAM 5.0默认引擎）和索引压缩
- 成本低：开源、免费
- 支持各种开发语言：PHP、C、C++、Java、Python等
- 数据库存储容量大：其最大的有效表尺寸通常是由于操作系统对文件大小的限制决定的，而不是由MySQL内部限制决定的。InnoDB存储引擎（MySQL 5.5起默认引擎）将表保存在一个表空间内，该表空间可以由数个文件创建，表空间的最大容量为64TB。



## 安装

具体的安装步骤就不详细介绍了，网上有很多的安装教程，不会的可以自行度娘一下。
安装包下载，可以从文章开头给出的官网链接下载，也可以使用其它第三方源提供的下载。

## 登陆

在安装配置好你的MySQL数据库之后，我们需要做肯定是连入你自己的数据库。

常用的连接数据库的方式有三种：1、命令行直接登陆 2、Web页面登陆（phpMyAdmin) 3、第三方工具

这里主要介绍命令行的登陆方式：

```bash
mysql -uroot -proot //-u后面连接数据库的用户名，这里为root；-p后面连接数据库的密码，这里为root
```

*需要注意的是，上述-u和-p后面是没有空格的，*
*且在没有设置系统环境变量的时候，只能够在MySQL的安装目录下的\bin目录下执行登陆操作*

## 数据库操作

在登陆数据库后，则可以对数据库的库、表、字段、包括内容进行一系列的操作。

- 创建数据库

```mysql
create database 数据库名;
/*数据库的命名规则*
*不能与其它数据库重名
*名称可以由字母、数据库、下划线（_）、或者'$'组成，除数字以外，其它均能作为开头
*名称最长可以为64字符，而别名可以为256字符
*不能使用MySQL关键字作为数据库、表明
*默认情况下，Windows下数据库名、表名不区分大小写，而Linux下相反，为了便于平台迁移建议使用小写字母进行定义
*/
```

- 选择数据库

```mysql
use 数据库名;   # 选择了对应的数据库后，才可以操作该数据库内的所有对象
```

- 查看数据库

```mysql
show databases;
```

- 删除数据库

```mysql
drop database 数据库名;  # 需要注意，对于删除数据库的操作，应谨慎使用，一旦执行，所有数据都将被删除，无法恢复
```

## 数据表操作

- 创建数据表

```mysql
create [TEMPORARY] table [IF NOT EXISTS] 数据表名 [(create_definition,...)] [table_options] [select_statement]
/*
*TEMPORARY - 使用该关键字，则临时创建一个临时表
*IF NOT EXISTS - 用于避免表存在时MySQL报错
*create_definition - 表的列属性部分，创建表时至少包含一列
*table_options - 表的一些特性参数
*select_statement - SELECT语句的描述，可以用于快速创建表
*/

# create_definition的用法
col_name type [NOT NULL | NULL] [DEFAULT default_value] [AUTO_INCREMENT] [PRIMARY KEY] [reference_definition]
/*
*col_name - 字段名
*type - 字段类型
*NOT NULL | NULL - 是否允许为空，注意数据‘0’和空格都不是空值，一般默认允许为空
*DEFAULT default_value - 表示默认值
*AUTO_INCREMENT - 是否自动编号，每张表只能有一个AUTO_INCREMENT列，且必须被索引
*PRIMARY KEY - 是否为主键，每张表只能由一个PRIMARY KEY，若未设定，MySQL将返回第一个没有任何NULL列的UNIQUE键作为主键
*reference_definition - 字段添加注释
*/

# 实际使用中只需要指定基本属性即可：
create table table_name (列名1 属性， 列名2 属性， ...);
```

- 查看表结构

```mysql
/*show columns语句*/
show [full] columns from 数据表名 [from 数据库名];
show [full] columns from 数据库名.数据表名;
# 两条语句任选其一即可

/*describe语句*/
describe 数据表名;          
# 列出整表describe 数据表名 列名;     
# 列出某一列的信息
```

- 修改表结构

```mysql
alter [IGNORE] table 数据表名 alter_spec[,alter_spec]...
/*当制定IGNORE时，若出现重复关键的行，则只执行一行，其它重复的被删除*/
/*alter_spec - 定义要修改的内容，语法如下*/
alter_specification:    
    ADD [COLUMN] create_definition [FIRST | AFTER column_name]  --添加新字段    
    ADD INDEX [index_name] (index_col_name,...)                 --添加索引名称    
    ADD PRIMARY KEY (index_col_name,...)                        --添加主键名称    
    ADD UNIQUE [index_name] (index_col_name,...)                --添加唯一索引    
    ALTER [COLUNM] col_name {SET DEFAULT literal | DROP DEFAULT}--修改字段名称    
    CHANGE [COLUMN] old_col_name create_definition              --修改字段名称    
    MODIFY [COLUMN] create_definition                           --修改子句定义字段    
    DROP [COLUMN] col_name                                      --删除字段名称    
    DROP PRIMARY KEY                                            --删除主键名称    
    DROP INDEX index_name                                       --删除索引名称    
    RENAME [AS] new_tbl_name                                    --更改表名
```

- 重命名数据表

```mysql
rename table 数据表名1 to 数据表名2; /*可以同时多个表进行，使用‘，’隔开即可*/
```

- 删除数据表

```mysql
drop table [if exists] 数据表名;
```

- 添加数据

```mysql
insert into 数据表名(column_name1, column_name2,...) values(value1, value2, value3,...);
```

- 查询数据

```mysql
select selection_list from 数据表名 where condition;
```

- 修改数据

```mysql
update 数据表名 set column_name1 = new_value1, column_name2 = new_value2,...where condition;
# where语句是可选的，如果不选则将修改所有行的记录
```

- 删除数据

```mysql
delete from 数据表名 where condition;
# 若未指定where语句，将删除所有记录
```

- 查询数据

```mysql
select selecttion_list              # 要查询的内容，选择哪些列
from table_name                     # 指定数据表
where primary_constraint            # 查询时需要满足的条件
group by grouping_columns           # 如何对结果进行分组
order by sorting_columns            # 如何对结果进行排序，默认升序，desc降序
having secondary_constraint         # 查询时满足第二个条件
like()                              # 模糊查询，可以使用'%'或者'_'匹配，前者匹配一个或者多个字符，后者只匹配一个字符
limit count                         # 限定输出的查询结果
```

## MySQL中的特殊字符

当SQL语句中存在特殊字符时，需要使用“\”对特殊字符进行转义，否则将会出现错误。

```mysql
\'      # 单引号
\''     # 双引号
\\      # 反斜杠
\n      # 换行符
\r      # 回车符
\t      # 制表符
\0      # 0字符
\%      # %字符
\_      # _字符
\b      # 退格符
```

## Tips

Drop、Delete、Truncate的区别

1. delete和truncate操作只删除表中的数据，而不删除表的结构
2. drop语句将删除表的结构，被依赖的约束、触发器、索引等
3. 属于不通类型的操作，delete属于DML，事务提交后才生效；另两者属于DDL，操作立即生效
4. 执行速度 drop > truncate > delete
5. 完全删除使用drop；想保留表而将所有数据删除，如若和事务无关，使用truncate；若和事务相关，则使用delete