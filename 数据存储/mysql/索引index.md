# 索引

​ 索引是一种特殊的数据库结构，由数据表中的一列或多列组合而成，可以用来快速查询数据表中有某一特定值的记录。

​ 通过索引，查询数据时不用读完记录的所有信息，而只是查询索引列。否则，数据库系统将读取每条记录的所有信息进行匹配。

​ 索引就是根据表中的一列或若干列按照一定顺序建立的列值与记录行之间的对应关系表，实质上是一张描述索引列的列值与原表中记录行之间一 一对应关系的有序表。

## 1. 访问数据库表的行数据

在 MySQL 中，通常有以下两种方式访问数据库表的行数据：

**1）.顺序访问**

​ 顺序访问是在表中实行全表扫描，从头到尾逐行遍历，直到在无序的行数据中找到符合条件的目标数据。

​ 顺序访问实现比较简单，但是当表中有大量数据的时候，效率非常低下。例如，在几千万条数据中查找少量的数据时，使用顺序访问方式将会遍历所有的数据，花费大量的时间，显然会影响数据库的处理性能。

**2）.索引访问**

​ 索引访问是通过遍历索引来直接访问表中记录行的方式。

​ 使用这种方式的前提是对表建立一个索引，在列上创建了索引之后，查找数据时可以直接根据该列上的索引找到对应记录行的位置，从而快捷地查找到数据。索引存储了指定列数据值的指针，根据指定的排序顺序对这些指针排序。

## 2. 优缺点

索引有其明显的优势，也有其不可避免的缺点。

**优点**

索引的优点如下：

- 通过创建唯一索引可以保证数据库表中每一行数据的唯一性。
- 可以给所有的 MySQL 列类型设置索引。
- 可以大大加快数据的查询速度，这是使用索引最主要的原因。
- 在实现数据的参考完整性方面可以加速表与表之间的连接。
- 在使用分组和排序子句进行数据查询时也可以显著减少查询中分组和排序的时间

**缺点**

增加索引也有许多不利的方面，主要如下：

- 创建和维护索引组要耗费时间，并且随着数据量的增加所耗费的时间也会增加。
- 索引需要占磁盘空间，除了数据表占数据空间以外，每一个索引还要占一定的物理空间。如果有大量的索引，索引文件可能比数据文件更快达到最大文件尺寸。
- 当对表中的数据进行增加、删除和修改的时候，索引也要动态维护，这样就降低了数据的维护速度。

## 3. 索引类型

MySQL 目前主要有以下几种索引类型：

- 普通索引
- 唯一索引
- 主键索引
- 组合索引
- 全文索引

语句：

```sql
CREATE TABLE table_name[col_name data type]
[unique|fulltext][index|key][index_name](col_name[length])[asc|desc];
```

> 1.unique|fulltext 为可选参数，分别表示唯一索引、全文索引
> 2.index 和 key 为同义词，两者作用相同，用来指定创建索引
> 3.col_name 为需要创建索引的字段列，该列必须从数据表中该定义的多个列中选择
> 4.index_name 指定索引的名称，为可选参数，如果不指定，默认 col_name 为索引值
> 5.length 为可选参数，表示索引的长度，只有字符串类型的字段才能指定索引长度
> 6.asc 或 desc 指定升序或降序的索引值存储

### (1) 普通索引

```sql
-- 直接创建索引
CREATE INDEX index_name ON table(column(length));
-- 修改表结构的方式添加索引
ALTER TABLE table_name ADD INDEX index_name ON (column(length));
-- 创建表的时候同时创建索引
CREATE TABLE `table` (
    `id` int(11) NOT NULL AUTO_INCREMENT ,
    `title` char(255) CHARACTER NOT NULL ,
    `content` text CHARACTER NULL ,
    `time` int(10) NULL DEFAULT NULL ,
    PRIMARY KEY (`id`),
    INDEX index_name (title(length))
);
-- 删除索引
DROP INDEX index_name ON table;
```

### (2) 唯一索引

```sql
-- 直接创建索引
CREATE UNIQUE INDEX indexName ON table(column(length))
-- 修改表结构的方式添加索引
ALTER TABLE table_name ADD UNIQUE indexName ON (column(length))
-- 创建表的时候同时创建索引
CREATE TABLE `table` (
    `id` int(11) NOT NULL AUTO_INCREMENT ,
    `title` char(255) CHARACTER NOT NULL ,
    `content` text CHARACTER NULL ,
    `time` int(10) NULL DEFAULT NULL ,
    PRIMARY KEY (`id`),
    UNIQUE indexName (title(length))
);
-- 删除索引
DROP INDEX index_name ON table;
```

### (3) 主键索引

​ 是一种特殊的唯一索引，一个表只能有一个主键，不允许有空值。一般是在建表的时候同时创建主键索引：

```sql
CREATE TABLE `table` (
    `id` int(11) NOT NULL AUTO_INCREMENT ,
    `title` char(255) NOT NULL ,
    PRIMARY KEY (`id`)
);
```

### (4) 组合索引

​ 指多个字段上创建的索引，只有在查询条件中使用了创建索引时的第一个字段，索引才会被使用。使用组合索引时遵循最左前缀集合

```sql
ALTER TABLE `table` ADD INDEX name_city_age (name,city,age);
```

### (5) 全文索引

​ 主要用来查找文本中的关键字，而不是直接与索引中的值相比较。fulltext 索引跟其它索引大不相同，它更像是一个搜索引擎，而不是简单的 where 语句的参数匹配。fulltext 索引配合 match against 操作使用，而不是一般的 where 语句加 like。它可以在 create table，alter table ，create index 使用，不过目前只有 char、varchar，text 列上可以创建全文索引。值得一提的是，在数据量较大时候，现将数据放入一个没有全局索引的表中，然后再用 CREATE index 创建 fulltext 索引，要比先为一张表建立 fulltext 然后再将数据写入的速度快很多。

```MySQL
# 建立全文索引的表的存储引擎类型必须为MyISAM：ENGINE=MyISAM  DEFAULT >
# 在SELECT的WHERE字句中用MATCH函数,索引的关键词用AGAINST标识,IN BOOLEAN MODE是只有含有关键字就行,不用在乎位置,是不是起启位置.
SELECT * FROM articles WHERE MATCH (tags) AGAINST ('哈哈' IN BOOLEAN MODE);

SELECT * FROM articles WHERE MATCH (title,body)     AGAINST ('+apple -banana' IN BOOLEAN MODE); #  + 表示AND，即必须包含。- 表示NOT，即不包含。

SELECT * FROM articles WHERE MATCH (title,body)     AGAINST ('apple banana' IN BOOLEAN MODE); #  apple和banana之间是空格，空格表示OR，即至少包含apple、banana中的一个。

SELECT * FROM articles WHERE MATCH (title,body)     AGAINST ('+apple banana' IN BOOLEAN MODE); #  必须包含apple，但是如果同时也包含banana则会获得更高的权重。

SELECT * FROM articles WHERE MATCH (title,body)     AGAINST ('+apple ~banana' IN BOOLEAN MODE); #  ~ 是我们熟悉的异或运算符。返回的记录必须包含apple，但是如果同时也包含banana会降低权重。但是它没有 +apple -banana 严格，因为后者如果包含banana压根就不返回。

SELECT * FROM articles WHERE MATCH (title,body)     AGAINST ('+apple +(>banana <orange)' IN BOOLEAN MODE);   # 返回同时包含apple和banana或者同时包含apple和orange的记录。但是同时包含apple和banana的记录的权重高于同时包含apple和orange的记录。
```

（1）创建表的适合添加全文索引

```sql
CREATE TABLE `table` (
    `id` int(11) NOT NULL AUTO_INCREMENT ,
    `title` char(255) CHARACTER NOT NULL ,
    `content` text CHARACTER NULL ,
    `time` int(10) NULL DEFAULT NULL ,
    PRIMARY KEY (`id`),
    FULLTEXT (content)
);
```

（2）修改表结构添加全文索引

```sql
ALTER TABLE article ADD FULLTEXT index_content(content);
```

（3）直接创建索引

```sql
CREATE FULLTEXT INDEX index_content ON article(content);
```

## 4. 注意事项

使用索引时，有以下一些技巧和注意事项： 1.索引不会包含有 null 值的列
只要列中包含有 null 值都将不会被包含在索引中，复合索引中只要有一列含有 null 值，那么这一列对于此复合索引就是无效的。所以我们在数据库设计时不要让字段的默认值为 null。 2.使用短索引
对串列进行索引，如果可能应该指定一个前缀长度。例如，如果有一个 char(255)的列，如果在前 10 个或 20 个字符内，多数值是惟一的，那么就不要对整个列进行索引。短索引不仅可以提高查询速度而且可以节省磁盘空间和 I/O 操作。 3.索引列排序
查询只使用一个索引，因此如果 where 子句中已经使用了索引的话，那么 order by 中的列是不会使用索引的。因此数据库默认排序可以符合要求的情况下不要使用排序操作；尽量不要包含多个列的排序，如果需要最好给这些列创建复合索引。
4.like 语句操作
一般情况下不推荐使用 like 操作，如果非使用不可，如何使用也是一个问题。like “%aaa%” 不会使用索引而 like “aaa%”可以使用索引。 5.不要在列上进行运算
这将导致索引失效而进行全表扫描，例如

```sql
SELECT * FROM table_name WHERE YEAR(column_name)<2017;
```

6.不使用 not in 和<>操作

## 5. 创建索引

**1) 使用 CREATE INDEX 语句**

```sql
CREATE <索引名> ON <表名> (<列名> [<长度>] [ ASC | DESC]);
```

> 语法说明如下：
>
> - `<索引名>`：指定索引名。一个表可以创建多个索引，但每个索引在该表中的名称是唯一的。
> - `<表名>`：指定要创建索引的表名。
> - `<列名>`：指定要创建索引的列名。通常可以考虑将查询语句中在 JOIN 子句和 WHERE 子句里经常出现的列作为索引列。
> - `<长度>`：可选项。指定使用列前的 length 个字符来创建索引。使用列的一部分创建索引有利于减小索引文件的大小，节省索引列所占的空间。在某些情况下，只能对列的前缀进行索引。索引列的长度有一个最大上限 255 个字节（MyISAM 和 InnoDB 表的最大上限为 1000 个字节），如果索引列的长度超过了这个上限，就只能用列的前缀进行索引。另外，BLOB 或 TEXT 类型的列也必须使用前缀索引。
> - `ASC|DESC`：可选项。`ASC`指定索引按照升序来排列，`DESC`指定索引按照降序来排列，默认为`ASC`。

**2) 使用 CREATE TABLE 语句**

索引也可以在创建表（CREATE TABLE）的同时创建。在 CREATE TABLE 语句中添加以下语句。语法格式：

```
CONSTRAINT PRIMARY KEY [索引类型] (<列名>,…)
```

在 CREATE TABLE 语句中添加此语句，表示在创建新表的同时创建该表的主键。语法格式：

```
KEY | INDEX [<索引名>] [<索引类型>] (<列名>,…)
```

在 CREATE TABLE 语句中添加此语句，表示在创建新表的同时创建该表的索引。

```
UNIQUE [ INDEX | KEY] [<索引名>] [<索引类型>] (<列名>,…)
```

在 CREATE TABLE 语句中添加此语句，表示在创建新表的同时创建该表的唯一性索引。
语法格式：

```
FOREIGN KEY <索引名> <列名>
```

在 CREATE TABLE 语句中添加此语句，表示在创建新表的同时创建该表的外键。

**3) 使用 ALTER TABLE 语句**

```sql
ALTER TABLE table-name
	ADD INDEX [<索引名>] [<索引类型>] (<列名>,…);
	ADD PRIMARY KEY [<索引类型>] (<列名>,…);
	ADD UNIQUE [ INDEX | KEY] [<索引名>] [<索引类型>] (<列名>,…);
	ADD FOREIGN KEY [<索引名>] (<列名>,…);
```

## 6. 查看索引

```sql
SHOW INDEX FROM <表名> [ FROM <数据库名>]
```

> 语法说明如下：
> <表名>：指定需要查看索引的数据表名。<数据库名>：指定需要查看索引的数据表所在的数据库，可省略。比如，SHOW INDEX FROM student FROM test; 语句表示查看 test 数据库中 student 数据表的索引。

结果，其中各主要参数说明如下：

| 参数         | 说明                                                                                                                                                             |
| ------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Table        | 表示创建索引的数据表名，这里是 tb_stu_info2 数据表。                                                                                                             |
| Non_unique   | 表示该索引是否是唯一索引。若不是唯一索引，则该列的值为 1；若是唯一索引，则该列的值为 0。                                                                         |
| Key_name     | 表示索引的名称。                                                                                                                                                 |
| Seq_in_index | 表示该列在索引中的位置，如果索引是单列的，则该列的值为 1；如果索引是组合索引，则该列的值为每列在索引定义中的顺序。                                               |
| Column_name  | 表示定义索引的列字段。                                                                                                                                           |
| Collation    | 表示列以何种顺序存储在索引中。在 MySQL 中，升序显示值“A”（升序），若显示为 NULL，则表示无分类。                                                                  |
| Cardinality  | 索引中唯一值数目的估计值。基数根据被存储为整数的统计数据计数，所以即使对于小型表，该值也没有必要是精确的。基数越大，当进行联合时，MySQL 使用该索引的机会就越大。 |
| Sub_part     | 表示列中被编入索引的字符的数量。若列只是部分被编入索引，则该列的值为被编入索引的字符的数目；若整列被编入索引，则该列的值为 NULL。                                |
| Packed       | 指示关键字如何被压缩。若没有被压缩，值为 NULL。                                                                                                                  |
| Null         | 用于显示索引列中是否包含 NULL。若列含有 NULL，该列的值为 YES。若没有，则该列的值为 NO。                                                                          |
| Index_type   | 显示索引使用的类型和方法（BTREE、FULLTEXT、HASH、RTREE）。                                                                                                       |
| Comment      | 显示评注。                                                                                                                                                       |

## 7. 删除索引

​ 当不再需要索引时，可以使用 DROP INDEX 语句或 ALTER TABLE 语句来对索引进行删除。

```sql
# 1 使用 DROP INDEX 语句
DROP INDEX <索引名> ON <表名>
# 2 使用 ALTER TABLE 语句
alter table 表名
	DROP PRIMARY KEY：表示删除表中的主键。一个表只有一个主键，主键也是一个索引。
	DROP INDEX index_name：表示删除名称为 index_name 的索引。
	DROP FOREIGN KEY fk_symbol：表示删除外键。
```

## 8. 索引什么时候不会被使用

**1). 查询语句中使用 LIKE 关键字**

​ 在查询语句中使用 LIKE 关键字进行查询时，如果匹配字符串的第一个字符为“%”，索引不会被使用

**2). 查询语句中使用多列索引**

​ 多列索引是在表的多个字段上创建一个索引，只有查询条件中使用了这些字段中的第一个字段，索引才会被使用

**3). 查询语句中使用 OR 关键字**

​ 查询语句只有 OR 关键字时，如果 OR 前后的两个条件的列都是索引，那么查询中将使用索引。如果 OR 前后有一个条件的列不是索引，那么查询中将不使用索引。

## 9. 怎么提升索引的使用效率，设计出更高效的索引

1. 选择唯一性索引
   唯一性索引的值是唯一的，可以更快速的通过该索引来确定某条记录。例如，学生表中学号是具有唯一性的字段。为该字段建立唯一性索引可以很快的确定某个学生的信息。如果使用姓名的话，可能存在同名现象，从而降低查询速度。

2. 为经常需要排序、分组和联合操作的字段建立索引
   经常需要 ORDER BY、GROUP BY、DISTINCT 和 UNION 等操作的字段，排序操作会浪费很多时间。如果为其建立索引，可以有效地避免排序操作。

3. 为常作为查询条件的字段建立索引
   如果某个字段经常用来做查询条件，那么该字段的查询速度会影响整个表的查询速度。因此，为这样的字段建立索引，可以提高整个表的查询速度。

> 注意：常查询条件的字段不一定是所要选择的列，换句话说，最适合索引的列是出现在 WHERE 子句中的列，或连接子句中指定的列，而不是出现在 SELECT 关键字后的选择列表中的列。

4. 限制索引的数目
   索引的数目不是“越多越好”。每个索引都需要占用磁盘空间，索引越多，需要的磁盘空间就越大。在修改表的内容时，索引必须进行更新，有时还可能需要重构。因此，索引越多，更新表的时间就越长。

> 如果有一个索引很少利用或从不使用，那么会不必要地减缓表的修改速度。此外，MySQL 在生成一个执行计划时，要考虑各个索引，这也要花费时间。创建多余的索引给查询优化带来了更多的工作。索引太多，也可能会使 MySQL 选择不到所要使用的最佳索引。

5. 尽量使用数据量少的索引
   如果索引的值很长，那么查询的速度会受到影响。例如，对一个 CHAR(100) 类型的字段进行全文检索需要的时间肯定要比对 CHAR(10) 类型的字段需要的时间要多。

6. 数据量小的表最好不要使用索引
   由于数据较小，查询花费的时间可能比遍历索引的时间还要短，索引可能不会产生优化效果。

7. 尽量使用前缀来索引
   如果索引字段的值很长，最好使用值的前缀来索引。例如，TEXT 和 BLOG 类型的字段，进行全文检索会很浪费时间。如果只检索字段的前面的若干个字符，这样可以提高检索速度。

8. 删除不再使用或者很少使用的索引
   表中的数据被大量更新，或者数据的使用方式被改变后，原有的一些索引可能不再需要。应该定期找出这些索引，将它们删除，从而减少索引对更新操作的影响。

