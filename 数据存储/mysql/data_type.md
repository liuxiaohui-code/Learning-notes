# MySQL 数据类型

## 数字

| 类型        | 大小   | 用途           |
| ----------- | ------ | -------------- |
| `tinyint`   | 1 字节 | 小整数值       |
| `smallint`  | 2 字节 | 大整数值       |
| `mediumint` | 3 字节 | 大整数值       |
| `int`       | 4 字节 | 大整数值       |
| `bigint`    | 8 字节 | 极大整数值     |
| `float`     | 4 字节 | 单精度浮点数值 |
| `double`    | 8 字节 | 双精度浮点数值 |

## 字符串与字节码

字符串类型:
字符串类型指 CHAR、VARCHAR、BINARY、VARBINARY、BLOB、TEXT、ENUM 和 SET

CHAR 和 VARCHAR 类型类似，但它们保存和检索的方式不同。它们的最大长度和是否尾部空格被保留等方面也不同。在存储或检索过程中不进行大小写转换。
BINARY 和 VARBINARY 类类似于 CHAR 和 VARCHAR，不同的是它们包含二进制字符串而不要非二进制字符串。也就是说，它们包含字节字符串而不是字符字符串。这说明它们没有字符集，并且排序和比较基于列值字节的数值值。
BLOB 是一个二进制大对象，可以容纳可变数量的数据。有 4 种 BLOB 类型：TINYBLOB、BLOB、MEDIUMBLOB 和 LONGBLOB。它们只是可容纳值的最大长度不同。
有 4 种 TEXT 类型：TINYTEXT、TEXT、MEDIUMTEXT 和 LONGTEXT。这些对应 4 种 BLOB 类型，有相同的最大长度和存储需求。

| 类型       | 大小                 | 用途                            |
| ---------- | -------------------- | ------------------------------- |
| CHAR       | 0-255 字节           | 定长字符串                      |
| VARCHAR    | 0-65535 字节         | 变长字符串                      |
| TINYBLOB   | 0-255 字节           | 不超过 255 个字符的二进制字符串 |
| TINYTEXT   | 0-255 字节           | 短文本字符串                    |
| BLOB       | 0-65 535 字节        | 二进制形式的长文本数据          |
| TEXT       | 0-65 535 字节        | 长文本数据                      |
| MEDIUMBLOB | 0-16 777 215 字节    | 二进制形式的中等长度文本数据    |
| MEDIUMTEXT | 0-16 777 215 字节    | 中等长度文本数据                |
| LONGBLOB   | 0-4 294 967 295 字节 | 二进制形式的极大文本数据        |
| LONGTEXT   | 0-4 294 967 295 字节 | 极大文本数据                    |

## 时间

日期类型:
表示时间值的日期和时间类型为 DATETIME、DATE、TIMESTAMP、TIME 和 YEAR。
每个时间类型有一个有效值范围和一个"零"值，当指定不合法的 MySQL 不能表示的值时使用"零"值。
TIMESTAMP 类型有专有的自动更新特性

| 类型        | 大小(字节) | 格式                | 用途                                                       |
| ----------- | ---------- | ------------------- | ---------------------------------------------------------- |
| `DATE`      | 3          | YYYY-MM-DD          | 日期值                                                     |
| `TIME`      | 3          | HH:MM:SS            | 时间值或持续时间                                           |
| `YEAR`      | 1          | YYYY                | 年份值                                                     |
| `DATETIME`  | 8          | YYYY-MM-DD HH:MM:SS | 混合日期和时间值                                           |
|             |
| `TIMESTAMP` | 4          | YYYYMMDD HHMMSS     | 混合日期和时间值，时间戳 ,当更新数据的时候自动添加更新时间 |

## JSON

### 插入

插入时:相当于插入字符串

```sql
insert into dept VALUES(1,'部门1','{"deptName": "部门1", "deptId": "1", "deptLeaderId": "3"}');
```

### 一般查询

使用 `json字段名->'$.json属性'` 进行查询条件

```sql
SELECT * from dept WHERE json_value->'$.deptLeaderId'='5';
SELECT * from dept WHERE json_value->'$.deptLeaderId'='5' and dept='部门3';
SELECT * from dept WHERE json_value->'$.deptLeaderId'='5' and json_value->'$.deptId'='5';
/* 多表联查 */
SELECT * from dept,dept_leader WHERE dept.json_value->'$.deptLeaderId'=dept_leader.json_value->'$.id' ;
```

### 函数查询

```sql
select * from t_law_responsibility where JSON_CONTAINS(serial_numbers,JSON_ARRAY('a'));

UPDATE t_law_responsibility SET serial_numbers = JSON_ARRAY('a','b','c') WHERE tlr_id = 2;
/*json_extract(字段名,$.json字段名) 从json中返回想要的字段*/
select id,json_extract(json_value,'$.deptName') as deptName from dept;

/*JSON_CONTAINS(target, candidate[, path]) JSON格式数据是否在字段中包含特定对象*/
select * from dept WHERE JSON_CONTAINS(json_value, JSON_OBJECT("deptName","部门5"))

/*JSON_OBJECT([key, val[, key, val] …]):将一个键值对列表转换成json对象*/
SELECT * from (
SELECT *,json_value->'$.deptName' as deptName FROM dept
) t WHERE JSON_CONTAINS(deptName,JSON_OBJECT("depp","dd")); /*用于查询 $.deptName.depp == 'dd'*/

/*函数JSON_ARRAY([val[, val] …]):创建JSON数组*/
SELECT * from dept WHERE JSON_CONTAINS(json_value->'$.deptName',JSON_ARRAY("1"))

/*函数JSON_TYPE(json_val) 查询某个json字段属性类型*/
SELECT json_value->'$.deptName' ,JSON_TYPE(json_value->'$.deptName') as type from dept ;

/*函数JSON_EXTRACT() ：从JSON文档返回数据*/
SELECT * FROM dept WHERE JSON_EXTRACT(json_value,'$.deptName') like '%部门%';

/*函数JSON_KEYS() ：JSON文档中的键数组*/
SELECT JSON_KEYS(json_value) FROM dept ; /*查询json格式数据中的所有key*/

/*接下来的3种函数都是新增数据类型的：*/
/* JSON_SET(json_doc, path, val[, path, val] …)  将数据插入JSON格式中，有key则替换，无key则新增*/
update dept set json_value=JSON_SET('{"deptName": "部门2", "deptId": "2", "deptLeaderId": "4"}','$.deptName','新增的部门1','$.newData','新增的数据') WHERE id=2;
/*注意：json_doc如果不带这个单元格之前的值，之前的值是会新值被覆盖的*/


/*JSON_INSERT(json_doc, path, val[, path, val] …) 插入值（往json中插入新值，但不替换已经存在的旧值） */
/*JSON_REPLACE(json_doc, path, val[, path, val] …)  */


```

## 运算符

- **算术运算符** `+，-，*，/(除法),求余(%)`
- **赋值运算符** `:=`
- **逻辑运算符** `and(并且),or(或者),not（取非）`
- **关系运算符** `>,<,>=,<=,!=(不等于),=(等于),<>(不等于)`
- **关键字**： `BETWEEN…AND； IN(set)； IS NULL； AND；OR； NOT；IS NOT NULL `

| 优先级由低到高排列 | 运算符                                                         |
| ------------------ | -------------------------------------------------------------- |
| 1                  | =(赋值运算）、:=                                               |
| 2                  | II、OR                                                         |
| 3                  | XOR                                                            |
| 4                  | &&、AND                                                        |
| 5                  | NOT                                                            |
| 6                  | BETWEEN、CASE、WHEN、THEN、ELSE                                |
| 7                  | =(比较运算）、<=>、>=、>、<=、<、<>、!=、 IS、LIKE、REGEXP、IN |
| 8                  | \|                                                             |
| 9                  | &                                                              |
| 10                 | <<、>>                                                         |
| 11                 | -(减号）、+                                                    |
| 12                 | \*、/、%                                                       |
| 13                 | ^                                                              |
| 14                 | -(负号）、〜（位反转）                                         |
| 15                 | !                                                              |
