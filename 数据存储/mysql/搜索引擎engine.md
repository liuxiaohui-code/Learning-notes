# 存储引擎

在 mysql 中，可以通过以下两种方式设置表的存储引擎：

1. 创建表时指定存储引擎：在创建表时，可以使用 engine 关键字指定表的存储引擎。例如，创建一个使用 innodb 存储引擎的表的 sql 语句如下：

```sql
create table table_name (column_name column_type, ...) engine=innodb;
```

2. 修改表的存储引擎：可以使用 alter table 语句修改表的存储引擎。例如，将表的存储引擎修改为 innodb 的 sql 语句如下：

```sql
alter table table_name engine=innodb;
```

需要注意的是，不是所有的存储引擎都支持相同的功能和特性，因此在选择存储引擎时需要根据具体的应用场景和需求进行选择。同时，在使用存储引擎时需要注意其特性和限制，以充分发挥其性能和可靠性。

此外，在 mysql 中，可以使用以下命令查看当前的默认存储引擎和支持的存储引擎：

```sql
show variables like 'default_storage_engine';
show engines;
```

第一个命令用于查看当前的默认存储引擎，第二个命令用于查看支持的存储引擎列表和各个存储引擎的状态。

总之，在 mysql 中，可以通过 create table 和 alter table 语句来设置表的存储引擎，并且需要根据应用场景和需求选择合适的存储引擎。

[mysql 存储引擎 innodb 与 myisam 的六大区别](https://my.oschina.net/junn/blog/183341)

引擎可以混用,但外键和事务不能跨引擎

```sql
create table xx()engine=innodb; -- 创建表时设定引擎
```

mysql 支持数个存储引擎作为对不同表的类型的处理器。mysql 存储引擎包括处理事务安全表的引擎和处理非事务安全表的引擎：

- `innodb` 可靠的`事务处理`引擎,支持外键和事务管理,性能中下
- `bdb` 提供事务安全表,被包含在为支持它的操作系统发布的 mysql-max 二进制分发版里。innodb 也默认被包括在所 有 mysql 5.1 二进制分发版里，你可以按照喜好通过配置 mysql 来允许或禁止任一引擎。
- `myisam` 默认引擎,管理**非事务**表。它提供`高速存储和检索`，以及`全文搜索`能力。myisam 在所有 mysql 配置里被支持，它是默认的存储引擎，除非你配置 mysql 默认使用另外一个引擎。
- `memory` 功能类似于 `myisam` ,支持**全文搜索**,**非事务**,数据存储在**内存**而非磁盘,速度特别快,适合临时表;
- `example` 存储引擎是一个"存根"引擎，它不做什么。你可以用这个引擎创建表，但没有数据被存储于其中或从其中检索。这个引擎的目的是服务，在 mysql 源代码中的一个例子，它演示说明如何开始编写新存储引擎。同样，它的主要兴趣是对开发者。
- ndb cluster 是被 mysql cluster 用来实现分割到多台计算机上的表的存储引擎。它在 mysql-max 5.1 二进制分发版里提供。这个存储引擎当前只被 linux, solaris, 和 mac os x 支持。在未来的 mysql 分发版中，我们想要添加其它平台对这个引擎的支持，包括 windows。
- `archive` 存储引擎被用来无索引地，非常小地覆盖存储的大量数据。
- `csv` 存储引擎把数据以逗号分隔的格式存储在文本文件中,它的键没有索引，不支持为空，不支持自增。
- `blackhole` 存储引擎接受但不存储数据，并且检索总是返回一个空集。
- `federated` 存储引擎把数据存在远程数据库中。在 mysql 5.1 中，它只和 mysql 一起工作，使用 mysql c client api。在未来的分发版中，我们想要让它使用其它驱动器或客户端连接方法连接到另外的数据源。
