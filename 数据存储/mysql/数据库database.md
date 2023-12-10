# ddl 操作数据库

- **ddl（data definition language）**：数据定义语言，用来定义数据库对象：库、表、列等。

## 创建 create

```sql
 create database 数据库名
 create database 数据库名 character set 编码方式
 create database 数据库名 set 编码方式 collate 排序规则
 -- 编码方式:gb2312,utf8,gbk,iso-8859-1
```

## 查看 show

1. 查看当前数据库服务器中的所有数据库 `show databases;`
2. 查看前面创建的 mydb2 数据库的定义信息
3. `show status`，用于显示广泛的服务器状态信息；
4. `show create database`和`show create table`，分别用来显示创建特定数据库或表的 mysql 语句；
5. `show grants`，用来显示授予用户（所有用户或特定用户）的安全权限；
6. `show errors`和`show warnings`，用来显示服务器错误或警告消息。
7. 显示帮助 `help show`
8. `show character set;` 显示所有可用字符\描述\默认校对
9. `show collation;` 显示所有可用校对

## 修改 alter

1. 修改数据库的字符集

```sql
alter database 数据库名 character set 编码方式
alter database mydb2 character set utf8;
```

## 删除 drop

```sql
drop database 数据库名;
```

## 切换 use

1. 查看当前使用的数据库`select database();`
2. 切换数据库 `use 数据库名;`
3. 如果不使用 use,可以使用限定符`.`访问某个数据库的表\列,例:`数据库.表.列`
