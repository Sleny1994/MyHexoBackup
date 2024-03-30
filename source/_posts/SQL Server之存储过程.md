---
title: SQL Server之存储过程
date: 2023-08-28
tags:
- SQL
categories:
- SQL
---


## 定义
- 存储过程（Stored Procedure）是使用Transact-SQL语言编写的一段能实现指定功能的程序
- 用户可以通过存储过程的名称和参数传递调用这些具有指定功能的存储过程
- 存储过程也是数据库对象

## 优点
- 执行速度快，效率高
- 模块式编程
- 减少网络流量
- 安全性

## 种类
- 系统存储过程（System Stored Procedures）：一般是以`sp_`为前缀的
- 扩展存储过程（Extended Stored Procedures）：一般是以`xp_`为前缀的，不存储在SQL Server中，而是以dll形式单独存在
- 用户自定义存储过程（User-defined Stored Procedures）：由用户自行创建的存储过程，可以输入参数，向客户端返回表格或结果、消息等，也可以返回输出参数。
    - Transact-SQL存储过程
    - CLR存储过程：是.NET Framework公共语言时（CLR）方法的引用

## 语法
### 创建
```sql
 CREATE { PROC｜PROCEDURE }
     [schema_name.] procedure_name [ ; number ]          --架构名。存储过程名[;分组]
     [ { @parameter [ type_schema_name. ] data_type }     --参数
       [ VARYING ] [ = default ] [ [ OUT [ PUT ]         --作为游标输出参数
     ] [ ,...n ]
 [ WITH <procedure_option> [ ,...n ]
 [ FOR REPLICATION ]                    --不能在订阅服务器上执行为复制创建的存储过程
 AS { <sql_statement> [;][ ...n ]       --存储过程语句
｜<method_specifier> }
 [;]
 <procedure_option> ::=
     [ ENCRYPTION ]                      --加密
     [ RECOMPILE ]                       --不预编译
     [ EXECUTE_AS_Clause ]      --执行存储过程的安全上下文
 <sql_statement> ::=
 { [ BEGIN ] statements [ END ] }                        --存储过程语句
 <method_specifier> ::=
 EXTERNAL NAME assembly_name.class_name.method_name      --指定程序集方法
```
- schema_name：架构名
- procedure_name：存储过程名
- number：对同名过程进行分组的选项，使用drop procedure语句可以将这些分组过程一起删除
- @parameter：存储过程的参数,@为局部变量，@@为全局变量
- [ type_schema_name. ] data_type：参数的架构及类型，OUTPUT关键字用于指定输出参数
- VARYING：指定作为输出参数支持的结果集，仅适用于cursor参数
- default：参数的默认值，如果定义了default值，则无须指定此参数的值也可执行存储过程
- OUTPUT：输出参数，此选项的值可以返回给调用存储过程的语句
- ENCRYPTION：加密存储过程
- RECOMPILE：指明该存储过程在运行时才编译，不预编译
- EXECUTE_AS_Clause：指定执行存储过程的安全上下文
- FOR REPLICATION：不能在订阅服务器上执行为复制创建的存储过程
- <sql_statement>语法块：存储过程执行的T-SQL语句
- <method_specifier>语法块：指定.NET Framework程序集的方法，以便CLR存储过程引用
### 执行
```sql
 [ [ EXEC [ UTE ] ]
{
[ @return_status = ]
{ procedure_name [ ;number ]｜@procedure_name_var
}
[ [ @parameter = ] { value｜@variable [ OUTPUT ]｜[ DEFAULT ] }
[ ,...n ]
[ WITH RECOMPILE ]
```
- @return_status：是一个可选的整型变量，保存存储过程的返回状态，这个变量在用于EXECUTE语句前，必须在批处理、存储过程或函数中声明过
- @procedure_name_var：是局部定义变量名，代表存储过程名称
### 修改
```sql
ALTER ...
```
### 删除
```sql
DROP PROCEDURE procedure_name
```

## 用户自定义函数
### 标量函数
- 函数的返回值为标量数据类型
```sql
CREATE FUNCTION [ owner_name.] function_name
( [ { @parameter_name [AS] scalar_parameter_data_type [ = default ] } [ ,...n ] ] )
RETURNS scalar_return_data_type
[ WITH < function_option> [ [,] ...n] ]
[ AS ]
BEGIN
  function_body
  RETURN scalar_expression
END
```
- owner_name：拥有该用户定义函数的用户名称。owner_name必须是已有的用户ID。
- function_name：用户定义函数的名称。函数名称必须符合标识符的规则，对其所有者来说，该名称在数据库中必须唯一。
- @parameter_name：用户定义函数的参数名。CREATE FUNCTION语句中可以声明一个或多个参数。函数最多可以有1024个参数。函数执行时每个已声明参数的值必须由用户指定，除非已经定义了该参数的默认值。如果函数的参数有默认值，在调用该函数时必须指定“default”关键字才能获得默认值。这种行为不同于存储过程中有默认值的参数，在存储过程中省略参数也意味着使用默认值。
- scalar_parameter_data_type：参数的数据类型。所有标量数据类型（包括bigint和sql_variant）都可用做用户定义函数的参数。不支持timestamp数据类型和用户定义数据类型。不能指定非标量类型（例如cursor和table）。
- scalar_return_data_type：函数返回值的数据类型。scalar_return_data_type可以是SQL Server支持的任何标量数据类型（text、ntext、image和timestamp除外）。
- ENCRYPTION：指出SQL Server加密包含CREATE FUNCTION语句文本的系统表列。使用ENCRYPTION可以避免将函数作为SQL Server复制的一部分发布。
- SCHEMABINDING：指定将函数绑定到它所引用的数据库对象上。如果函数是用SCHEMABINDING选项创建的，则不能更改（使用ALTER语句）或除去（使用DROP语句）该函数引用的数据库对象。
- function_body：由多条Transact-SQL语句组成的函数体。
- scalar_expression：用户定义函数要返回的标量值表达式。
### 表值函数
- 函数返回值为TABLE（表）
1. 内嵌表值函数
    - RETURNS自居指定的TABLE不附带字段列表，则该函数为内嵌函数
    - 由单个SELECT语句定义，该函数返回的表的字段（包括数据类型）来自定义该函数的SELECT语句的字段列表
    ```sql
    CREATE FUNCTION [ owner_name.] function_name
    ( [ { @parameter_name [AS] scalar_parameter_data_type [ = default ] } [ ,...n ] ] )
    RETURNS TABLE
    [ WITH < function_option > [ [,] ...n ] ]
    [ AS ]
    RETURN [ ( ) select-stmt [ ] ]
    ```
    - TABLE：指定返回值为TABLE（表）
    - select-stmt：单条SELECT语句
2. 多语句表值函数
    - RETURNS子句指定的TABLE类型带有字段及其数据类型
    - 多语句函数的主体中允许使用多种语句
      - 赋值语句
      - 控制流语句
      - DECLARE语句，该语句定义函数局部的数据变量和游标
      - SELECT语句，该语句包含带有表达式的选择列表，其中的表达式将值赋予函数的局部变量
      - 游标操作，该操作引用在函数中声明、打开、关闭和释放的局部游标。只允许使用以INTO子句向局部变量赋值的FETCH语句；不允许使用将数据返回到客户端的FETCH语句
      - INSERT、UPDATE、DELETE语句，这些语句修改函数的局部table类型的变量
      - 调用存储过程的EXECUTE语句
    ```sql
    CREATE FUNCTION [ owner_name.] function_name
    ( [ { @parameter_name [AS] scalar_parameter_data_type [ = default ] } [ ,...n ] ] )
    RETURNS @return_variable TABLE < table_type_definition >
    [ WITH < function_option > [ [,] ...n] ]
    [ AS ]
    BEGIN
      function_body
      RETURN
    END
    ```
    - @return_variable：table类型的变量。当调用程序调用函数时，函数返回的就是该变量的值
### 查看、修改、删除
1. 查看
    - 用户自定义函数的定义存放在sys.sql_modules视图中，在sys.sql_modules中就可以查看到所有用户自定义的函数
2. 修改
    - 修改用户自定义函数使用的是ALTER FUNCTION语句，其他的语法与创建用户自定义函数一样，但是使用ALTER FUNCTION命令不能更改用户自定义函数的类型，例如不能将标量函数更改为内联表值函数或者多语句表值函数
3. 删除
    ```sql
    DROP FUNCTION { [ owner_name .] function_name } [ ,...n ]
    ```

## 游标
### 定义
游标是类似C语言指针的一种结构，它包含结果集和结果集中指向记录的游标位置两部分，游标作为指针来遍历结果集（每次指向一行），可以理解游标是一个数据缓冲区，它存放了查询结果，而用户则可以利用SQL语句从这个缓冲区中提取数据。
### 作用
游标允许开发者从多条数据记录的结果集中每次提取一条记录，从而使用户能够更加灵活地处理SQL操作当中返回的结果集。例如：通常使用的SELECT查询语句，可以返回一个结果集合，而用户则需要从结果集中逐一地读取每条记录，此时就可以利用游标来完成这项操作。

在利用SQL语句处理数据时可以对所有符合条件的数据进行操作，而很多时候则需要对这些符合条件的数据进行逐条的操作，这种操作如果没有游标则会由前端的高级编程语言完成，这样做不仅浪费了程序所在服务器的资源，也浪费了网络资源，使得整个数据操作周期增加，用户体验下降。而通过游标则可以有效地在数据库服务器上解决此类问题。
允许从当前行检索一行或多行数据。

主要功能操作：</br>
- 允许操作当前行的数据，这也是最常用的游标使用方式。
- 可以定位在特定的行。

固定的步骤：</br>
- 创建游标，游标在使用前必须创建，创建游标时允许定义相关的特性。
- 执行游标，利用查询语句来完成对游标的填充。
- 遍历结果集，当游标被数据填充后，可以利用命令来对游标中的数据进行遍历操作，也就是检索游标中的数据。
- 对检索当前行的数据进行操作，例如修改数据等。
- 关闭游标，对游标的操作完成后需要关闭游标，以释放相关资源。

【注】：结果集指的是查询的结果，它可以是多行，也可以是0行，多行时可以利用游标遍历结果，而0行时，游标则没有执行的必要。
### 创建
```sql
DECLARE cursor_name [ INSENSITIVE ] [ SCROLL ] CURSOR
FOR select_statement
[FOR { READ ONLY｜UPDATE [ OF column_name [ ,...n ]]}]
```
- DECLARE：关键词，声明游标。
- cursor_name：创建的游标的名称。
- INSENSITIVE：表示创建的游标中的数据是基础表中的数据副本，该游标不允许修改，如果省略该关键词，那么从游标中提取的数据被修改后将作用在基础表中。
- SCROLL：表示游标的所有相关操作方式被允许，包括FIRST、LAST、NEXT、RELATIVE等，如果省略该项，那么游标的操作将只有NEXT被允许，如果指定了FAST_FORWARD项，则SCROLL将不能被使用。
- select_statement：查询语句，用来提供游标结果集，该查询语句有一定的限制，不允许使用INTO、COMPUTE、COMPUTE BY等关键词。
- FOR：关键词。
- READ ONLY：表示游标只读，不允许通过游标来修改数据。
- UPDATE：表示指定游标可以修改的列，[ OF column_name [ ,...n ]]将指定具体允许被修改的列名，如果只有UPDATE关键词，则表示所有列都允许被更新。
### 打开
```sql
OPEN
{{[GLOBAL]cursor_name }｜cursor_variable_name}
```
- OPEN：打开游标的关键词。
- GLOBAL：指定该游标是全局的游标，如果没有该项，则表示要打开的游标是局部的游标。
- cursor_name：指定打开游标的名称，如果全局游标和局部游标名称相同，则需要GLOBAL来指定打开的对象。
- cursor_variable_name：表示一个游标变量的名称，这一项表示允许打开一个游标变量。
- 变量@@CURSOR_ROWS用于获得游标中记录数，该变量可以有4个返回值，代表的含义如下：
  - -m：表示当前行数，游标被异步填充。
  -  -1：表示游标为动态游标，动态游标反映当前数据的更改情况，由于数据不断更改，因此此时游标不可能得到所有符合要求的数据。
  -  0：此时表示没有符合要求的数据，游标没有打开或已经关闭释放资源。
  -  n：游标正常查询数据，n表示游标中的记录数。15.7.4 得到游标中的数据
- 游标得到的是一个数据集，SQL语句没有办法对数据集进行操作，因此，需要利用FETCH来遍历提取结果集中的数据
```sql
FETCH
        [ [ NEXT｜PRIOR｜FIRST｜LAST
            ｜ABSOLUTE { n｜@nvar }
            ｜RELATIVE { n｜@nvar }
          ]
          FROM
        ]
{ { [ GLOBAL ] cursor_name }｜@cursor_variable_name }
[ INTO @variable_name [ ,...n ] ]
```
- FETCH：检索数据的关键词。
- NEXT：用来返回结果集当前行的下一行，是默认的提取项。
- PRIOR：返回结果集中当前行的前一行，需要说明的是，当从结果集中读取数据时，第一次使用该选项，将无记录返回。
- FIRST：指针返回游标中的第一行并将其作为当前行。
- LAST：指针返回游标中的最后一行并将其作为当前行。
- ABSOLUTE { n｜@nvar }：假如n或者@nvar参数为正，则表示返回从游标开始位置的第n行，并将第n行作为当前行。如果n或@nvar为负数，则返回从游标末尾开始的第n行，并将返回的这一行作为当前行。
- RELATIVE { n｜@nvar}：假如n或@nvar为正数，则返回从当前行开始的第n行，并将返回行变成做为当前行。如果n或@nvar为负数，则返回当前行之前的第n行，并将返回行作为当前行。
- FROM：关键词。
- GLOBAL：指定游标为全局游标。
- cursor_name：游标名称。
- @cursor_variable_name：指游标变量名称。
- INTO：关键词，表示存入。
- @variable_name：变量，它们将接收提取的列数据，要求列表中的各个变量从左到右与游标结果集中的相应列相对应，要求变量的数据类型与相应的结果集列的数据类型匹配，或支持隐式的数据类型转换，除此之外，还要求变量的数量与游标选择列表中的列数相同。
【注】：默认情况下NEXT是唯一受支持的FETCH选项，除非在DECLARE CURSOR语句中指定SCROLL选项，NEXT支持游标指针指向下一行。
### 关闭
当游标使用完毕后，利用CLOSE关键词关闭游标，以释放游标锁定的行，而CLOSE会保留数据结构，从而使得游标可以重新打开，如果需要释放游标占用的数据结构，则利用DEALLOCATE语句完成。
```sql
CLOSE { { [ GLOBAL ] cursor_name }｜cursor_variable_name }
```
- CLOSE：关键词，表示关闭游标操作。
- GLOBAL：指明游标为全局游标，而不是局部游标。
- cursor_name：游标名称。
- cursor_variable_name：游标变量名称。
- 当利用FETCH NEXT遍历游标中的数据时，需要使用@@FETCH_STATUS全局变量检测FETCH操作的状态，可以方便地得知数据是否已经到最后一行，是否操作成功。该变量具有以下几个返回值:
  - 0：表示FETCH成功执行。
  - -1：表示FETCH执行失败。
  - 2：表示读取的数据已经不存在。

【注】：CLOSE只允许作用在打开的游标上，而仅有游标声明或关闭的游标则不能使用该关键语句。
### 修改
要修改游标中的数据则需要指明游标非只读，游标除了使用FOR READ ONLY关键字指明其只读外，在查询语句中使用ORDER BY、DISTINCT等关键词也会使游标只读。
```sql
UPDATE table_name
{SET col_name = expression }[,...n]
WHERE CURRENT OF cursor_name
```
- UPDATE：关键词，表示修改。
- table_name：表名，指需要修改列的所属表名称。
- SET：关键词。
- col_name：准备修改的列名。
- expression：数值或表达式，修改后的值。
- WHERE CURRENT OF：关键词，表示指针所在的当前记录行。
- cursor_name：游标名称。
### 删除
```sql
DELETE FROM table_name
WHERE CURRENT OF cursor_name
```
### 示例
示例1：游标的使用
```sql
-- 使用数据库AdventureWorks
  USE AdventureWorks
  GO

  DECLARE ATriTest_Cusor CURSOR      --声明创建游标
  FOR
  SELECT id,name FROM ATriTest       --游标绑定查询语句
  ORDER BY id 
  OPEN ATriTest_Cusor                    --打开游标
  DECLARE @Id INT, @Name VARCHAR(10)     --声明变量

-- 输出，利用CAST函数把int类型转换成varchar类型，以便正常输出，@@CURSOR_ROWS能获取游标记录数 
  PRINT'游标结果集中的记录总数为：'+CAST(@@CURSOR_ROWS AS varchar(2))

-- 利用FETCH NEXT获取当前行数据存入变量当中，并把指针移动到下一行 
  FETCH NEXT FROM ATriTest_Cusor INTO @ID,@Name
 
  -- 检查Check @@FETCH_STATUS变量，查看FETCH命令是否成功执行
  WHILE @@FETCH_STATUS = 0
  BEGIN
     --输出当前行的记录
     PRINT 'ID：'+CAST(@ID AS char(4))+' 姓名：'+@Name
     --定位到下一行的记录
     FETCH NEXT FROM ATriTest_Cusor INTO @ID,@Name
  END --结束
 
  CLOSE ATriTest_Cusor          --关闭游标
  DEALLOCATE ATriTest_Cusor     --释放
  GO
```
示例2：存储过程+游标的使用
```sql
USE [PRODUCT]
GO
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE PROCEDURE [dbo].[Sp_Procedure_Name]           -- 创建存储过程
  --@Company NVARCHAR(8)                             -- 定义参数：@参数名 参数类型
AS
BEGIN
  SET NOCOUNT ON;                                    -- 不显示计数结果信息
IF CURSOR_STATUS('global','cursor_tag') = -3         -- 标示游标不存在
BEGIN
	DECLARE cursor_tag Cursor FOR                      -- 定义游标
		select ta_name from table_A                      -- 游标内的数据源
END
IF OBJECT_ID('tempdb..#T1') IS NOT NULL              -- 判断临时表是否存在
BEGIN
	DROP TABLE #T1                                     -- 存在即删除该表
END
CREATE TABLE #T1(                                    -- 创建一个临时表，表内字段与查询字段一致
	t1_name varchar(20)
)
OPEN cursor_tag                                      -- 打开游标
DECLARE @varCursor varchar(20);                      -- 定义游标变量，变量数与游标数据源内数量一致
FETCH NEXT FROM cursor_tag INTO @varCursor           -- 将游标数据赋值给游标变量
	WHILE(@@FETCH_STATUS = 0)                          -- 循环体
	BEGIN
	INSERT #T1
		select tb_name from table_B 
		where tb_name = @varCursor
	FETCH NEXT FROM cursor_tag into @varCursor
	END
CLOSE cursor_tag                                     -- 关闭游标（有打开就有关闭）
SELECT * FROM #T1
DEALLOCATE cursor_tag                                -- 释放游标
DROP TABLE #T1                                       -- 删除临时表
END
```