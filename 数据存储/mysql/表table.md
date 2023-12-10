# DDL 操作表结构

## 创建新表 create

1. 新建表

```sql
CREATE TABLE 表名 [if not exists]( -- 当表不存在时创建它
	列名1 数据类型 [约束1] [约束2] [...], -- 逗号分隔,列名唯一,不区分大小写,
	列名2 数据类型 [约束],
	列名n 数据类型 [约束] -- 最后一行不要逗号
)engine=InnoDB default charset=utf8mb4 collate=utf8mb4_0900_ai_ci;
```

2. 约束

   1. 自增 `auto_increment` ,每个表只允许一列,且被索引;`select last_insert_id();`返回最后一个自增值
   2. 关键词 `primary key(,,,)` ,主键唯一,非空
   3. 非空 `not null`
   4. `default 数据` 默认值,使用默认值而非空值
   5. `unique` 唯一

3. 字符集

## 删除表 drop

```sql
  drop table 表名;
```

## 查看 show

1. 当前数据库中的所有表 `show tables;`
2. 查看表的字段信息
   1. `desc 表名；`
   2. `describe 表名;`
   3. `show columns from 表名 ; `
3. 查看表格的创建细节 `show create table 表名;`

## 控制 alter

1. 增加列 `alter table 表名 add 新列名 新的数据类型; `
2. 修改列 `alter table 表名 change 旧列名 新列名 新的数据类型;`
3. 删除列,一次只能删一列 `alter table 表名 drop 列名;`
4. 修改表名 `alter table 源表名 rename 新表名 ; `
5. 修改表的字符集 `alter table 表名 character set 编码方式;`

# DML 操作表中数据

- **DML（Data Manipulation Language）**：数据操作语言，用来定义数据库记录（数据）增删改。

## 1. 插入 insert

```sql
insert into 表名 values(,,,,); -- 插入完整的行,对每个列必须提供一个值,如果没有使用 null , 因为列名顺序的问题,不推荐使用
insert into 表名(列名,,) values(数据值,,); -- 单行插入
insert into 表名(列名,,) values(第一行数据),(第二行数据),(),(); -- 多行插入
insert into 表名1(列名,,) select 列名,,, from 表名2; -- 将从表2中查出的数据插入表一中
```

- 非数值的列值两侧需要加单引号
- 提高整体性能;当表内索引很多,insert 操作耗时很高时,在 `insert` 和 `into` 之间添加关键字 `low_priority`,降低优先级,亦适用于 update\delete

```sql
select * from user where host = '28.102.171.%' and user = 'root';
flush privileges;
```

## 2. 删除 delete/truncate

```sql
 DELETE from 表名 【WHERE 列名=值】; # 删除表中的数据，表结构还在;删除后的数据可以找回

 TRUNCATE TABLE 表名 ; # 删除是把表直接DROP掉，然后再创建一个同样的新表;删除的数据不能找回。执行速度比DELETE快。
```

> #### TRUNCATE 和 DELETE 的区别
>
> 从逻辑上说，TRUNCATE 语句与 DELETE 语句作用相同，但是在某些情况下，两者在使用上有所区别。
>
> - DELETE 是 DML 类型的语句；TRUNCATE 是 DDL 类型的语句。它们都用来清空表中的数据。
> - DELETE 是逐行一条一条删除记录的；TRUNCATE 则是直接删除原来的表，再重新创建一个一模一样的新表，而不是逐行删除表中的数据，执行数据比 DELETE 快。因此需要删除表中全部的数据行时，尽量使用 TRUNCATE 语句， 可以缩短执行时间。
> - DELETE 删除数据后，配合事件回滚可以找回数据；TRUNCATE 不支持事务的回滚，数据删除后无法找回。
> - DELETE 删除数据后，系统不会重新设置自增字段的计数器；TRUNCATE 清空表记录后，系统会重新设置自增字段的计数器。
> - DELETE 的使用范围更广，因为它可以通过 WHERE 子句指定条件来删除部分数据；而 TRUNCATE 不支持 WHERE 子句，只能删除整体。
> - DELETE 会返回删除数据的行数，但是 TRUNCATE 只会返回 0，没有任何意义。

## 3. 修改 update

```sql
UPDATE 表名 SET 列名1=列值1,列名2=列值2 ... WHERE 列名=值;
update ignore ..; -- 即使发生错误也继续更新
```
# DQL 数据查询

DQL（Data Query Language）：数据查询语言，用来查询记录（数据）查询。

## 语法

```sql
SELECT 要查询的列名称,col2,col3 as col_new_name
FROM 表名称
WHERE 限定条件 /*行条件*/
GROUP BY grouping_columns /*对结果分组，注意:如果查询语句中有分组操作，则select后面能添加的只能是聚合函数和被分组的列名*/
HAVING condition /*分组后的行条件*/
ORDER BY sorting_columns /*对结果排序,asc升序,desc降序,默认不写的话是升序*/
LIMIT offset_start, row_count /* offset_start = row_count*(page_number - 1)
```

1. `select`
   1. 列名
   2. 聚合函数
   3. `*` 所有列
   4. 可以多个,用`,`分隔
   5. `distinct` 去除重复;直接放在列名的前面,应用于所有列而不仅是前置它的列,多个列前,所有都不同才会去重
   6. `+-*/`算数运算
   7. `as` 重命名,包括列名\表名,可省略
2. `from` 查询对象,后跟表名
3. `where` 查询条件;分组前查询;
   1. 操作符 `= <> != < <= > >= between...and...`
   2. `and or` 优先处理 and,
   3. `in (1,2,3)`
   4. 模糊查询`like _一个字符 %不限字符`
   5. 空值`is null`
   6. `not`否定它之后所跟的任何条件
   7. 正则表达式 `regexp '正则表达式'`
4. `group by` 分组;
   1. 使用分组时,查询对象只能为分组列名和聚合函数,且列名不能使用别名
   2. 条件为列名或有效表达式,但不能为聚集函数
   3. 可以多个列,进行分组嵌套
5. `having` 分组后的查询条件
6. `order by <col> asc|desc ` 排序,`asc`升序,`desc`降序;
   1. 字符串以字母顺序;
   2. 使用显示的列或非显示的列都可以;
   3. 多个列排序,又先后,先相同时按后排列
7. `limit <开始下标>, <条数>` 分页,如果只给定一个参数：它表示返回最大的记录行数目;代替语法 `limit 条数 offset 开始下标`

like 与 regexp 的区别: LIKE 匹配整个列。如果被匹配的文本在列值中出现，LIKE 将不会找到它，相应的行也不被返回（除非使用通配符）。而 REGEXP 在列值内进行匹配，如果被匹配的文本在列值中出现，REGEXP 将会找到它，相应的行将被返回。即:`like '1000'`不会返回列值等于 1000 的行,但`regexp '1000'`会返回

## 调优:

1. 使用 in 不使用 or
2. 不要过度使用 like,除非有必要,通配符不要用在开头
3. 在查询记录量低于 100 时，查询时间基本没有差距，随着查询记录量越来越大，所花费的时间也会越来越多。
   随着查询偏移的增大，尤其查询偏移大于 10 万以后，查询时间急剧增加。
   这种分页查询方式会从数据库第一条记录开始扫描，所以越往后，查询速度越慢，而且查询的数据越多，也会拖慢总查询速度。
   对于以上分页查询，更好的方法是记住上一次获取到的最大 id，然后在下一次查询中作为条件传入

## 语法顺序

以下是 SQL 中各个子句的语法顺序，前面括号内的数字代表了它们的逻辑执行顺序：

```sql
(6)SELECT [DISTINCT | ALL] col1, col2, agg_func(col3) AS alias
(1)  FROM t1 JOIN t2
(2)    ON (join_conditions)
(3) WHERE where_conditions
(4) GROUP BY col1, col2
(5)HAVING having_condition
(7) UNION [ALL]
   ...
(8) ORDER BY col1 ASC,col2 DESC
(9)OFFSET m ROWS FETCH NEXT num_rows ROWS ONLY;

```

1. 首先，FROM 和 JOIN 是 SQL 语句执行的第一步。它们的逻辑结果是一个笛卡尔积，决定了接下来要操作的数据集。注意逻辑执行顺序并不代表物理执行顺序，实际上数据库在获取表中的数据之前会使用 ON 和 WHERE 过滤条件进行优化访问；
2. 其次，应用 ON 条件对上一步的结果进行过滤并生成新的数据集；
3. 然后，执行 WHERE 子句对上一步的数据集再次进行过滤。WHERE 和 ON 大多数情况下的效果相同，但是外连接查询有所区别，我们将会在下文给出示例；
4. 接着，基于 GROUP BY 子句指定的表达式进行分组；同时，对于每个分组计算聚合函数 agg_func 的结果。经过 GROUP BY 处理之后，数据集的结构就发生了变化，只保留了分组字段和聚合函数的结果；
5. 如果存在 GROUP BY 子句，可以利用 HAVING 针对分组后的结果进一步进行过滤，通常是针对聚合函数的结果进行过滤；
6. 接下来，SELECT 可以指定要返回的列；如果指定了 DISTINCT 关键字，需要对结果集进行去重操作。另外还会为指定了 AS 的字段生成别名；
7. 如果还有集合操作符（UNION、INTERSECT、EXCEPT）和其他的 SELECT 语句，执行该查询并且合并两个结果集。对于集合操作中的多个 SELECT 语句，数据库通常可以支持并发执行；
8. 然后，应用 ORDER BY 子句对结果进行排序。如果存在 GROUP BY 子句或者 DISTINCT 关键字，只能使用分组字段和聚合函数进行排序；否则，可以使用 FROM 和 JOIN 表中的任何字段排序；
9. 最后，OFFSET 和 FETCH（LIMIT、TOP）限定了最终返回的行数。

## regexp 正则表达式

1. `.` 任意字符
2. 默认不区分大小写,区分大小写可以使用 `regexp binary`关键字
3. `|` or 匹配 `1000|2000`
4. `[123]` 匹配特定字符 1 或 2 或 3
   1. 范围`[0-9][a-z]`
5. 定位符
   1. `^` 文本开头,另外还可以在集合[]的开头否定此集合
   2. `$` 文本结尾
   3. `[[:<:]]` 词的开始
   4. `[[:>:]]` 词的开始
6. 匹配特殊字符需要转义`\\`
   1. `\\f` 换页
   2. `\\n` 换行
   3. `\\r` 回车
   4. `\\t` 制表
   5. `\\v` 纵向制表
7. 匹配字符类
   1. `[:alnum:]` 任意字母和数字（同`[a-zA-Z0-9]`）
   2. `[:alpha:]` 任意字符（同`[a-zA-Z]`）
   3. `[:blank:]` 空格和制表（同`[\\t]`）
   4. `[:cntrl:]` ASCII 控制字符（ASCII 0 到 31 和 127）
   5. `[:digit:]` 任意数字（同`[0-9]`）
   6. `[:graph:]` 与`[:print:]`相同，但不包括空格
   7. `[:lower:]` 任意小写字母（同`[a-z]`）
   8. `[:print:]` 任意可打印字符
   9. `[:punct:]` 既不在`[:alnum:]`又不在`[:cntrl:]`中的任意字符
   10. `[:space:]` 包括空格在内的任意空白字符（同`[\\f\\n\\r\\t\\v]`）
   11. `[:upper:]` 任意大写字母（同`[A-Z]`）
   12. `[:xdigit:]` 任意十六进制数字（同`[a-fA-F0-9]`）
8. 匹配多个实例
   1. `*` 0 个或多个匹配
   2. `+` 1 个或多个匹配（等于{1,}）
   3. `?` 0 个或 1 个匹配（等于{0,1}）
   4. `{n}` 指定数目的匹配
   5. `{n,}` 不少于指定数目的匹配
   6. `{n,m}` 匹配数目的范围（m 不超过 255）
9. 简单的正则表达式测试: `select '字符串' regexp '正则表达式'`

## 子查询

子查询一般与 IN 操作符结合使用，但也可以用于测试等于（=）、不等于（<>）等。

```sql
select * from table_name where colume_name in (
    select colume_name from table_name2
);
```

使用计算字段作为子查询

```sql
select * from table_name where colume_name > (
    select avg(colume_name) from table_name2
);
```

注意: 如果可以,不要使用子查询,因为子查询会执行多次(最终找到几个结果,就会执行几次)

## 多表查询

### 内连接

`join` 和 `inner join` 等同,得到共有的数据

```sql
select a.*,b.* from a,b where a.a1=b.b1;
select a.*,b.* from a [inner] join b on a.a1=b.b1; -- (首选)

-- 多个表
select a.a,b.b,c.c from a,b,c where a.a1=b.b1 and a.a2 = c.c1;
```

### 外连接

```sql
-- 左连接 left join 等同于 left outer join ,得到左表a的全部数据和共有数据
select a.*, b.* from tablea a left join tableb b on a.id = b.id

-- 右连接 right join,right outer join ,得到右表b的全部数据和共有数据
select a.*, b.* from tablea a right join tableb b on a.id = b.id

-- 自然连接 union 将两个表数据上下连接,放到一张表中,并去重
select a.*, b.* from tablea a left join tableb b on a.id = b.id
union -- 可以通过union得到full join
select a.*, b.* from tablea a right join tableb b on a.id = b.id
```

### 交叉连接 cross join

实际应用中还有这样一种情形，想得到 A，B 记录的排列组合，即笛卡儿积，这个就不好用集合和元素来表示了。需要用到 cross join

```sql
select a.*, b.* from tablea a cross join tableb b where a.id = b.id; -- 交叉连接没有 on
```

内连接不加 on 就等同于交叉连接

## 全文本查询

使用通配符和正则表达式搜索大文本字段非常耗时,所有启动全文本查询.

1. 建表索引被搜索的列`fulltext()`,并且随着数据的改变不断地重新索引(text)
2. 引擎 `engine=MyISAM`
3. 不要在导入数据时使用 fulltext,更新索引需要花费时间;如果正在导入数据到一个新表,应该先导入所有数据,然后再修改表,定义 fulltext.
4. 索引之后
   1. `match(colume)` 指定被搜索的列
   2. `against('xxxx')` 指定搜索表达式
   3. eg: `select note_text from notes match(note_text) against('xxx')`
   4. 搜索不区分大小写,除非使用`binary` 方式
5. 查询扩展 `against('xxx' with query expansion)`
6. 布尔文本搜索,**即使没有 FULLTEXT 索引也可以使用**
   1. `against('xxx zzz -yyy*' in boolean mode)` 匹配包含 xxx\zzz 至少一个,不包含以 yyy 开头的的词的行
   2. 布尔操作符
      1. - 包含，词必须存在
      2. - 排除，词必须不出现
      3. > 包含，而且增加等级值
      4. < 包含，且减少等级值
      5. () 把词组成子表达式（允许这些子表达式作为一个组被包含、排除、排列等）
      6. ~ 取消一个词的排序值
      7. - 词尾的通配符
      8. "" 定义一个短语（与单个词的列表不一样，它匹配整个短语以便包含或排除这个短语）

# 数据完整性

用来保证存放到数据库中的数据是有效的,即数据的有效性和准确性
确保数据的完整性 = 在创建表时给表中添加约束
完整性的分类：

- 实体完整性(行完整性):
  主键约束：primary key  
  唯一约束：unique [key]
  自动增长：auto_increment

```sql
# primary key
CREATE TABLE xx(
    xx xx primary key, # 1
    ... ,
    primary key(xx,xx,...)  # 2
);
ALTER TABLE student ADD PRIMARY KEY (id); # 3
```

- 域完整性(列完整性):
  非空约束：not null
  默认约束：default
- 引用完整性(关联表完整性):
  外键约束: foreign key ---- `constraint 自定义外键名称 foreign key(外键列名) references 主键表名(主键列名)`

建议这些约束应该在创建表的时候设置
多个约束条件之间使用空格间隔

# 多表查询

多表查询有如下几种：

- 合并结果集:UNION 、 UNION ALL `合并结果集就是把两个select语句的查询结果合并到一起！,UNION 去重，UNION ALL不去重，eg: select ... union select ...;`

- 连接查询 `是求出多个表的乘积,连接查询会产生笛卡尔积`

  - 内连接 [INNER] JOIN ON

    ```sql
    # 内连接的特点：查询结果必须满足条件。
    # 1
    select 列名 from 表1 inner join 表2 on 表1.列名=表2.列名 //外键列的关系
    where..... ;
    # 2
    select 列名 from 表1,表2 where 表1.列名=表2.列名 and ...(其他条件)
    /*
    注:
    	<1>表1和表2的顺序可以互换
     	<2>找两张表的等值关系时,找表示相同含义的列作为等值关系。
     	<3>点操作符表示“的”,格式:表名.列名
     	<4>可以使用as,给表名起别名,注意定义别名之后,统一使用别名
     */
    ```

  - 外连接 OUTER JOIN ON `外连接的特点：查询出的结果存在不满足条件的可能。`

    - 左外连接 LEFT [OUTER] JOIN

    - 右外连接 RIGHT [OUTER] JOIN
    - 全外连接（MySQL 不支持）FULL JOIN

    ```sql
    # 左外联:
    select 列名 from 主表 left join 次表 on 主表.列名=次表.列名;
    # 右外联:
    select 列名 from 次表 right join 主表 on 主表.列名=次表.列名;

    /*
    -- 1.主表数据全部显示，次表数据匹配显示，能匹配到的显示数据，匹配不成功的显示null
    -- 2.主表和次表不能随意调换位置
    */
    ```

  - 自然连接 NATURAL JOIN `自然连接是一种特殊的等值连接，他要求两个关系表中进行连 接的必须是相同的属性列（名字相同），无须添加连接条件，并且在结果中消除重复的属性列。`

- 子查询

> 笛卡尔积，假设集合 A={a,b}，集合 B={0,1,2}，则两个集合的笛卡尔积为{(a,0),(a,1),
> (a,2),(b,0),(b,1),(b,2)}。


