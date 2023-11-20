# event 事件（定时任务）

​ 事件（event）也可称为事件调度器（event scheduler），是用来执行定时任务的一组 sql 集合，可以通俗理解成 mysql 中的定时器。一个事件可调用一次，也可周期性的启动。

​ 事件可以作为定时任务调度器，取代部分原来只能用操作系统的计划任务才能执行的工作。另外，更值得一提的是，mysql 的事件可以实现每秒钟执行一个任务，非常适合对实时性要求较高的环境，而操作系统的计划任务只能精确到每分钟一次。

​ 事件和触发器类似，都是在某些事情发生时启动。当数据库启动一条语句的时候，触发器就启动了，而事件是根据调度事件来启动的。由于他们彼此相似，所以事件也称为临时性触发器。

## 开启时间
```sql
-- 查看事件是否开启
show variables like 'event_scheduler'; -- on
select @@event_scheduler; --  on
show processlist; --

-- 开启事件
set global event_scheduler = on ;
-- 方法二，配置文件 【mysqld】添加
event_scheduler = on
--在配置文件中添加代码并保存文件后，重启 mysql 服务才能生效。
```

## 1. 创建

```sql
create event [if not exists] event_name
    on schedule schedule
    [on completion [not] preserve]
    [enable | disable | disable on slave]
    [comment 'comment']
    do event_body;
```

| 子句                                  | 说明                                                                                                                                                                                                                                                                                                        |
| ------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| definer                               | 可选 用于定义事件执行时检查权限的用户                                                                                                                                                                                                                                                                       |
| if not exists                         | 可选 用于判断要创建的事件是否存在                                                                                                                                                                                                                                                                           |
| event event_name                      | 必选 用于指定事件名称，event_name 的最大长度为 64 个字符 如果未指定 event_name，则默认为当前的 mysql 用户名（不区分大小写）                                                                                                                                                                                 |
| on schedule schedule                  | 必选 用于定义执行的时间和时间间隔 schedule 表示触发点                                                                                                                                                                                                                                                       |
| on completion [not] preserve          | 可选 用于定义事件是否循环执行，即是一次执行还是永久执行，默认为一次执行，即 not preserve                                                                                                                                                                                                                    |
| enable \| disable \| disable on slave | 可选，用于指定事件的一种属性。 其中，关键字 enable 表示该事件是活动的，即调度器检查事件是否必须调用； 关键字 disable 表示该事件是关闭的，即事件的声明存储到目录中，但是调度器不会检查它是否应该调用； 关键字 disable on slave 表示事件在从机中是关闭的。 如果不指定以上 3 个选项中的任何一个，默认为 enable |
| comment 'comment'                     | 可选，用于定义事件的注释                                                                                                                                                                                                                                                                                    |
| do event_body                         | 必选 用于指定事件启动时所要执行的代码，可以是任何有效的 sql 语句、存储过程或者一个计划执行的事件。 如果包含多条语句，则可以使用 begin..end 复合结构                                                                                                                                                         |

在 on schedule 子句中，参数 schedule 的值为一个 at 子句，用于指定事件在某个时刻发生，其语法格式如下：

```sql
at timestamp [+ interval interval]...
    | every interval
    [starts timestamp [+ interval interval] ...]
    [ends timestamp[+ interval interval]...]
```

参数说明如下：

- timestamp：一般用于只执行一次，表示一个具体的时间点，后面加上一个时间间隔，表示在这个时间间隔后事件发生。
- every 子句：用于事件在指定时间区间内每隔多长时间发生一次，其中 starts 子句用于指定开始时间；ends 子句用于指定结束时间。
- interval：一般用于周期性执行，表示一个从现在开始的时间，其值由一个数值和单位构成。例如，使用“4 week”表示 4 周，使用“'1:10'hour_minute”表示 1 小时 10 分钟。间隔的长短用 date_add() 函数支配。

interval 参数可以是以下值：

**year** | quarter | **month** | **day** | **hour** | **minute** |
week | **second** | year_month | day_hour | day_minute |
day_second | hour_minute | hour_second | minute_second

一般情况下，不建议使用不标准（以上未加粗关键字）的时间单位。

## 2. 查看

```sql
/*
创建好事件后，用户可以通过以下 3 种方式来查看事件的状态信息：
1. 查看 mysql.event
2. 查看 information_schema.events
3. 切换到相应的数据库后执行 show events;
*/

select * from information_schema.events limit 1\g
```

| 参数名               | 说明                                                                                                                                                                                                                                  |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| event_catalog        | 事件存放目录，一般情况下，值为 def，不建议修改                                                                                                                                                                                        |
| event_schema         | 事件所在的数据库                                                                                                                                                                                                                      |
| event_name           | 事件名称                                                                                                                                                                                                                              |
| definer              | 事件的定义者                                                                                                                                                                                                                          |
| time_zone            | 事件使用的时区，默认是 system，不建议修改                                                                                                                                                                                             |
| event_body           | 一般情况下，值为 sql，不建议修改                                                                                                                                                                                                      |
| event_definition     | 该事件的内容，可以是具体的 insert 等 sql，也可以是一个调用的存储过程                                                                                                                                                                  |
| event_type           | 事件类型，这个参数比较重要，在定义时指定 有两个值：recurring 和 one time recurring 表示只要符合条件就会重复执行，recurring 类型的事件一般为 null，表示该事件的预计执行时间 one time 只会调用 execute_at，针对 one-time 类型的事件有效 |
| interval_value       | 针对 recurring 类型的事件有效，表示执行间隔长度                                                                                                                                                                                       |
| interval_field       | 针对 recurring 类型的事件有效，表示执行间隔的单位，一般是 second，day 等值，可参考创建语法                                                                                                                                            |
| sql_mode             | 当前事件采用的 sql_mode                                                                                                                                                                                                               |
| starts               | 针对 recurring 类型的事件有效，表示一个事件从哪个时间点开始执行，和 one-time 的 execute_at 功能类似。 为 null 时表示一符合条件就开始执行                                                                                              |
| ends                 | 针对 recurring 类型的事件有效，表示一个事件到了哪个时间点后不再执行，如果为 null 就是永不停止                                                                                                                                         |
| status               | 一般有三个值，enabled、disabled 和 slaveside_disabled                                                                                                                                                                                 |
| on_completion        | 只有两个值，preserve 和 not preserve                                                                                                                                                                                                  |
| created              | 事件的创建时间                                                                                                                                                                                                                        |
| last_altered         | 事件最近一次被修改的时间                                                                                                                                                                                                              |
| last_executed        | 事件最近一次执行的时间，如果为 null 表示从未执行过                                                                                                                                                                                    |
| event_comment        | 事件的注释信息                                                                                                                                                                                                                        |
| originator           | 当前事件创建时的 server-id，用于主从上的处理，比如 slaveside_disabled                                                                                                                                                                 |
| character_set_client | 事件创建时的客户端字符集                                                                                                                                                                                                              |
| collation_connection | 事件创建时的连接字符校验规则                                                                                                                                                                                                          |
| database_collation   | 事件创建时的数据库字符集校验规则                                                                                                                                                                                                      |

## 3. 修改/删除

```sql
--修改
alter event event_name
    on schedule schedule
    [on completion [not] preserve]
    [enable | disable | disable on slave]
    [comment 'comment']
    do event_body;


-- 删除
drop event [if exists] event_name;
```
