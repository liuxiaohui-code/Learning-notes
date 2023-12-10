# 触发器

​ mysql 的触发器和存储过程一样，都是嵌入到 mysql 中的一段程序，是 mysql 中管理数据的有力工具。不同的是执行存储过程要使用 call 语句来调用，而触发器的执行不需要使用 call 语句来调用，也不需要手工启动，而是通过对数据表的相关操作来触发、激活从而实现执行。比如当对 student 表进行操作（insert，delete 或 update）时就会激活它执行。

​ 触发器与数据表关系密切，主要用于保护表中的数据。特别是当有多个表具有一定的相互联系的时候，触发器能够让不同的表保持数据的一致性。

​ 在 mysql 中，只有执行 **insert、update 和 delete** 操作时才能激活触发器，其它 sql 语句则不会激活触发器。

## 1. 优缺点

触发器的优点如下：

- 触发器的执行是自动的，当对触发器相关表的数据做出相应的修改后立即执行。
- 触发器可以实施比 foreign key 约束、check 约束更为复杂的检查和操作。
- 触发器可以实现表数据的级联更改，在一定程度上保证了数据的完整性。

触发器的缺点如下：

- 使用触发器实现的业务逻辑在出现问题时很难进行定位，特别是涉及到多个触发器的情况下，会使后期维护变得困难。
- 大量使用触发器容易导致代码结构被打乱，增加了程序的复杂性，
- 如果需要变动的数据量较大时，触发器的执行效率会非常低。

## 2. mysql 支持的触发器

### (1) insert 触发器

在 insert 语句执行之前或之后响应的触发器。
使用 insert 触发器需要注意以下几点：

- 在 insert 触发器代码内，可引用一个名为 new（不区分大小写）的虚拟表来访问被插入的行。
- 在 before insert 触发器中，new 中的值也可以被更新，即允许更改被插入的值（只要具有对应的操作权限）。
- 对于 auto_increment 列，new 在 insert 执行之前包含的值是 0，在 insert 执行之后将包含新的自动生成值。

```sql
-- 非主键自增
create trigger  insert_trigger_name
before insert on table_name
for each row
begin
    set @tem=1;
    select auto_id into @tem from table_name order by card_number desc limit 1;
    set new.auto_id=@tem+1;
end;

```
### (2).update 触发器

在 update 语句执行之前或之后响应的触发器。
使用 update 触发器需要注意以下几点：

- 在 update 触发器代码内，可引用一个名为 new（不区分大小写）的虚拟表来访问更新的值。
- 在 update 触发器代码内，可引用一个名为 old（不区分大小写）的虚拟表来访问 update 语句执行前的值。
- 在 before update 触发器中，new 中的值可能也被更新，即允许更改将要用于 update 语句中的值（只要具有对应的操作权限）。
- old 中的值全部是只读的，不能被更新。

注意：当触发器设计对触发表自身的更新操作时，只能使用 before 类型的触发器，after 类型的触发器将不被允许。

### (3).delete 触发器

在 delete 语句执行之前或之后响应的触发器。
使用 delete 触发器需要注意以下几点：

- 在 delete 触发器代码内，可以引用一个名为 old（不区分大小写）的虚拟表来访问被删除的行。
- old 中的值全部是只读的，不能被更新。

### (5)执行机制

总体来说，触发器使用的过程中，mysql 会按照以下方式来处理错误。

对于事务性表，如果触发程序失败，以及由此导致的整个语句失败，那么该语句所执行的所有更改将回滚；对于非事务性表，则不能执行此类回滚，即使语句失败，失败之前所做的任何更改依然有效。

若 before 触发程序失败，则 mysql 将不执行相应行上的操作。

若在 before 或 after 触发程序的执行过程中出现错误，则将导致调用触发程序的整个语句失败。

仅当 before 触发程序和行操作均已被成功执行，mysql 才会执行 after 触发程序。

## 3.创建

```sql
create trigger  <触发器名>
< before | after > <insert | update | delete > on <表名>
for each row
<触发器主体> ;
```

**1) 触发器名**

​ 触发器的名称，触发器在当前数据库中必须具有唯一的名称。如果要在某个特定数据库中创建，名称前面应该加上数据库的名称。

**2) insert | update | delete**

​ 触发事件，用于指定激活触发器的语句的种类。
​ 注意：三种触发器的执行时间如下。

- insert：将新行插入表时激活触发器。例如，insert 的 before 触发器不仅能被 mysql 的 insert 语句激活，也能被 load data 语句激活。
- delete： 从表中删除某一行数据时激活触发器，例如 delete 和 replace 语句。
- update：更改表中某一行数据时激活触发器，例如 update 语句。

**3) before | after**

​ before 和 after，触发器被触发的时刻，表示触发器是在激活它的语句之前或之后触发。若希望验证新数据是否满足条件，则使用 before 选项；若希望在激活触发器的语句执行之后完成几个或更多的改变，则通常使用 after 选项。

**4) 表名**

​ 与触发器相关联的表名，此表必须是永久性表，不能将触发器与临时表或视图关联起来。在该表上触发事件发生时才会激活触发器。同一个表不能拥有两个具有相同触发时刻和事件的触发器。例如，对于一张数据表，不能同时有两个 before update 触发器，但可以有一个 before update 触发器和一个 before insert 触发器，或一个 before update 触发器和一个 after update 触发器。

**5) 触发器主体**

​ 触发器动作主体，包含触发器激活时将要执行的 mysql 语句。如果要执行多个语句，可使用 begin…end 复合语句结构。

**6) for each row**

​ 一般是指行级触发，对于受触发事件影响的每一行都要激活触发器的动作。例如，使用 insert 语句向某个表中插入多行数据时，触发器会对每一行数据的插入都执行相应的触发器动作。

> 注意：每个表都支持 insert、update 和 delete 的 before 与 after，因此**每个表最多支持 6 个触发器**。每个表的每个事件每次只允许有一个触发器。单一触发器不能与多个事件或多个表关联。

另外，在 mysql 中，若需要查看数据库中已有的触发器，则可以使用 `show triggers;` 语句。

## 4. 查看

```sql
-- 查看当前创建的所有触发器的基本信息
show triggers;
/*
由运行结果可以看到触发器的基本信息。对以上显示信息的说明如下：
trigger 表示触发器的名称，在这里触发器的名称为 trigupdate；
event 表示激活触发器的事件，这里的触发事件为更新操作 update；
table 表示激活触发器的操作对象表，这里为 account 表；
statement 表示触发器执行的操作，这里是向 myevent 数据表中插入一条数据；
timing 表示触发器触发的时间，这里为更新操作之后（after）；
还有一些其他信息，比如触发器的创建时间、sql 的模式、触发器的定义账户和字符集等，这里不再一一介绍。
*/

-- 如果要查看特定触发器的信息或者数据库中触发器较多时，可以直接从 information_schema 数据库中的 triggers 数据表中查找。
select * from information_schema.triggers where trigger_name= '触发器名';
/*
由运行结果可以看到触发器的详细信息。对以上显示信息的说明如下：
trigger_schema 表示触发器所在的数据库；
trigger_name 表示触发器的名称；
event_object_table 表示在哪个数据表上触发；
action_statement 表示触发器触发的时候执行的具体操作；
action_orientation 的值为 row，表示在每条记录上都触发；
action_timing 表示触发的时刻是 after；
还有一些其他信息，比如触发器的创建时间、sql 的模式、触发器的定义账户和字符集等，这里不再一一介绍。
*/
```

## 5.删除

```sql
# 删除
drop trigger [ if exists ] [数据库名] <触发器名> ;
```
