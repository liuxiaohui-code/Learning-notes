# install

## Linux

```sh
Ubuntu 安装 PostgreSQL
Ubuntu 可以使用 apt-get 安装 PostgreSQL：

sudo apt-get update
sudo apt-get install postgresql postgresql-client
安装完毕后，系统会创建一个数据库超级用户 postgres，密码为空。
#  sudo -i -u postgres

这时使用以下命令进入 postgres，输出以下信息，说明安装成功：

~$ psql
psql (9.5.17)
Type "help" for help.

postgres=#

输入以下命令退出 PostgreSQL 提示符：

postgres=# \q

PostgreSQL 安装完成后默认是已经启动的，但是也可以通过下面的方式来手动启动服务。

sudo /etc/init.d/postgresql start   # 开启
sudo /etc/init.d/postgresql stop    # 关闭
sudo /etc/init.d/postgresql restart # 重启
```

## windows

下载地址： https://www.enterprisedb.com/downloads/postgres-postgresql-downloads

命令行工具路径： `Program Files → PostgreSQL 11.3 → SQL Shell(psql)`

# 命令

## 1.帮助

```
\help [command]
```

# 数据类型

## 1.数值类型

| 名字             | 存储长度 | 描述                 | 范围                                         |
| :--------------- | :------- | :------------------- | :------------------------------------------- |
| smallint         | 2 字节   | 小范围整数           | -32768 到 +32767                             |
| integer          | 4 字节   | 常用的整数           | -2147483648 到 +2147483647                   |
| bigint           | 8 字节   | 大范围整数           | -9223372036854775808 到 +9223372036854775807 |
| decimal          | 可变长   | 用户指定的精度，精确 | 小数点前 131072 位；小数点后 16383 位        |
| numeric          | 可变长   | 用户指定的精度，精确 | 小数点前 131072 位；小数点后 16383 位        |
| real             | 4 字节   | 可变精度，不精确     | 6 位十进制数字精度                           |
| double precision | 8 字节   | 可变精度，不精确     | 15 位十进制数字精度                          |
| smallserial      | 2 字节   | 自增的小范围整数     | 1 到 32767                                   |
| serial           | 4 字节   | 自增整数             | 1 到 2147483647                              |
| bigserial        | 8 字节   | 自增的大范围整数     | 1 到 9223372036854775807                     |

## 2.货币类型

money 类型存储带有固定小数精度的货币金额。

numeric、int 和 bigint 类型的值可以转换为 money，不建议使用浮点数来处理处理货币类型，因为存在舍入错误的可能性。

| 名字  | 存储容量 | 描述     | 范围                                           |
| :---- | :------- | :------- | :--------------------------------------------- |
| money | 8 字节   | 货币金额 | -92233720368547758.08 到 +92233720368547758.07 |

## 3.字符类型

下表列出了 PostgreSQL 所支持的字符类型：

| 序号 |                     名字 & 描述                      |
| :--- | :--------------------------------------------------: |
| 1    | **character varying(n), varchar(n)**变长，有长度限制 |
| 2    |      **character(n), char(n)**f 定长,不足补空白      |
| 3    |               **text**变长，无长度限制               |

---

## 4 日期/时间类型

下表列出了 PostgreSQL 支持的日期和时间类型。

| 名字                                      | 存储空间 | 描述                     | 最低值        | 最高值        | 分辨率         |
| :---------------------------------------- | :------- | :----------------------- | :------------ | :------------ | :------------- |
| timestamp [ (*p*) ] [ without time zone ] | 8 字节   | 日期和时间(无时区)       | 4713 BC       | 294276 AD     | 1 毫秒 / 14 位 |
| timestamp [ (*p*) ] with time zone        | 8 字节   | 日期和时间，有时区       | 4713 BC       | 294276 AD     | 1 毫秒 / 14 位 |
| date                                      | 4 字节   | 只用于日期               | 4713 BC       | 5874897 AD    | 1 天           |
| time [ (*p*) ] [ without time zone ]      | 8 字节   | 只用于一日内时间         | 00:00:00      | 24:00:00      | 1 毫秒 / 14 位 |
| time [ (*p*) ] with time zone             | 12 字节  | 只用于一日内时间，带时区 | 00:00:00+1459 | 24:00:00-1459 | 1 毫秒 / 14 位 |
| interval [ *fields* ] [ (*p*) ]           | 12 字节  | 时间间隔                 | -178000000 年 | 178000000 年  | 1 毫秒 / 14 位 |

---

## 5 布尔类型

PostgreSQL 支持标准的 boolean 数据类型。

boolean 有"true"(真)或"false"(假)两个状态， 第三种"unknown"(未知)状态，用 NULL 表示。

| 名称    | 存储格式 | 描述       |
| :------ | :------- | :--------- |
| boolean | 1 字节   | true/false |

---

## 6 枚举类型

枚举类型是一个包含静态和值的有序集合的数据类型。

PostgtesSQL 中的枚举类型类似于 C 语言中的 enum 类型。

与其他类型不同的是枚举类型需要使用 CREATE TYPE 命令创建。

`create type enum_name as enum ('a','b','c',...)`

```
CREATE TYPE mood AS ENUM ('sad', 'ok', 'happy');
```

创建一周中的几天，如下所示:

```
CREATE TYPE week AS ENUM ('Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun');
```

就像其他类型一样，一旦创建，枚举类型可以用于表和函数定义。

```
CREATE TYPE mood AS ENUM ('sad', 'ok', 'happy');
CREATE TABLE person (
    name text,
    current_mood mood
);
INSERT INTO person VALUES ('Moe', 'happy');
SELECT * FROM person WHERE current_mood = 'happy';
 name | current_mood
------+--------------
 Moe  | happy
(1 row)
```

## 几何类型

几何数据类型表示二维的平面物体。

下表列出了 PostgreSQL 支持的几何类型。

最基本的类型：点。它是其它类型的基础。

| 名字    | 存储空间    | 说明                   | 表现形式               |
| :------ | :---------- | :--------------------- | :--------------------- |
| point   | 16 字节     | 平面中的点             | (x,y)                  |
| line    | 32 字节     | (无穷)直线(未完全实现) | ((x1,y1),(x2,y2))      |
| lseg    | 32 字节     | (有限)线段             | ((x1,y1),(x2,y2))      |
| box     | 32 字节     | 矩形                   | ((x1,y1),(x2,y2))      |
| path    | 16+16n 字节 | 闭合路径(与多边形类似) | ((x1,y1),...)          |
| path    | 16+16n 字节 | 开放路径               | [(x1,y1),...]          |
| polygon | 40+16n 字节 | 多边形(与闭合路径相似) | ((x1,y1),...)          |
| circle  | 24 字节     | 圆                     | <(x,y),r> (圆心和半径) |

---

## 网络地址类型

PostgreSQL 提供用于存储 IPv4 、IPv6 、MAC 地址的数据类型。

用这些数据类型存储网络地址比用纯文本类型好， 因为这些类型提供输入错误检查和特殊的操作和功能。

| 名字    | 存储空间     | 描述                    |
| :------ | :----------- | :---------------------- |
| cidr    | 7 或 19 字节 | IPv4 或 IPv6 网络       |
| inet    | 7 或 19 字节 | IPv4 或 IPv6 主机和网络 |
| macaddr | 6 字节       | MAC 地址                |

在对 inet 或 cidr 数据类型进行排序的时候， IPv4 地址总是排在 IPv6 地址前面，包括那些封装或者是映射在 IPv6 地址里的 IPv4 地址， 比如 ::10.2.3.4 或 ::ffff:10.4.3.2。

---

## 位串类型

位串就是一串 1 和 0 的字符串。它们可以用于存储和直观化位掩码。 我们有两种 SQL 位类型：bit(n) 和 bit varying(n)， 这里的 n 是一个正整数。

bit 类型的数据必须准确匹配长度 n， 试图存储短些或者长一些的数据都是错误的。bit varying 类型数据是最长 n 的变长类型；更长的串会被拒绝。 写一个没有长度的 bit 等效于 bit(1)， 没有长度的 bit varying 意思是没有长度限制。

---

## 文本搜索类型

全文检索即通过自然语言文档的集合来找到那些匹配一个查询的检索。

PostgreSQL 提供了两种数据类型用于支持全文检索：

| 序号 |                                                       名字 & 描述                                                       |
| :--- | :---------------------------------------------------------------------------------------------------------------------: |
| 1    |             **tsvector**tsvector 的值是一个无重复值的 lexemes 排序列表， 即一些同一个词的不同变种的标准化。             |
| 2    | **tsquery**tsquery 存储用于检索的词汇，并且使用布尔操作符 &(AND)，\|(OR)和!(NOT) 来组合它们，括号用来强调操作符的分组。 |

---

## UUID 类型

uuid 数据类型用来存储 RFC 4122，ISO/IEF 9834-8:2005 以及相关标准定义的通用唯一标识符（UUID）。 （一些系统认为这个数据类型为全球唯一标识符，或 GUID。） 这个标识符是一个由算法产生的 128 位标识符，使它不可能在已知使用相同算法的模块中和其他方式产生的标识符相同。 因此，对分布式系统而言，这种标识符比序列能更好的提供唯一性保证，因为序列只能在单一数据库中保证唯一。

UUID 被写成一个小写十六进制数字的序列，由分字符分成几组， 特别是一组 8 位数字+3 组 4 位数字+一组 12 位数字，总共 32 个数字代表 128 位， 一个这种标准的 UUID 例子如下：

```
a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11
```

---

## XML 类型

xml 数据类型可以用于存储 XML 数据。 将 XML 数据存到 text 类型中的优势在于它能够为结构良好性来检查输入值， 并且还支持函数对其进行类型安全性检查。 要使用这个数据类型，编译时必须使用 **configure --with-libxml**。

xml 可以存储由 XML 标准定义的格式良好的"文档"， 以及由 XML 标准中的 **XMLDecl? content** 定义的"内容"片段， 大致上，这意味着内容片段可以有多个顶级元素或字符节点。 xmlvalue IS DOCUMENT 表达式可以用来判断一个特定的 xml 值是一个完整的文件还是内容片段。

### 创建 XML 值

使用函数 xmlparse: 来从字符数据产生 xml 类型的值：

```
XMLPARSE (DOCUMENT '<?xml version="1.0"?><book><title>Manual</title><chapter>...</chapter></book>')
XMLPARSE (CONTENT 'abc<foo>bar</foo><bar>foo</bar>')
```

---

## JSON 类型

json 数据类型可以用来存储 JSON（JavaScript Object Notation）数据， 这样的数据也可以存储为 text，但是 json 数据类型更有利于检查每个存储的数值是可用的 JSON 值。

此外还有相关的函数来处理 json 数据：

|                   实例                   |      实例结果       |
| :--------------------------------------: | :-----------------: |
| array_to_json('{{1,5},{99,100}}'::int[]) |  [[1,5],[99,100]]   |
|        row_to_json(row(1,'foo'))         | {"f1":1,"f2":"foo"} |

---

## 数组类型

PostgreSQL 允许将字段定义成变长的多维数组。

数组类型可以是任何基本类型或用户定义类型，枚举类型或复合类型。

### 声明数组

创建表的时候，我们可以声明数组，方式如下：

```
CREATE TABLE sal_emp (
    name            text,
    pay_by_quarter  integer[],
    schedule        text[][]
);
```

pay_by_quarter 为一维整型数组、schedule 为二维文本类型数组。

我们也可以使用 "ARRAY" 关键字，如下所示：

```
CREATE TABLE sal_emp (
   name text,
   pay_by_quarter integer ARRAY[4],
   schedule text[][]
);
```

### 插入值

插入值使用花括号 {}，元素在 {} 使用逗号隔开：

```sql
INSERT INTO sal_emp
    VALUES ('Bill','{10000, 10000, 10000, 10000}','{{"meeting", "lunch"},{"training", "presentation"}}');

INSERT INTO sal_emp
    VALUES ('Carol',
    '{20000, 25000, 25000, 25000}',
    '{{"breakfast", "consulting"}, {"meeting", "lunch"}}');
```

### 访问数组

现在我们可以在这个表上运行一些查询。

首先，我们演示如何访问数组的一个元素。 这个查询检索在第二季度薪水变化的雇员名：

```
SELECT name FROM sal_emp WHERE pay_by_quarter[1] <> pay_by_quarter[2];

 name
-------
 Carol
(1 row)
```

数组的下标数字是写在方括弧内的。

### 修改数组

我们可以对数组的值进行修改：

```
UPDATE sal_emp SET pay_by_quarter = '{25000,25000,27000,27000}'
    WHERE name = 'Carol';
```

或者使用 ARRAY 构造器语法：

```
UPDATE sal_emp SET pay_by_quarter = ARRAY[25000,25000,27000,27000]
    WHERE name = 'Carol';
```

### 数组中检索

要搜索一个数组中的数值，你必须检查该数组的每一个值。

比如：

```
SELECT * FROM sal_emp WHERE pay_by_quarter[1] = 10000 OR
                            pay_by_quarter[2] = 10000 OR
                            pay_by_quarter[3] = 10000 OR
                            pay_by_quarter[4] = 10000;
```

另外，你可以用下面的语句找出数组中所有元素值都等于 10000 的行：

```
SELECT * FROM sal_emp WHERE 10000 = ALL (pay_by_quarter);
```

或者，可以使用 generate_subscripts 函数。例如：

```
SELECT * FROM
   (SELECT pay_by_quarter,
           generate_subscripts(pay_by_quarter, 1) AS s
      FROM sal_emp) AS foo
 WHERE pay_by_quarter[s] = 10000;
```

---

## 复合类型

复合类型表示一行或者一条记录的结构； 它实际上只是一个字段名和它们的数据类型的列表。PostgreSQL 允许像简单数据类型那样使用复合类型。比如，一个表的某个字段可以声明为一个复合类型。

### 声明复合类型

下面是两个定义复合类型的简单例子：

```
CREATE TYPE complex AS (
    r       double precision,
    i       double precision
);

CREATE TYPE inventory_item AS (
    name            text,
    supplier_id     integer,
    price           numeric
);
```

语法类似于 CREATE TABLE，只是这里只可以声明字段名字和类型。

定义了类型，我们就可以用它创建表：

```
CREATE TABLE on_hand (
    item      inventory_item,
    count     integer
);

INSERT INTO on_hand VALUES (ROW('fuzzy dice', 42, 1.99), 1000);
```

### 复合类型值输入

要以文本常量书写复合类型值，在圆括弧里包围字段值并且用逗号分隔他们。 你可以在任何字段值周围放上双引号，如果值本身包含逗号或者圆括弧， 你必须用双引号括起。

复合类型常量的一般格式如下：

```
'( val1 , val2 , ... )'
```

一个例子是:

```
'("fuzzy dice",42,1.99)'
```

### 访问复合类型

要访问复合类型字段的一个域，我们写出一个点以及域的名字， 非常类似从一个表名字里选出一个字段。实际上，因为实在太像从表名字中选取字段， 所以我们经常需要用圆括弧来避免分析器混淆。比如，你可能需要从 on_hand 例子表中选取一些子域，像下面这样：

```
SELECT item.name FROM on_hand WHERE item.price > 9.99;
```

这样将不能工作，因为根据 SQL 语法，item 是从一个表名字选取的， 而不是一个字段名字。你必须像下面这样写：

```
SELECT (item).name FROM on_hand WHERE (item).price > 9.99;
```

或者如果你也需要使用表名字(比如，在一个多表查询里)，那么这么写：

```
SELECT (on_hand.item).name FROM on_hand WHERE (on_hand.item).price > 9.99;
```

现在圆括弧对象正确地解析为一个指向 item 字段的引用，然后就可以从中选取子域。

---

## 范围类型

范围数据类型代表着某一元素类型在一定范围内的值。

例如，timestamp 范围可能被用于代表一间会议室被预定的时间范围。

PostgreSQL 内置的范围类型有：

- int4range — integer 的范围
- int8range —bigint 的范围
- numrange —numeric 的范围
- tsrange —timestamp without time zone 的范围
- tstzrange —timestamp with time zone 的范围
- daterange —date 的范围

此外，你可以定义你自己的范围类型。

```
CREATE TABLE reservation (room int, during tsrange);
INSERT INTO reservation VALUES
    (1108, '[2010-01-01 14:30, 2010-01-01 15:30)');

-- 包含
SELECT int4range(10, 20) @> 3;

-- 重叠
SELECT numrange(11.1, 22.2) && numrange(20.0, 30.0);

-- 提取上边界
SELECT upper(int8range(15, 25));

-- 计算交叉
SELECT int4range(10, 20) * int4range(15, 25);

-- 范围是否为空
SELECT isempty(numrange(1, 5));
```

范围值的输入必须遵循下面的格式：

```
(下边界,上边界)
(下边界,上边界]
[下边界,上边界)
[下边界,上边界]
空
```

圆括号或者方括号显示下边界和上边界是不包含的还是包含的。注意最后的格式是 空，代表着一个空的范围（一个不含有值的范围）。

```
-- 包括3，不包括7，并且包括二者之间的所有点
SELECT '[3,7)'::int4range;

-- 不包括3和7，但是包括二者之间所有点
SELECT '(3,7)'::int4range;

-- 只包括单一值4
SELECT '[4,4]'::int4range;

-- 不包括点（被标准化为‘空’）
SELECT '[4,4)'::int4range;
```

---

## 对象标识符类型

PostgreSQL 在内部使用对象标识符(OID)作为各种系统表的主键。

同时，系统不会给用户创建的表增加一个 OID 系统字段(除非在建表时声明了 WITH OIDS 或者配置参数 default_with_oids 设置为开启)。oid 类型代表一个对象标识符。除此以外 oid 还有几个别名：regproc, regprocedure, regoper, regoperator, regclass, regtype, regconfig, 和 regdictionary。

| 名字          | 引用         | 描述               | 数值例子                               |
| :------------ | :----------- | :----------------- | :------------------------------------- |
| oid           | 任意         | 数字化的对象标识符 | 564182                                 |
| regproc       | pg_proc      | 函数名字           | sum                                    |
| regprocedure  | pg_proc      | 带参数类型的函数   | sum(int4)                              |
| regoper       | pg_operator  | 操作符名           | +                                      |
| regoperator   | pg_operator  | 带参数类型的操作符 | \*(integer,integer) 或 -(NONE,integer) |
| regclass      | pg_class     | 关系名             | pg_type                                |
| regtype       | pg_type      | 数据类型名         | integer                                |
| regconfig     | pg_ts_config | 文本搜索配置       | english                                |
| regdictionary | pg_ts_dict   | 文本搜索字典       | simple                                 |

---

## 伪类型

PostgreSQL 类型系统包含一系列特殊用途的条目， 它们按照类别来说叫做伪类型。伪类型不能作为字段的数据类型， 但是它可以用于声明一个函数的参数或者结果类型。 伪类型在一个函数不只是简单地接受并返回某种 SQL 数据类型的情况下很有用。

下表列出了所有的伪类型：

| 名字             | 描述                                                |
| :--------------- | :-------------------------------------------------- |
| any              | 表示一个函数接受任何输入数据类型。                  |
| anyelement       | 表示一个函数接受任何数据类型。                      |
| anyarray         | 表示一个函数接受任意数组数据类型。                  |
| anynonarray      | 表示一个函数接受任意非数组数据类型。                |
| anyenum          | 表示一个函数接受任意枚举数据类型。                  |
| anyrange         | 表示一个函数接受任意范围数据类型。                  |
| cstring          | 表示一个函数接受或者返回一个空结尾的 C 字符串。     |
| internal         | 表示一个函数接受或者返回一种服务器内部的数据类型。  |
| language_handler | 一个过程语言调用处理器声明为返回 language_handler。 |
| fdw_handler      | 一个外部数据封装器声明为返回 fdw_handler。          |
| record           | 标识一个函数返回一个未声明的行类型。                |
| trigger          | 一个触发器函数声明为返回 trigger。                  |
| void             | 表示一个函数不返回数值。                            |
| opaque           | 一个已经过时的类型，以前用于所有上面这些用途。      |

# 数据库操作

## 1 创建数据库

### ceate database

在 postgres 命令行使用

```
create database <db-name>
```

### ceatedb

在命令行使用

```shell
createdb [option...] [dbname [description]]

$ createdb <db-name>
$ createdb # 创建一个与用户名同名的数据库

-D tablespace 指定数据库默认表空间。
-e						将 createdb 生成的命令发送到服务端。
-E encoding		指定数据库的编码。
-l locale			指定数据库的语言环境。
-T template		指定创建此数据库的模板。
--help				显示 createdb 命令的帮助信息。
-h host				指定服务器的主机名。
-p port				指定服务器监听的端口，或者 socket 文件。
-U username		连接数据库的用户名。
-w						忽略输入密码。
-W						连接时强制要求输入密码。
```

## 2 删除数据库

### drop database

DROP DATABASE 会删除数据库的系统目录项并且删除包含数据的文件目录。

DROP DATABASE 只能由超级管理员或数据库拥有者执行。

DROP DATABASE 命令需要在 PostgreSQL 命令窗口来执行，语法格式如下：

```
postgres=# DROP DATABASE [ IF EXISTS ] name

IF EXISTS：如果数据库不存在则发出提示信息，而不是错误信息。
name：要删除的数据库的名称。
```

### dropdb

dropdb 是 DROP DATABASE 的包装器。

dropdb 用于删除 PostgreSQL 数据库。

dropdb 命令只能由超级管理员或数据库拥有者执行。

dropdb 命令语法格式如下

```shell
dropdb [connection-option...] [option...] dbname

-e 		显示 dropdb 生成的命令并发送到数据库服务器。
-i		在做删除的工作之前发出一个验证提示。
-V		打印 dropdb 版本并退出。
--if-exists		如果数据库不存在则发出提示信息，而不是错误信息。
--help		显示有关 dropdb 命令的帮助信息。
-h host		指定运行服务器的主机名。
-p port		指定服务器监听的端口，或者 socket 文件。
-U username		连接数据库的用户名。
-w		连接时忽略输入密码。
-W		连接时强制要求输入密码。
--maintenance-db=dbname		删除数据库时指定连接的数据库，默认为 postgres，如果它不存在则使用 template1。
```

## 3 查看+进入

查看已经存在的数据库`\l`

进入数据库`\c <dbName>`

命令行进入`psql -h localhost -p 5432 -U postgres runoobdb`

# 表操作

## 1.创建

**CREATE TABLE** 是一个关键词，用于告诉数据库系统将创建一个数据表。

表名字必需在同一模式中的其它表、 序列、索引、视图或外部表名字中唯一。

**CREATE TABLE** 在当前数据库创建一个新的空白表，该表将由发出此命令的用户所拥有。

```
CREATE TABLE table_name(
   column1 datatype [约束],
   column2 datatype,
   column3 datatype,
   .....
   columnN datatype,
   PRIMARY KEY( 一个或多个列 )   # 唯一约束
);
```

### 自增

AUTO INCREMENT（自动增长） 会在新记录插入表中时生成一个唯一的数字。

PostgreSQL 使用序列来标识字段的自增长，数据类型有 smallserial、serial 和 bigserial 。这些属性类似于 MySQL 数据库支持的 AUTO_INCREMENT 属性。

```sql
CREATE TABLE tablename (
   colname SERIAL
);
```

| 伪类型        | 存储大小 | 范围                          |
| :------------ | :------- | :---------------------------- |
| `SMALLSERIAL` | 2 字节   | 1 到 32,767                   |
| `SERIAL`      | 4 字节   | 1 到 2,147,483,647            |
| `BIGSERIAL`   | 8 字节   | 1 到 922,337,2036,854,775,807 |

## 2.约束

### 约束种类

- `NOT NULL`：指示某列不能存储 NULL 值。
- `UNIQUE`：确保某列的值都是唯一的。
- `PRIMARY Key`：NOT NULL 和 UNIQUE 的结合。确保某列（或两个列多个列的结合）有唯一标识，有助于更容易更快速地找到表中的一个特定的记录。。
- `FOREIGN Key`： 保证一个表中的数据匹配另一个表中的值的参照完整性。通常一个表中的 FOREIGN KEY 指向另一个表中的 UNIQUE KEY(唯一约束的键)，即维护了两个相关表之间的引用完整性。
- `CHECK(condition)`： 保证列中的所有值满足某一条件，即对输入一条记录要进行检查。如果条件值为 false，则记录违反了约束，且不能输入到表。
- `EXCLUSION `：排他约束，保证如果将任何两行的指定列或表达式使用指定操作符进行比较，至少其中一个操作符比较将会返回 false 或空值。

```sql
 EXCLUDE USING gist  -- USING gist 是用于构建和执行的索引一种类型
   (NAME WITH =,  -- 如果满足 NAME 相同，AGE 不相同则不允许插入，否则允许插入
   AGE WITH <>)   -- 其比较的结果是如果整个表边式返回 true，则不允许插入，否则允许
```

### 删除约束

```sql
ALTER TABLE table_name DROP CONSTRAINT some_name;
```

## 3.查看所有表

查看所有表格 `\d`

查看表格详细信息 `\d <tableName>`

## 4.删除

PostgreSQL 使用 `DROP TABLE` 语句来删除表格，包含表格数据、规则、触发器等，所以删除表格要慎重，删除后所有信息就消失了。

**DROP TABLE** 语法格式如下：

```sql
DROP TABLE table_name,table_name2,...;
可以一次删除多个表
```

PostgreSQL 中 `TRUNCATE TABLE` 用于删除表的数据，但不删除表结构。

也可以用 DROP TABLE 删除表，但是这个命令会连表的结构一起删除，如果想插入数据，需要重新建立这张表。

TRUNCATE TABLE 与 DELETE 具有相同的效果，但是由于它实际上并不扫描表，所以速度更快。 此外，TRUNCATE TABLE 可以立即释放表空间，而不需要后续 VACUUM 操作，这在大型表上非常有用。

PostgreSQL VACUUM 操作用于释放、再利用更新/删除行所占据的磁盘空间。

```sql
TRUNCATE TABLE  table_name;
```

## 5.alter

在 PostgreSQL 中，**ALTER TABLE** 命令用于添加，修改，删除一张已经存在表的列。

另外你也可以用 **ALTER TABLE** 命令添加和删除约束。

用 ALTER TABLE 在一张已存在的表上**添加列**的语法如下：

```sql
ALTER TABLE table_name ADD column_name datatype;
```

在一张已存在的表上 DROP COLUMN（**删除列**），语法如下：

```
ALTER TABLE table_name DROP COLUMN column_name;
```

**修改表中某列的 DATA TYPE（数据类型）**，语法如下：

```sql
ALTER TABLE table_name ALTER COLUMN column_name TYPE datatype;
```

给表中某列**添加 NOT NULL 约束**，语法如下：

```sql
ALTER TABLE table_name ALTER column_name datatype NOT NULL;

alter table ${table_name} alter column ${column_name} set not null;
```

给表中某列 ADD UNIQUE CONSTRAINT（ **添加 UNIQUE 约束**），语法如下：

```sql
ALTER TABLE table_name
ADD CONSTRAINT MyUniqueConstraint UNIQUE(column1, column2...);
```

给表中 ADD CHECK CONSTRAINT（**添加 CHECK 约束**），语法如下：

```sql
ALTER TABLE table_name
ADD CONSTRAINT MyUniqueConstraint CHECK (CONDITION);
```

给表 ADD PRIMARY KEY（添加主键），语法如下：

```sql
ALTER TABLE table_name
ADD CONSTRAINT MyPrimaryKey PRIMARY KEY (column1, column2...);
```

DROP CONSTRAINT （删除约束），语法如下：

```sql
ALTER TABLE table_name
DROP CONSTRAINT MyUniqueConstraint;
```

DROP PRIMARY KEY （删除主键），语法如下：

```sql
ALTER TABLE table_name
DROP CONSTRAINT MyPrimaryKey
```

删除表中某列的 NOT NULL 约束，语法如下：

```sql
alter table ${table_name} alter column ${column_name} drop not null;
```

# 模式 schema

PostgreSQL 模式（SCHEMA）可以看着是一个表的集合。

一个模式可以包含视图、索引、数据类型、函数和操作符等。

相同的对象名称可以被用于不同的模式中而不会出现冲突，例如 schema1 和 myschema 都可以包含名为 mytable 的表。

使用模式的优势：

- 允许多个用户使用一个数据库并且不会互相干扰。
- 将数据库对象组织成逻辑组以便更容易管理。
- 第三方应用的对象可以放在独立的模式中，这样它们就不会与其他对象的名称发生冲突。

模式类似于操作系统层的目录，但是模式不能嵌套。

## 创建

```
# 创建一个模式
CREATE SCHEMA <schemaName>;
# 创建一个模式的表
create table <schemaName>.<tableName> ();
```

## 删除

```
# 删除一个为空的模式（其中的所有对象已经被删除）：
DROP SCHEMA <schemaName>;
# 删除一个模式以及其中包含的所有对象：
DROP SCHEMA <schemaName> CASCADE;
```

# 表中数据操作

## select

```sql
SELECT  [DISTINCT]  column1, column2, columnN  # 去重
FROM table_name
WHERE [CONDITION | EXPRESSION] # 条件|表达式  分组前查询
LIMIT [no_of_rows]  # 限制结果条数
LIMIT [no_of_rows] OFFSET [row_num] # 从第row_num条记录开始返回no_of_rows条记录
GROUP BY column1, column2....columnN # 分组
HAVING [ conditions ]  # 分组后做查询
[ORDER BY column1, column2, .. columnN] [ASC | DESC];#排序


-- GROUP BY 子句必须放在 WHERE 子句中的条件之后，必须放在 ORDER BY 子句之前。
```

```sql
NOT NULL;
LIKE '%k_';
IN ();
NOT IN ();
BETWEEN A AND B;
EXISTS
is null;
is not null;
```

### 别名 as

```sql
SELECT column_name AS alias_nameas
```

### with

在 PostgreSQL 中，WITH 子句提供了一种编写辅助语句的方法，以便在更大的查询中使用。

查询时作为临时表

```sql
WITH
   name_for_summary_data AS (
      SELECT Statement)
   SELECT columns
   FROM name_for_summary_data
   WHERE conditions <=> (
      SELECT column
      FROM name_for_summary_data)
   [ORDER BY columns]；
```

例

```sql
# 在 视图 CTE 中做查询操作
With CTE AS
(Select
 ID
, NAME
, AGE
, ADDRESS
, SALARY
FROM COMPANY )
Select * From CTE;
```

### 连接

- CROSS JOIN ：交叉连接
- INNER JOIN：内连接
- LEFT OUTER JOIN：左外连接
- RIGHT OUTER JOIN：右外连接
- FULL OUTER JOIN：全外连接

### union

PostgreSQL UNION 操作符合并两个或多个 SELECT 语句的结果。

UNION 操作符用于合并两个或多个 SELECT 语句的结果集。

请注意，UNION 内部的每个 SELECT 语句必须拥有相同数量的列。列也必须拥有相似的数据类型。同时，每个 SELECT 语句中的列的顺序必须相同。

```sql
SELECT column1 [, column2 ]
FROM table1 [, table2 ]
[WHERE condition]

UNION  # 去除重复行  不去重使用： union all

SELECT column1 [, column2 ]
FROM table1 [, table2 ]
[WHERE condition]；
```

**union all** 不去重

### 子查询

子查询或称为内部查询、嵌套查询，指的是在 PostgreSQL 查询中的 WHERE 子句中嵌入查询语句。

一个 SELECT 语句的查询结果能够作为另一个语句的输入值。

子查询可以与 SELECT、INSERT、UPDATE 和 DELETE 语句一起使用，并可使用运算符如 =、<、>、>=、<=、IN、BETWEEN 等。

以下是子查询必须遵循的几个规则：

- 子查询必须用括号括起来。
- 子查询在 SELECT 子句中只能有一个列，除非在主查询中有多列，与子查询的所选列进行比较。
- ORDER BY 不能用在子查询中，虽然主查询可以使用 ORDER BY。可以在子查询中使用 GROUP BY，功能与 ORDER BY 相同。
- 子查询返回多于一行，只能与多值运算符一起使用，如 IN 运算符。
- BETWEEN 运算符不能与子查询一起使用，但是，BETWEEN 可在子查询内使用。

```sql
SELECT column_name [, column_name ]
FROM   table1 [, table2 ]
WHERE  column_name OPERATOR
      (SELECT column_name [, column_name ]
      FROM table1 [, table2 ]
      [WHERE]);



INSERT INTO table_name [ (column1 [, column2 ]) ]
   SELECT [ *|column1 [, column2 ] ]
   FROM table1 [, table2 ]
   [ WHERE VALUE OPERATOR ];



UPDATE table
SET column_name = new_value
[ WHERE OPERATOR [ VALUE ]
   (SELECT COLUMN_NAME
   FROM TABLE_NAME)
   [ WHERE) ]


DELETE FROM TABLE_NAME
[ WHERE OPERATOR [ VALUE ]
   (SELECT COLUMN_NAME
   FROM TABLE_NAME)
   [ WHERE) ]
```

## insert

PostgreSQL `INSERT INTO` 语句用于向表中插入新记录，可以插入一行也可以同时插入多行。

```sql
INSERT INTO TABLE_NAME (column1, column2, column3,...columnN)
VALUES (value1, value2, value3,...valueN),(value1, value2, value3,...valueN),...;
```

- 字段列必须和数据值数量相同，且顺序也要对应;

- 向表中的所有字段插入值，则可以不需要指定字段，只需要指定插入的值即可;

结果返回：

​ 1.`INSERT oid 1`只插入一行并且目标表具有 OID 的返回信息， 那么 oid 是分配给被插入行的 OID。

​ 2.`INSERT 0 #`插入多行返回的信息， # 为插入的行数。

## update

```sql
UPDATE table_name
SET column1 = value1, column2 = value2...., columnN = valueN
WHERE [condition];
```

## delete

删除表数据`DELETE FROM`

```sql
DELETE FROM table_name WHERE [condition];
# 如果没有指定 WHERE 子句，PostgreSQL 表中的所有记录将被删除。
```

# 运算符

## 1.算数运算符

假设变量 a 为 2，变量 b 为 3，则：

| 运算符 |        描述        |        实例         |
| :----: | :----------------: | :-----------------: |
|   +    |         加         |   a + b 结果为 5    |
|   -    |         减         |   a - b 结果为 -1   |
|   \*   |         乘         |   a \* b 结果为 6   |
|   /    |         除         |   b / a 结果为 1    |
|   %    |     模（取余）     |   b % a 结果为 1    |
|   ^    |        指数        |   a ^ b 结果为 8    |
|  \|/   |       平方根       |  \|/ 25.0 结果为 5  |
| \|\|/  |       立方根       | \|\|/ 27.0 结果为 3 |
|   !    |        阶乘        |   5 ! 结果为 120    |
|   !!   | 阶乘（前缀操作符） |   !! 5 结果为 120   |

测试

```
select 1+2；

	-- 3
```

## 2.逻辑运算符

| 序号 |                                                                 运算符 & 描述                                                                  |
| :--- | :--------------------------------------------------------------------------------------------------------------------------------------------: |
| 1    |                **AND**逻辑与运算符。如果两个操作数都非零，则条件为真。PostgresSQL 中的 WHERE 语句可以用 AND 包含多个过滤条件。                 |
|      |                                                                                                                                                |
| 2    | **NOT**逻辑非运算符。用来逆转操作数的逻辑状态。如果条件为真则逻辑非运算符将使其为假。PostgresSQL 有 NOT EXISTS, NOT BETWEEN, NOT IN 等运算符。 |
| 3    |            **OR**逻辑或运算符。如果两个操作数中有任意一个非零，则条件为真。PostgresSQL 中的 WHERE 语句可以用 OR 包含多个过滤条件。             |

例

```
NOT NULL;
LIKE '%k_';
IN ();
NOT IN ();
BETWEEN A AND B;
EXISTS
```

## 3.位运算符

下表显示了 PostgreSQL 支持的位运算符。假设变量 **A** 的值为 60，变量 **B** 的值为 13，则：

| 运算符 | 描述                                                                                         | 实例                                                             |
| :----- | :------------------------------------------------------------------------------------------- | :--------------------------------------------------------------- | ------ | ------ | ----- | ---------------------------------- |
| &      | 按位与操作，按二进制位进行"与"运算。运算规则：`0&0=0; 0&1=0; 1&0=0; 1&1=1;`                  | (A & B) 将得到 12，即为 0000 1100                                |
| \|     | 按位或运算符，按二进制位进行"或"运算。运算规则：`0                                           | 0=0; 0                                                           | 1=1; 1 | 0=1; 1 | 1=1;` | (A \| B) 将得到 61，即为 0011 1101 |
| #      | 异或运算符，按二进制位进行"异或"运算。运算规则：`0#0=0; 0#1=1; 1#0=1; 1#1=0;`                | (A # B) 将得到 49，即为 0011 0001                                |
| ~      | 取反运算符，按二进制位进行"取反"运算。运算规则：`~1=0; ~0=1;`                                | (~A ) 将得到 -61，即为 1100 0011，一个有符号二进制数的补码形式。 |
| <<     | 二进制左移运算符。将一个运算对象的各二进制位全部左移若干位（左边的二进制位丢弃，右边补 0）。 | A << 2 将得到 240，即为 1111 0000                                |
| >>     | 二进制右移运算符。将一个数的各二进制位全部右移若干位，正数左补 0，负数左补 1，右边丢弃。     | A >> 2 将得到 15，即为 0000 1111                                 |

## 4.比较运算符

假设变量 a 为 10，变量 b 为 20，则：

| 运算符 |   描述   |        实例         |
| :----: | :------: | :-----------------: |
|   =    |   等于   | (a = b) 为 false。  |
|   !=   |  不等于  | (a != b) 为 true。  |
|   <>   |  不等于  | (a <> b) 为 true。  |
|   >    |   大于   | (a > b) 为 false。  |
|   <    |   小于   |  (a < b) 为 true。  |
|   >=   | 大于等于 | (a >= b) 为 false。 |
|   <=   | 小于等于 | (a <= b) 为 true。  |

# 触发器

PostgreSQL 触发器是数据库的回调函数，它会在指定的数据库事件发生时自动执行/调用。

下面是关于 PostgreSQL 触发器几个比较重要的点：

- PostgreSQL 触发器可以在下面几种情况下触发：
  - 在执行操作之前（在检查约束并尝试插入、更新或删除之前）。
  - 在执行操作之后（在检查约束并插入、更新或删除完成之后）。
  - 更新操作（在对一个视图进行插入、更新、删除时）。
- 触发器的 FOR EACH ROW 属性是可选的，如果选中，当操作修改时每行调用一次；相反，选中 FOR EACH STATEMENT，不管修改了多少行，每个语句标记的触发器执行一次。
- WHEN 子句和触发器操作在引用 NEW.column-name 和 OLD.column-name 表单插入、删除或更新时可以访问每一行元素。其中 column-name 是与触发器关联的表中的列的名称。
- 如果存在 WHEN 子句，PostgreSQL 语句只会执行 WHEN 子句成立的那一行，如果没有 WHEN 子句，PostgreSQL 语句会在每一行执行。
- BEFORE 或 AFTER 关键字决定何时执行触发器动作，决定是在关联行的插入、修改或删除之前或者之后执行触发器动作。
- 要修改的表必须存在于同一数据库中，作为触发器被附加的表或视图，且必须只使用 tablename，而不是 database.tablename。
- 当创建约束触发器时会指定约束选项。这与常规触发器相同，只是可以使用这种约束来调整触发器触发的时间。当约束触发器实现的约束被违反时，它将抛出异常。

## 创建

```sql
CREATE  TRIGGER trigger_name [BEFORE|AFTER|INSTEAD OF] <[insert]| [update]|[delete]> [OF column_name [,...]]
ON table_name  [FOR EACH ROW]
[
 -- 触发器逻辑....
];


# event_name 可以是在所提到的表 table_name 上的 INSERT、DELETE 和 UPDATE 数据库操作。您可以在表名后选择指定 FOR EACH ROW。
```

## 列出触发器

```sql
SELECT * FROM pg_trigger;
# 列出特定表的触发器
SELECT tgname FROM pg_trigger, pg_class WHERE tgrelid=pg_class.oid AND relname='company';
```

## 删除触发器

```sql
drop trigger ${trigger_name} on ${table_of_trigger_dependent};
```

# 索引

## 1.创建

使用 CREATE INDEX 语句创建索引，它允许命名索引，指定表及要索引的一列或多列，并指示索引是升序排列还是降序排列。

索引也可以是唯一的，与 UNIQUE 约束类似，在列上或列组合上防止重复条目。

```sql
CREATE INDEX index_name ON table_name;
```

## 2.索引类型

### 单列索引

单列索引是一个只基于表的一个列上创建的索引，基本语法如下：

```sql
CREATE INDEX index_name
ON table_name (column_name);
```

### 组合索引

组合索引是基于表的多列上创建的索引，基本语法如下：

```sql
CREATE INDEX index_name
ON table_name (column1_name, column2_name);
```

不管是单列索引还是组合索引，该索引必须是在 WHERE 子句的过滤条件中使用非常频繁的列。

如果只有一列被使用到，就选择单列索引，如果有多列就使用组合索引。

### 唯一索引

使用唯一索引不仅是为了性能，同时也为了数据的完整性。唯一索引不允许任何重复的值插入到表中。基本语法如下：

```sql
CREATE UNIQUE INDEX index_name
on table_name (column_name);
```

### 局部索引

局部索引 是在表的子集上构建的索引；子集由一个条件表达式上定义。索引只包含满足条件的行。基础语法如下：

```sql
CREATE INDEX index_name
on table_name (conditional_expression);
```

### 隐式索引

隐式索引 是在创建对象时，由数据库服务器自动创建的索引。索引自动创建为主键约束和唯一约束。

## 3.查看索引

`\di` 命令列出数据库中所有索引

## 4.删除索引

```sql
DROP INDEX index_name;
```

## 5.索引使用场景

什么情况下要避免使用索引？

虽然索引的目的在于提高数据库的性能，但这里有几个情况需要避免使用索引。

使用索引时，需要考虑下列准则：

- 索引不应该使用在较小的表上。
- 索引不应该使用在有频繁的大批量的更新或插入操作的表上。
- 索引不应该使用在含有大量的 NULL 值的列上。
- 索引不应该使用在频繁操作的列上。

# 视图

View（视图）是一张假表，只不过是通过相关的名称存储在数据库中的一个 PostgreSQL 语句。

View（视图）实际上是一个以预定义的 PostgreSQL 查询形式存在的表的组合。

View（视图）可以包含一个表的所有行或从一个或多个表选定行。

View（视图）可以从一个或多个表创建，这取决于要创建视图的 PostgreSQL 查询。

View（视图）是一种虚拟表，允许用户实现以下几点：

- 用户或用户组认为更自然或直观查找结构数据的方式。
- 限制数据访问，用户只能看到有限的数据，而不是完整的表。
- 汇总各种表中的数据，用于生成报告。

PostgreSQL 视图是只读的，因此可能无法在视图上执行 DELETE、INSERT 或 UPDATE 语句。但是可以在视图上创建一个触发器，当尝试 DELETE、INSERT 或 UPDATE 视图时触发，需要做的动作在触发器内容中定义。

## 1.创建

在 PostgreSQL 用 CREATE VIEW 语句创建视图，视图创建可以从一张表，多张表或者其他视图。

```sql
CREATE [TEMP | TEMPORARY] VIEW view_name AS
SELECT column1, column2.....
FROM table_name
WHERE [condition];
```

## 2.删除

要删除视图，只需使用带有 view_name 的 DROP VIEW 语句。DROP VIEW 的基本语法如下：

```sql
DROP VIEW view_name;
```

# 事务

TRANSACTION（事务）是数据库管理系统执行过程中的一个逻辑单位，由一个有限的数据库操作序列构成。

数据库事务通常包含了一个序列的对数据库的读/写操作。包含有以下两个目的：

- 为数据库操作序列提供了一个从失败中恢复到正常状态的方法，同时提供了数据库即使在异常状态下仍能保持一致性的方法。
- 当多个应用程序在并发访问数据库时，可以在这些应用程序之间提供一个隔离方法，以防止彼此的操作互相干扰。

当事务被提交给了数据库管理系统（DBMS），则 DBMS 需要确保该事务中的所有操作都成功完成且其结果被永久保存在数据库中，如果事务中有的操作没有成功完成，则事务中的所有操作都需要回滚，回到事务执行前的状态；同时，该事务对数据库或者其他事务的执行无影响，所有的事务都好像在独立的运行。

## 事务的属性

事务具有以下四个标准属性，通常根据首字母缩写为 ACID：

- 原子性（Atomicity）：事务作为一个整体被执行，包含在其中的对数据库的操作要么全部被执行，要么都不执行。
- 一致性（Consistency）：事务应确保数据库的状态从一个一致状态转变为另一个一致状态。一致状态的含义是数据库中的数据应满足完整性约束。
- 隔离性（Isolation）：多个事务并发执行时，一个事务的执行不应影响其他事务的执行。
- 持久性（Durability）：已被提交的事务对数据库的修改应该永久保存在数据库中。

## 事务控制

使用下面的命令来控制事务：

BEGIN TRANSACTION：开始一个事务。**COMMIT**：事务确认，或者可以使用 END TRANSACTION 命令。**ROLLBACK**：事务回滚。

事务控制命令只与 INSERT、UPDATE 和 DELETE 一起使用。他们不能在创建表或删除表时使用，因为这些操作在数据库中是自动提交的。

事务可以使用 BEGIN TRANSACTION 命令或简单的 BEGIN 命令来启动。此类事务通常会持续执行下去，直到遇到下一个 COMMIT 或 ROLLBACK 命令。不过在数据库关闭或发生错误时，事务处理也会回滚。以下是启动一个事务的简单语法：

```sql
开始： BEGIN; 或者  BEGIN TRANSACTION;
提交： COMMIT;或者  END TRANSACTION;
回滚：	ROLLBACK ;
```

# 锁

锁主要是为了保持数据库数据的一致性，可以阻止用户修改一行或整个表，一般用在并发较高的数据库中。

在多个用户访问数据库的时候若对并发操作不加控制就可能会读取和存储不正确的数据，破坏数据库的一致性。

数据库中有两种基本的锁：**排它锁（Exclusive Locks）**和**共享锁（Share Locks）**。

如果数据对象加上排它锁，则其他的事务不能对它读取和修改。

如果加上共享锁，则该数据库对象可以被其他事务读取，但不能修改。

## 语法

```sql
LOCK [ TABLE ]
name
 IN
lock_mode
```

> - name：要锁定的现有表的名称（可选模式限定）。如果只在表名之前指定，则只锁定该表。如果未指定，则锁定该表及其所有子表（如果有）。
> - lock_mode：锁定模式指定该锁与哪个锁冲突。如果没有指定锁定模式，则使用限制最大的访问独占模式。可能的值是：ACCESS SHARE，ROW SHARE， ROW EXCLUSIVE， SHARE UPDATE EXCLUSIVE， SHARE，SHARE ROW EXCLUSIVE，EXCLUSIVE，ACCESS EXCLUSIVE。

一旦获得了锁，锁将在当前事务的其余时间保持。没有解锁表命令；锁总是在事务结束时释放。

## 死锁

当两个事务彼此等待对方完成其操作时，可能会发生死锁。尽管 PostgreSQL 可以检测它们并以回滚结束它们，但死锁仍然很不方便。为了防止应用程序遇到这个问题，请确保将应用程序设计为以相同的顺序锁定对象。

## 咨询锁

PostgreSQL 提供了创建具有应用程序定义含义的锁的方法。这些被称为咨询锁。由于系统不强制使用它们，所以正确使用它们取决于应用程序。咨询锁对于不适合 MVCC 模型的锁定策略非常有用。

例如，咨询锁的一个常见用途是模拟所谓"平面文件"数据管理系统中典型的悲观锁定策略。虽然存储在表中的标志可以用于相同的目的，但是通知锁更快，避免了表膨胀，并且在会话结束时由服务器自动清理。

# 权限

无论何时创建数据库对象，都会为其分配一个所有者，所有者通常是执行 create 语句的人。

对于大多数类型的对象，初始状态是只有所有者(或超级用户)才能修改或删除对象。要允许其他角色或用户使用它，必须为该用户设置权限。

在 PostgreSQL 中，权限分为以下几种：

- SELECT
- INSERT
- UPDATE
- DELETE
- TRUNCATE
- REFERENCES
- TRIGGER
- CREATE
- CONNECT
- TEMPORARY
- EXECUTE
- USAGE

根据对象的类型(表、函数等)，将指定权限应用于该对象。

要向用户分配权限，可以使用 GRANT 命令。

## grant

GRANT 命令的基本语法如下：

```sql
GRANT privilege [, ...]
ON object [, ...]
TO { PUBLIC | GROUP group | username }
```

- privilege − 值可以为：SELECT，INSERT，UPDATE，DELETE， RULE，ALL。
- object − 要授予访问权限的对象名称。可能的对象有： table， view，sequence。
- PUBLIC − 表示所有用户。
- GROUP group − 为用户组授予权限。
- username − 要授予权限的用户名。PUBLIC 是代表所有用户的简短形式。

## revoke

另外，我们可以使用 REVOKE 命令取消权限，REVOKE 语法：

```sql
REVOKE privilege [, ...]
ON object [, ...]
FROM { PUBLIC | GROUP groupname | username }
```

## 用户

```sql
CREATE USER runoob WITH PASSWORD 'password'; -- 创建用户
DROP USER runoob; -- 删除用户
```

# 函数

## 日期

两个时间可以用+-\*/做运算，如：

```sql
date '2001-09-28' + integer '7'  -- date '2001-10-05'
```

函数

| 函数                                                                                                                                                                            | 返回类型                   | 描述                                                                                            | 例子                                                       | 结果                       |
| :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | :------------------------- | :---------------------------------------------------------------------------------------------- | :--------------------------------------------------------- | :------------------------- |
| `age(timestamp, timestamp)`                                                                                                                                                     | `interval`                 | 减去参数后的"符号化"结果，使用年和月，不只是使用天                                              | `age(timestamp '2001-04-10', timestamp '1957-06-13')`      | `43 years 9 mons 27 days`  |
| `age(timestamp)`                                                                                                                                                                | `interval`                 | 从`current_date`减去参数后的结果（在午夜）                                                      | `age(timestamp '1957-06-13')`                              | `43 years 8 mons 3 days`   |
| `clock_timestamp()`                                                                                                                                                             | `timestamp with time zone` | 实时时钟的当前时间戳（在语句执行时变化）                                                        |                                                            |                            |
| `current_date`                                                                                                                                                                  | `date`                     | 当前的日期；                                                                                    |                                                            |                            |
| `current_time`                                                                                                                                                                  | `time with time zone`      | 当日时间；                                                                                      |                                                            |                            |
| `current_timestamp`                                                                                                                                                             | `timestamp with time zone` | 当前事务开始时的时间戳；                                                                        |                                                            |                            |
| `date_part(text, timestamp)`                                                                                                                                                    | `double precision`         | 获取子域(等效于`extract`)；                                                                     | `date_part('hour', timestamp '2001-02-16 20:38:40')`       | `20`                       |
| `date_part(text, interval)`                                                                                                                                                     | `double precision`         | 获取子域(等效于`extract`)；                                                                     | `date_part('month', interval '2 years 3 months')`          | `3`                        |
| `date_trunc(text, timestamp)`                                                                                                                                                   | `timestamp`                | 截断成指定的精度；                                                                              | `date_trunc('hour', timestamp '2001-02-16 20:38:40')`      | `2001-02-16 20:00:00`      |
| `date_trunc(text, interval)`                                                                                                                                                    | `interval`                 | 截取指定的精度，                                                                                | `date_trunc('hour', interval '2 days 3 hours 40 minutes')` | `2 days 03:00:00`          |
| `extract(field from timestamp)`                                                                                                                                                 | `double precision`         | 获取子域；                                                                                      | `extract(hour from timestamp '2001-02-16 20:38:40')`       | `20`                       |
| `extract(field from interval)`                                                                                                                                                  | `double precision`         | 获取子域；                                                                                      | `extract(month from interval '2 years 3 months')`          | `3`                        |
| `isfinite(date)`                                                                                                                                                                | `boolean`                  | 测试是否为有穷日期(不是 +/-无穷)                                                                | `isfinite(date '2001-02-16')`                              | `true`                     |
| `isfinite(timestamp)`                                                                                                                                                           | `boolean`                  | 测试是否为有穷时间戳(不是 +/-无穷)                                                              | `isfinite(timestamp '2001-02-16 21:28:30')`                | `true`                     |
| `isfinite(interval)`                                                                                                                                                            | `boolean`                  | 测试是否为有穷时间间隔                                                                          | `isfinite(interval '4 hours')`                             | `true`                     |
| `justify_days(interval)`                                                                                                                                                        | `interval`                 | 按照每月 30 天调整时间间隔                                                                      | `justify_days(interval '35 days')`                         | `1 mon 5 days`             |
| `justify_hours(interval)`                                                                                                                                                       | `interval`                 | 按照每天 24 小时调整时间间隔                                                                    | `justify_hours(interval '27 hours')`                       | `1 day 03:00:00`           |
| `justify_interval(interval)`                                                                                                                                                    | `interval`                 | 使用`justify_days`和`justify_hours`调整时间间隔的同时进行正负号调整                             | `justify_interval(interval '1 mon -1 hour')`               | `29 days 23:00:00`         |
| `localtime`                                                                                                                                                                     | `time`                     | 当日时间；                                                                                      |                                                            |                            |
| `localtimestamp`                                                                                                                                                                | `timestamp`                | 当前事务开始时的时间戳；                                                                        |                                                            |                            |
| `make_date(year int, month int, day int)`                                                                                                                                       | `date`                     | 为年、月和日字段创建日期                                                                        | `make_date(2013, 7, 15)`                                   | `2013-07-15`               |
| `make_interval(years int DEFAULT 0, months int DEFAULT 0, weeks int DEFAULT 0, days int DEFAULT 0, hours int DEFAULT 0, mins int DEFAULT 0, secs double precision DEFAULT 0.0)` | `interval`                 | 从年、月、周、天、小时、分钟和秒字段中创建间隔                                                  | `make_interval(days := 10)`                                | `10 days`                  |
| `make_time(hour int, min int, sec double precision)`                                                                                                                            | `time`                     | 从小时、分钟和秒字段中创建时间                                                                  | `make_time(8, 15, 23.5)`                                   | `08:15:23.5`               |
| `make_timestamp(year int, month int, day int, hour int, min int, sec double precision)`                                                                                         | `timestamp`                | 从年、月、日、小时、分钟和秒字段中创建时间戳                                                    | `make_timestamp(2013, 7, 15, 8, 15, 23.5)`                 | `2013-07-15 08:15:23.5`    |
| `make_timestamptz(year int, month int, day int, hour int, min int, sec double precision, [ timezone text ])`                                                                    | `timestamp with time zone` | 从年、月、日、小时、分钟和秒字段中创建带有时区的时间戳。 没有指定`timezone`时，使用当前的时区。 | `make_timestamptz(2013, 7, 15, 8, 15, 23.5)`               | `2013-07-15 08:15:23.5+01` |
| `now()`                                                                                                                                                                         | `timestamp with time zone` | 当前事务开始时的时间戳；                                                                        |                                                            |                            |
| `statement_timestamp()`                                                                                                                                                         | `timestamp with time zone` | 实时时钟的当前时间戳；                                                                          |                                                            |                            |
| `timeofday()`                                                                                                                                                                   | `text`                     | 与`clock_timestamp`相同，但结果是一个`text` 字符串；                                            |                                                            |                            |
| `transaction_timestamp()`                                                                                                                                                       | `timestamp with time zone` | 当前事务开始时的时间戳；                                                                        |                                                            |                            |

## 聚合函数

PostgreSQL 内置函数也称为聚合函数，用于对字符串或数字数据执行处理。

下面是所有通用 PostgreSQL 内置函数的列表：

- COUNT 函数：用于计算数据库表中的行数。
- MAX 函数：用于查询某一特定列中最大值。
- MIN 函数：用于查询某一特定列中最小值。
- AVG 函数：用于计算某一特定列中平均值。
- SUM 函数：用于计算数字列所有值的总和。
- ARRAY 函数：用于输入值(包括 null)添加到数组中。
- Numeric 函数：完整列出一个 SQL 中所需的操作数的函数。
- String 函数：完整列出一个 SQL 中所需的操作字符的函数。

## 数学函数

## 字符串

## 类型转换相关函数

https://www.runoob.com/postgresql/postgresql-functions.html

# question

1. 权限

```
ls: cannot access '/docker-entrypoint-initdb.d/': Operation not permitted
```

solution：

```
方案一： 使用镜像 postgres:buster 代替latest
方案二： 升级docker到最新版本
```

# 附录

[菜鸟教程](https://www.runoob.com/postgresql/postgresql-tutorial.html)

[官方文档](http://www.postgres.cn/v2/document)
