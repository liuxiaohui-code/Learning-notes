
# 存储过程

​ 存储过程是一组为了完成特定功能的 SQL 语句集合。使用存储过程的目的是将常用或复杂的工作预先用 SQL 语句写好并用一个指定名称存储起来，这个过程经编译和优化后存储在数据库服务器中，因此称为存储过程。当以后需要数据库提供与已定义好的存储过程的功能相同的服务时，只需调用“CALL 存储过程名字”即可自动完成。

​ 一个存储过程是一个可编程的函数，它在数据库中创建并保存，一般由 SQL 语句和一些特殊的控制结构组成。当希望在不同的应用程序或平台上执行相同的特定功能时，存储过程尤为合适。

## 1. 优点

**1) 封装性**

​ 通常完成一个逻辑功能需要多条 SQL 语句，而且各个语句之间很可能传递参数，所以，编写逻辑功能相对来说稍微复杂些，而存储过程可以把这些 SQL 语句包含到一个独立的单元中，使外界看不到复杂的 SQL 语句，只需要简单调用即可达到目的。并且数据库专业人员可以随时对存储过程进行修改，而不会影响到调用它的应用程序源代码。

**2) 可增强 SQL 语句的功能和灵活性**

​ 存储过程可以用流程控制语句编写，有很强的灵活性，可以完成复杂的判断和较复杂的运算。

**3) 可减少网络流量**

​ 由于存储过程是在服务器端运行的，且执行速度快，因此当客户计算机上调用该存储过程时，网络中传送的只是该调用语句，从而可降低网络负载。

**4) 高性能**

​ 当存储过程被成功编译后，就存储在数据库服务器里了，以后客户端可以直接调用，这样所有的 SQL 语句将从服务器执行，从而提高性能。但需要说明的是，存储过程不是越多越好，过多的使用存储过程反而影响系统性能。

**5) 提高数据库的安全性和数据的完整性**

​ 存储过程提高安全性的一个方案就是把它作为中间组件，存储过程里可以对某些表做相关操作，然后存储过程作为接口提供给外部程序。这样，外部程序无法直接操作数据库表，只能通过存储过程来操作对应的表，因此在一定程度上，安全性是可以得到提高的。

**6) 使数据独立**

​ 数据的独立可以达到解耦的效果，也就是说，程序可以调用存储过程，来替代执行多条的 SQL 语句。这种情况下，存储过程把数据同用户隔离开来，优点就是当数据表的结构改变时，调用表不用修改程序，只需要数据库管理者重新编写存储过程即可。

## 2. 创建

```sql
create procedure <过程名> ( [过程参数[,…] ] ) <过程体>

[过程参数[,…] ] 格式：
	[ IN | OUT | INOUT ] <参数名> <类型>


-- ------------------------------例:
delimiter // -- 使用//作为mysql分隔符,替代;
create procedure hello(
   out id int,
   in id2 int,
   inout id3 int
)
begin
select id  into id2 from hello where id = id2 or id=id3;
end //
delimiter ; -- 恢复 ;

-- 使用
call hello(@id,'id0000',@id3)  -- 所有MySQL变量都必须以@开始。
select @id;-- 查看结果
-- 删除
drop procedure  if exists hello;
```

## 3. 查看

```sql
# 查看存储过程的状态
SHOW PROCEDURE STATUS LIKE 存储过程名;
# 查看存储过程的定义
SHOW CREATE PROCEDURE 存储过程名;
```

​ SHOW STATUS 语句只能查看存储过程是操作的哪一个数据库、存储过程的名称、类型、谁定义的、创建和修改时间、字符编码等信息。但是，这个语句不能查询存储过程的集体定义，如果需要查看详细定义，需要使用 SHOW CREATE 语句。

​ 存储过程的信息都存储在 information_schema 数据库下的 Routines 表中，可以通过查询该表的记录来查询存储过程的信息，SQL 语句如下：

```sql
SELECT * FROM information_schema.Routines WHERE ROUTINE_NAME=存储过程名;
```

​ 在 information_schema 数据库下的 routines 表中，存储着所有存储过程的定义。所以，使用 SELECT 语句查询 routines 表中的存储过程和函数的定义时，一定要使用 routine_name 字段指定存储过程的名称，否则，将查询出所有的存储过程的定义。

## 4. 修改

MySQL 中修改存储过程的语法格式如下：

```sql
ALTER PROCEDURE 存储过程名 [ 特征 ... ]
/*
特征指定了存储过程的特性，可能的取值有：
	CONTAINS SQL 表示子程序包含 SQL 语句，但不包含读或写数据的语句。
	NO SQL 表示子程序中不包含 SQL 语句。
	READS SQL DATA 表示子程序中包含读数据的语句。
	MODIFIES SQL DATA 表示子程序中包含写数据的语句。
	SQL SECURITY { DEFINER |INVOKER } 指明谁有权限来执行。
	DEFINER 表示只有定义者自己才能够执行。
	INVOKER 表示调用者可以执行。
	COMMENT 'string' 表示注释信息。
*/
```

## 5. 删除

```sql
DROP PROCEDURE [ IF EXISTS ] <过程名> ;
# IF EXISTS：指定这个关键字，用于防止因删除不存在的存储过程而引发的错误。
```

## 6. 存储函数

```sql
-- 创建
CREATE FUNCTION sp_name ([func_parameter[...]])
RETURNS type
[characteristic ...]
routine_body

/*
	sp_name 参数：表示存储函数的名称；
	func_parameter：表示存储函数的参数列表；
	RETURNS type：指定返回值的类型；
	characteristic 参数：指定存储函数的特性，该参数的取值与存储过程是一样的；
	routine_body 参数：表示 SQL 代码的内容，可以用 BEGIN...END 来标示 SQL 代码的开始和结束。

注意：在具体创建函数时，函数名不能与已经存在的函数名重名。除了上述要求外，推荐函数名命名（标识符）为 function_xxx 或者 func_xxx。
*/

DELIMITER //
CREATE FUNCTION func_student(id INT(11))
RETURNS VARCHAR(20)
COMMENT '查询某个学生的姓名'
BEGIN
RETURN(SELECT name FROM tb_student WHERE tb_student.id = id);
END //
Query OK, 0 rows affected (0.10 sec)
DELIMITER ;
```

func_parameter 可以由多个参数组成，其中每个参数由参数名称和参数类型组成，其形式如下：

```sql
[IN | OUT | INOUT] param_name type;
```

其中：

- IN 表示输入参数，OUT 表示输出参数，INOUT 表示既可以输入也可以输出；
- param_name 参数是存储函数的参数名称；
- type 参数指定存储函数的参数类型，该类型可以是 MySQL 数据库的任意数据类型。

查看存储函数的语法如下：

```sql
SHOW FUNCTION STATUS LIKE 存储函数名;
SHOW CREATE FUNCTION 存储函数名;
SELECT * FROM information_schema.Routines WHERE ROUTINE_NAME=存储函数名;
```

可以发现，操作存储函数和操作存储过程不同的是将 PROCEDURE 替换成了 FUNCTION。同样，修改存储函数的语法如下：

```sql
ALTER FUNCTION 存储函数名 [ 特征 ... ]
```

存储函数的特征与存储过程的基本一样。
删除存储过程的语法如下：

```sql
DROP FUNCTION [ IF EXISTS ] <函数名>
```

## 7. 调用

存储过程通过 **CALL** 语句来调用，存储函数的使用方法与 MySQL 内部函数的使用方法相同。执行存储过程和存储函数需要拥有 EXECUTE 权限（EXECUTE 权限的信息存储在 `information_schema `数据库下的 `USER_PRIVILEGES `表中）。

```sql
-- CALL 语句接收存储过程的名字以及需要传递给它的任意参数，基本语法形式如下：
CALL sp_name([parameter[...]]);
-- 存储函数的使用方法与 MySQL 内部函数的使用方法是一样的
SELECT func_student(3);
```

## 8. 变量

```sql
# 定义,声明
DECLARE var_name[,...] type [DEFAULT value]
# 赋值
SET var_name = expr[,var_name = expr]...
# 赋值2
SELECT col_name [...] INTO var_name[,...]
FROM table_name WEHRE condition;
```

> 注意：@X 表示用户变量，使用 SET 语句为其赋值，用户变量与连接有关，一个客户端定义的变量不能被其他客户端所使用，当客户端退出时，该客户端连接的所有变量将自动释放。

## 9. 定义条件和处理程序

```sql
# 定义条件
DECLARE condition_name CONDITION FOR condition_value;
condition value:
SQLSTATE [VALUE] sqlstate_value | mysql_error_code
/*
其中：
condition_name 参数表示条件的名称；
condition_value 参数表示条件的类型；
sqlstate_value 参数和 mysql_error_code 参数都可以表示 MySQL 的错误。sqlstate_value 表示长度为 5 的字符串类型错误代码，mysql_error_code 表示数值类型错误代码。例如 ERROR 1146(42S02) 中，sqlstate_value 值是 42S02，mysql_error_code 值是 1146。
*/

eg:定义“ERROR 1146 (42S02)”这个错误，名称为 can_not_find
-- 方法一：使用sqlstate_value
DECLARE can_not_find CONDITION FOR SQLSTATE '42S02';

-- 方法二：使用 mysql_error_code
DECLARE can_not_find CONDITION FOR 1146;
```

**处理程序**

```sql
DECLARE handler_type HANDLER FOR condition_value[...] sp_statement ;

handler_type:
	CONTINUE | EXIT | UNDO
condition_value:
	SQLSTATE [VALUE] sqlstate_value | condition_name | SQLWARNING | NOT FOUND | SQLEXCEPTION | mysql_error_code
/*
	其中，handler_type 参数指明错误的处理方式，该参数有 3 个取值。这 3 个取值分别是 CONTINUE、EXIT 和 UNDO。
	CONTINUE 表示遇到错误不进行处理，继续向下执行；
	EXIT 表示遇到错误后马上退出；
	UNDO 表示遇到错误后撤回之前的操作，MySQL 中暂时还不支持这种处理方式。
注意：	通常情况下，执行过程中遇到错误应该立刻停止执行下面的语句，并且撤回前面的操作。但是，MySQL 中现在还不能支持 UNDO 操作。因此，遇到错误时最好执行 EXIT 操作。如果事先能够预测错误类型，并且进行相应的处理，那么可以执行 CONTINUE 操作。

参数指明错误类型，该参数有 6 个取值：
sqlstate_value：包含 5 个字符的字符串错误值；
condition_name：表示 DECLARE 定义的错误条件名称；
SQLWARNING：匹配所有以 01 开头的 sqlstate_value 值；
NOT FOUND：匹配所有以 02 开头的 sqlstate_value 值；
SQLEXCEPTION：匹配所有没有被 SQLWARNING 或 NOT FOUND 捕获的 sqlstate_value 值；
mysql_error_code：匹配数值类型错误代码。

sp_statement 参数为程序语句段，表示在遇到定义的错误时，需要执行的一些存储过程或函数。
*/

eg:
# 方法一：捕获 sqlstate_value
DECLARE CONTINUE HANDLER FOR SQLSTATE '42S02' SET @info='CAN NOT FIND';
# 捕获 sqlstate_value 值。如果遇到 sqlstate_value 值为 42S02，执行 CONTINUE 操作，并且输出“CAN NOT FIND”信息。

//方法二：捕获 mysql_error_code
DECLARE CONTINUE HANDLER FOR 1146 SET @info='CAN NOT FIND';

//方法三：先定义条件，然后调用
DECLARE can_not_find CONDITION FOR 1146;
DECLARE CONTINUE HANDLER FOR can_not_find SET @info='CAN NOT FIND';

//方法四：使用 SQLWARNING
DECLARE EXIT HANDLER FOR SQLWARNING SET @info='ERROR';

//方法五：使用 NOT FOUND
DECLARE EXIT HANDLER FOR NOT FOUND SET @info='CAN NOT FIND';

//方法六：使用 SQLEXCEPTION
DECLARE EXIT HANDLER FOR SQLEXCEPTION SET @info='ERROR';
```

## 10. 游标

只能在存储过程中使用

```sql
-- 声明
DECLARE cursor_name CURSOR FOR select_statement;
-- select_statement 表示 SELECT 语句，可以返回一行或多行数据。

-- 打开游标
OPEN cursor_name;
-- 打开一个游标时，游标并不指向第一条记录，而是指向第一条记录的前边。

-- 使用游标 FETCH...INTO
FETCH cursor_name INTO var_name [,var_name]...
-- 将游标 cursor_name 中 SELECT 语句的执行结果保存到变量参数 var_name 中
-- 使用用游标类似高级语言中的数组遍历，当第一次使用游标时，此时游标指向结果集的第一条记录。

-- 关闭游标
CLOSE cursor_name;
/*
	CLOSE 释放游标使用的所有内部内存和资源，因此每个游标不再需要时都应该关闭。
	在一个游标关闭后，如果没有重新打开，则不能使用它。但是，使用声明过的游标不需要再次声明，用 OPEN 语句打开它就可以了。
	如果你不明确关闭游标，MySQL 将会在到达 END 语句时自动关闭它。游标关闭之后，不能使用 FETCH 来使用该游标。
*/
```

## 11. 流程控制

### (1) if

```sql
IF search_condition THEN statement_list
    [ELSEIF search_condition THEN statement_list]...
    [ELSE statement_list]
END IF
```

> 注意：MySQL 中的 `IF( ) 函数`不同于这里的 IF 语句。

### (2) case

```sql
CASE case_value
    WHEN when_value THEN statement_list
    [WHEN when_value THEN statement_list]...
    [ELSE statement_list]
END CASE

/*
其中：
case_value 参数表示条件判断的变量，决定了哪一个 WHEN 子句会被执行；
when_value 参数表示变量的取值，如果某个 when_value 表达式与 case_value 变量的值相同，则执行对应的 THEN 关键字后的 statement_list 中的语句；
statement_list 参数表示 when_value 值没有与 case_value 相同值时的执行语句。
CASE 语句都要使用 END CASE 结束。
*/
# 另一种形式
CASE
    WHEN search_condition THEN statement_list
    [WHEN search_condition THEN statement_list] ...
    [ELSE statement_list]
END CASE
/*
	其中，search_condition 参数表示条件判断语句；statement_list 参数表示不同条件的执行语句。
	与上述语句不同的是，该语句中的 WHEN 语句将被逐个执行，直到某个 search_condition 表达式为真，则执行对应 THEN 关键字后面的 statement_list 语句。如果没有条件匹配，ELSE 子句里的语句被执行。
*/
```

### (3) LOOP

```sql
# LOOP 语句本身没有停止循环的语句，必须使用 LEAVE 语句等才能停止循环，跳出循环过程
[begin_label:]LOOP
    statement_list
END LOOP [end_label]
/*
其中，begin_label 参数和 end_label 参数分别表示循环开始和结束的标志，这两个标志必须相同，而且都可以省略；statement_list 参数表示需要循环执行的语句。
*/
```

### (4) LEAVE

```SQL
-- 跳出循环控制
LEAVE label
```

### (5) ITERATE

```sql
-- 用来跳出本次循环，直接进入下一次循环
ITERATE label
```

### (6) REPEAT

```sql
[begin_label:] REPEAT
    statement_list
    UNTIL search_condition
END REPEAT [end_label]
/*
有条件控制的循环语句，每次语句执行完毕后，会对条件表达式进行判断，如果表达式返回值为 TRUE，则循环结束，否则重复执行循环中的语句。
*/
```

### (7) WHILE

```sql
[begin_label:] WHILE search_condition DO
    statement list
END WHILE [end label]
/*
有条件控制的循环语句。WHILE 语句和 REPEAT 语句不同的是，WHILE 语句是当满足条件时，执行循环内的语句，否则退出循环
*/
```

