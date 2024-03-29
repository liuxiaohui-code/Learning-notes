
# 函数

资料： http://c.biancheng.net/mysql/function/

官方文档： https://dev.mysql.com/doc/refman/5.7/en/

## 数值型函数

| 函数名称                                                         | 作 用                                                       |
| ---------------------------------------------------------------- | ----------------------------------------------------------- |
| [ABS](http://c.biancheng.net/mysql/abc.html)                     | 求绝对值                                                    |
| [SQRT](http://c.biancheng.net/mysql/sqrt.html)                   | 求二次方根                                                  |
| [MOD](http://c.biancheng.net/mysql/mod.html)                     | 求余数                                                      |
| [CEIL 和 CEILING](http://c.biancheng.net/mysql/ceil_celing.html) | 两个函数功能相同，都是返回不小于参数的最小整数，即向上取整  |
| [FLOOR](http://c.biancheng.net/mysql/floor.html)                 | 向下取整，返回值转化为一个 BIGINT                           |
| [RAND](http://c.biancheng.net/mysql/rand.html)                   | 生成一个 0~1 之间的随机数，传入整数参数是，用来产生重复序列 |
| [ROUND](http://c.biancheng.net/mysql/round.html)                 | 对所传参数进行四舍五入                                      |
| [SIGN](http://c.biancheng.net/mysql/sign.html)                   | 返回参数的符号                                              |
| [POW 和 POWER](http://c.biancheng.net/mysql/pow_power.html)      | 两个函数的功能相同，都是所传参数的次方的结果值              |
| [SIN](http://c.biancheng.net/mysql/sin.html)                     | 求正弦值                                                    |
| [ASIN](http://c.biancheng.net/mysql/asin.html)                   | 求反正弦值，与函数 SIN 互为反函数                           |
| [COS](http://c.biancheng.net/mysql/cos.html)                     | 求余弦值                                                    |
| [ACOS](http://c.biancheng.net/mysql/acos.html)                   | 求反余弦值，与函数 COS 互为反函数                           |
| [TAN](http://c.biancheng.net/mysql/tan.html)                     | 求正切值                                                    |
| [ATAN](http://c.biancheng.net/mysql/atan.html)                   | 求反正切值，与函数 TAN 互为反函数                           |
| [COT](http://c.biancheng.net/mysql/cot.html)                     | 求余切值                                                    |

## 字符串型函数

| 函数名称                                                 | 作 用                                                                |
| -------------------------------------------------------- | -------------------------------------------------------------------- |
| [LENGTH](http://c.biancheng.net/mysql/length.html)       | 计算字符串长度函数，返回字符串的字节长度                             |
| [CONCAT](http://c.biancheng.net/mysql/concat.html)       | 合并字符串函数，返回结果为连接参数产生的字符串，参数可以使一个或多个 |
| [INSERT](http://c.biancheng.net/mysql/insert.html)       | 替换字符串函数                                                       |
| [LOWER](http://c.biancheng.net/mysql/lower.html)         | 将字符串中的字母转换为小写                                           |
| [UPPER](http://c.biancheng.net/mysql/upper.html)         | 将字符串中的字母转换为大写                                           |
| [LEFT](http://c.biancheng.net/mysql/left.html)           | 从左侧字截取符串，返回字符串左边的若干个字符                         |
| [RIGHT](http://c.biancheng.net/mysql/right.html)         | 从右侧字截取符串，返回字符串右边的若干个字符                         |
| [TRIM](http://c.biancheng.net/mysql/trim.html)           | 删除字符串左右两侧的空格                                             |
| [REPLACE](http://c.biancheng.net/mysql/replace.html)     | 字符串替换函数，返回替换后的新字符串                                 |
| [SUBSTRING](http://c.biancheng.net/mysql/substring.html) | 截取字符串，返回从指定位置开始的指定长度的字符换                     |
| [REVERSE](http://c.biancheng.net/mysql/reverse.html)     | 字符串反转（逆序）函数，返回与原始字符串顺序相反的字符串             |

## 时间函数

| 函数名称                                                                          | 作 用                                                           |
| --------------------------------------------------------------------------------- | --------------------------------------------------------------- |
| [CURDATE 和 CURRENT_DATE](http://c.biancheng.net/mysql/curdate_current_date.html) | 两个函数作用相同，返回当前系统的日期值                          |
| [CURTIME 和 CURRENT_TIME](http://c.biancheng.net/mysql/curtime_current_time.html) | 两个函数作用相同，返回当前系统的时间值                          |
| [NOW 和 SYSDATE](http://c.biancheng.net/mysql/now_sysdate.html)                   | 两个函数作用相同，返回当前系统的日期和时间值                    |
| [UNIX_TIMESTAMP](http://c.biancheng.net/mysql/unix_timestamp.html)                | 获取 UNIX 时间戳函数，返回一个以 UNIX 时间戳为基础的无符号整数  |
| [FROM_UNIXTIME](http://c.biancheng.net/mysql/from_unixtime.html)                  | 将 UNIX 时间戳转换为时间格式，与 UNIX_TIMESTAMP 互为反函数      |
| [MONTH](http://c.biancheng.net/mysql/month.html)                                  | 获取指定日期中的月份                                            |
| [MONTHNAME](http://c.biancheng.net/mysql/monthname.html)                          | 获取指定日期中的月份英文名称                                    |
| [DAYNAME](http://c.biancheng.net/mysql/dayname.html)                              | 获取指定曰期对应的星期几的英文名称                              |
| [DAYOFWEEK](http://c.biancheng.net/mysql/dayofweek.html)                          | 获取指定日期对应的一周的索引位置值                              |
| [WEEK](http://c.biancheng.net/mysql/week.html)                                    | 获取指定日期是一年中的第几周，返回值的范围是否为 0〜52 或 1〜53 |
| [DAYOFYEAR](http://c.biancheng.net/mysql/dayofyear.html)                          | 获取指定曰期是一年中的第几天，返回值范围是 1~366                |
| [DAYOFMONTH](http://c.biancheng.net/mysql/dayofmonth.html)                        | 获取指定日期是一个月中是第几天，返回值范围是 1~31               |
| [YEAR](http://c.biancheng.net/mysql/year.html)                                    | 获取年份，返回值范围是 1970〜2069                               |
| [TIME_TO_SEC](http://c.biancheng.net/mysql/time_to_sec.html)                      | 将时间参数转换为秒数                                            |
| [SEC_TO_TIME](http://c.biancheng.net/mysql/sec_to_time.html)                      | 将秒数转换为时间，与 TIME_TO_SEC 互为反函数                     |
| [DATE_ADD 和 ADDDATE](http://c.biancheng.net/mysql/date_add_adddate.html)         | 两个函数功能相同，都是向日期添加指定的时间间隔                  |
| [DATE_SUB 和 SUBDATE](http://c.biancheng.net/mysql/date_sub_subdate.html)         | 两个函数功能相同，都是向日期减去指定的时间间隔                  |
| [ADDTIME](http://c.biancheng.net/mysql/addtime.html)                              | 时间加法运算，在原始时间上添加指定的时间                        |
| [SUBTIME](http://c.biancheng.net/mysql/subtime.html)                              | 时间减法运算，在原始时间上减去指定的时间                        |
| [DATEDIFF](http://c.biancheng.net/mysql/datediff.html)                            | 获取两个日期之间间隔，返回参数 1 减去参数 2 的值                |
| [DATE_FORMAT](http://c.biancheng.net/mysql/date_format.html)                      | 格式化指定的日期，根据参数返回指定格式的值                      |
| [WEEKDAY](http://c.biancheng.net/mysql/weekday.html)                              | 获取指定日期在一周内的对应的工作日索引                          |

## 聚合函数

可以使用 distinct 限定列之后进行计算

| 函数名称                                         | 作用                             |
| ------------------------------------------------ | -------------------------------- |
| [MAX](http://c.biancheng.net/mysql/max.html)     | 查询指定列的最大值               |
| [MIN](http://c.biancheng.net/mysql/min.html)     | 查询指定列的最小值               |
| [COUNT](http://c.biancheng.net/mysql/count.html) | 统计查询结果的行数               |
| [SUM](http://c.biancheng.net/mysql/sum.html)     | 求和，返回指定列的总和           |
| [AVG](http://c.biancheng.net/mysql/avg.html)     | 求平均值，返回指定列数据的平均值 |

## 流程控制函数

| 函数名称                                           | 作用           |
| -------------------------------------------------- | -------------- |
| [IF](http://c.biancheng.net/mysql/if.html)         | 判断，流程控制 |
| [IFNULL](http://c.biancheng.net/mysql/ifnull.html) | 判断是否为空   |
| [CASE](http://c.biancheng.net/mysql/case.html)     | 搜索语句       |

