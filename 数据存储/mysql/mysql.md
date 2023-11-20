# 前言

## 知识图谱

1. 数据库概念：数据库的定义、分类、特点、优缺点等。
2. 数据库设计：数据模型的设计、实体关系图、范式理论等。
3. 数据库管理：数据库的创建、备份、恢复、优化、安全等。
4. SQL语言：SQL语句的基本语法、数据查询、数据操作、数据类型、函数等。
5. 存储引擎：存储引擎的概念、分类、特点、优缺点等。
6. 数据库连接：连接池的概念、实现原理、优化等。
7. MySQL工具：MySQL客户端、MySQL Workbench、Navicat等。
8. 高级应用：事务、视图、存储过程、触发器、索引、分区等。
9. 运维实践：MySQL的监控、调优、容灾、备份、恢复等


## 名词

database 数据库,
DBMS（数据库管理系统）
表（table） 某种特定类型数据的结构化清单
模式（schema） 关于数据库和表的布局及特性的信息。
列（column） 表中的一个字段。
数据类型（datatype） 所容许的数据的类型。
行（row） 表中的一个记录。
主键（primary key）① 一一列（或一组列），其值能够唯一区分表中每个行。
SQL（发音为字母 S-Q-L 或 sequel）是结构化查询语言（Structured Query Language）的缩写。SQL 是一种专门用来与数据库通信的语言。

# install

## Linux

### docker 启动 MySQL

```
docker run -itd --name mysql-test -p 3306:3306 -e MYSQL_ROOT_PASSWORD=brilliance -e MYSQL_USER=bcbrowser -e MYSQL_PASSWORD=brilliance -e MYSQL_DATABASE=bcbrowser mysql:5.7

-e:
      - MYSQL_ROOT_PASSWORD=brilliance
      - MYSQL_DATABASE=bcbrowser
      - MYSQL_USER=bcbrowser
      - MYSQL_PASSWORD=brilliance

mkdir -p /usr/local/mysql/mysql-config/data /usr/local/mysql/mysql-config/conf

docker run -it -p 3307:3307 -v /usr/local/mysql/mysql-config/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --rm --name myMysql mysql:5.7 /bin/bash

alter user 'bcbrowser'@'%' identified with mysql_native_password by 'brilliance';
flush privileges;
```

### mysql5.7 安装

```sh
#############下载
mkdir /usr/local/mysql
cd /usr/local/mysql
wget -c https://cdn.mysql.com//Downloads/MySQL-5.7/mysql-5.7.35-linux-glibc2.12-x86_64.tar.gz
tar -zxvf mysql-5.7.35-linux-glibc2.12-x86_64.tar.gz -C /usr/local/mysql
mv mysql-5.7.35-linux-glibc2.12-x86_64/ mysql-5.7.35
#查看所有用户组信息是否存在mysql组，不存在则创建，存在则直接新建用户
cat /etc/group | grep mysql
##################### 查看是否存在mysql用户
cat /etc/passwd |grep mysql
###########################六、创建用户组、用户
###################### 创建用户组
groupadd mysql
################## 创建用户
useradd -r -g mysql mysql
#############################、创建mysql的data数据目录
mkdir /usr/mysql/mysql-5.7.35/data
#########################授权目录、用户
# 将 /usr/local/mysql/mysql-5.7.35 的所有者及所属组改为mysql
cd /usr/mysql/mysql-5.7.35
chown -R mysql:mysql ./
####################修改 my.cnf 配置
vim /etc/my.cnf
#按 i 键进入 my.cnf 的编辑模式，关于my.cnf 的详细配置在文章结尾处有详细介绍
[mysqld]
datadir=/usr/mysql/mysql-5.7.35/data
socket=/tmp/mysql.sock
#character config
symbolic-links=0
character_set_server=utf8mb4
bind-address=0.0.0.0
port=3306
user=mysql
basedir=/usr/mysql/mysql-5.7.35
log-error=/usr/mysql/mysql-5.7.35/data/mysql-err.log
pid-file=/usr/mysql/mysql-5.7.35/data/mysql.pid
#####################安装 libaio
yum install -y libaio
###################################初始化数据库
# 进入bin目录
cd /usr/mysql/mysql-5.7.35/bin
# 初始化数据库
./mysqld --initialize --user=mysql --basedir=/usr/mysql/mysql-5.7.35 --datadir=/usr/mysql/mysql-5.7.35/data
# /usr/mysql/mysql-5.7.35/data/mysql-err.log  日志中，“root@localhost:”后面紧跟的部分就是初始密码
##########################将启动脚本放到初始化目录
cp /usr/mysql/mysql-5.7.35/support-files/mysql.server /etc/init.d/mysql
############################启动mysql服务
service mysql start
#启动mysql报错mysqld_safe error: log-error set to /var/log/mariadb/mariadb.log
mkdir /var/log/mariadb
touch /var/log/mariadb/mariadb.log
###### 用户组及用户
chown -R mysql:mysql /var/log/mariadb/
/usr/local/mysql/support-files/mysql.server start
#####################################配置软连接
ln -s /usr/mysql/mysql-5.7.35/bin/mysql /usr/bin
##########################修改mysql初始密码
#方法一.
#进入 /usr/mysql/mysql-5.7.35/ 目录下
cd /usr/mysql/mysql-5.7.35/
./bin/mysql -u root -p
#然后输入初始化密码登录，登录成功后修改密码，修改完成并刷新
 set password=password('123456');
 grant all privileges on *.* to root@'%' identified by '123456';
 flush privileges;
#方法二.
#忘记密码，跳过权限验证，免密登录修改密码
#先找到配置文件，在 [mysqld] 下面添加一句配置，跳过数据库权限验证
vim /etc/my.cnf
skip-grant-tables
#重启mysql
service mysql restart
#进入mysql, 在/usr/mysql/mysql-5.7.35/bin目录下执行
mysql -u root -p
#进入mysql后，修改密码
#这里就得注意一下了，从5.7版本开始，mysql就取消了password 这个字段，改成 authentication_string
# 进入mysql库
 use mysql
# mysql 5.7版本之前的用这种方式修改密码
 update user set password=password("123456") where user='root';
# mysql 5.7版本及以后版本的用这种方式修改密码
 update user set authentication_string=password("123456") where user='root';
# 刷新一下
 flush privileges;
############################重启MySQL
service mysql restart
#################################安装及使用过程中遇到的问题
#问题一. 当本地使用Navicat连接数据库是，出现下面这种报错，是因为mysql配置了不支持远程
Host XXX is not allowed to connect to this MySQL server
登录root用户，进入mysql, 在 /usr/mysql/mysql-5.7.35/bin 目录下执行
mysql -u root -p
再进入mysql库，并执行下面命令，发现当前主机配置信息host为localhost.
 use mysql
 select host from user where user='root';
将host设置为通配符%
 update user set host = '%' where user ='root';
#刷新一下，成功！
 flush privileges;
#################################删除卸载MySQL
#在linux控制台根目录输入下面命令，将输出的目录文件删除
find / -name mysql
sudo find / -name mysql*
whicher mysql
```

### 关于 my.cnf 配置详解

```sh
[client]
#客户端设置
port    = 3306
socket    = /data/mysql/data/mysql.sock
default-character-set = utf8mb4

[mysqld]
#mysql启动时使用的用户
user    = mysql
#默认连接端口
port    = 3306
#为MySQL客户端程序和服务器之间的本地通讯指定一个套接字文件
socket    = /data/mysql/data/mysql.sock
#数据库服务器id，这个id用来在主从服务器中标记唯一mysql服务器
server-id = 1
#端口绑定的ip地址，0.0.0.0表示允许所有远程访问，135.0.0.1表示只能本机访问，默认值为*
bind-address = 0.0.0.0
#默认名为 主机名.pid,在数据库/mysql/data/主机名.pid,记录mysql运行的process id
#如果存在，再次start时会报已经启动
pid-file = /data/mysql/data/mysql.pid

#安装目录
basedir    = /usr/local/mysql
#数据库存放目录
datadir    = /data/mysql/data/

#系统数据库编码设置，排序规则
character_set_server = utf8mb4
collation_server = utf8mb4_bin

#secure_auth 为了防止低版本的MySQL客户端(<4.1)使用旧的密码认证方式访问高版本的服务器。MySQL 5.6.7开始secure_auth 默认为启用值1
secure_auth = 1

#可能的连接数
#指出在MySQL暂时停止响应新请求之前的短时间内多少个请求可以被存在堆栈中。
back_log = 1024

#########################################
#################其他设置################
#########################################

#显式指定默认时间戳，即定义表中的timestamp时间戳的列时需要显示指定默认值
#默认为OFF，
#如果第一个TIMESTAMP列，没有显式设置DEFAULT，将自动分配DEFAULT CURRENT_TIMESTAMP和ON UPDATE CURRENT_TIMESTAMP属性
#timestamp列不能设置为NULL,第二列及以后的timestamp列都默认为"0000-00-00 00:00:00"
#如果设置为ON，
#第一个timestamp列可以设置为NULL，不会默认分配DEFAULT CURRENT_TIMESTAMP和ON UPDATE CURRENT_TIMESTAMP属性
#声明为NOT NULL且没有显式DEFAULT子句，在严格模式下会报错。
explicit_defaults_for_timestamp = ON

#linux下要严格区分大小写，windows下不区分大小写
#1表示不区分大小写，0表示区分大小写。
#lower_case_table_names = 0
lower_case_table_names = 0

#默认sql模式，严格模式
#sql_mode = ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,
#NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
#ONLY_FULL_GROUP_BY
#NO_ZERO_IN_DATE 不允许年月为0
#NO_ZERO_DATE 不允许插入年月为0的日期
#ERROR_FOR_DIVISION_BY_ZERO 在INSERT或UPDATE过程中，如果数据被零除，则产生错误而非警告。如 果未给出该模式，那么数据被零除时MySQL返回NULL
#NO_ENGINE_SUBSTITUTION 不使用默认的存储引擎替代
sql_mode = STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION


########################################################
############各种缓冲区及处理数据的最大值设置############
########################################################

#是MySQL执行排序使用的缓冲大小。如果想要增加ORDER BY的速度，首先看是否可以让MySQL使用索引而不是额外的排序阶段
#如果不能，可以尝试增加sort_buffer_size变量的大小
sort_buffer_size = 16M

#应用程序经常会出现一些两表（或多表）Join的操作需求，MySQL在完成某些 Join 需求的时候（all/index join），
#为了减少参与Join的“被驱动表”的读取次数以提高性能，需要使用到 Join Buffer 来协助完成 Join操作。
#当 Join Buffer 太小，MySQL 不会将该 Buffer 存入磁盘文件，而是先将Join Buffer中的结果集与需要 Join 的表进行 Join 操作
#然后清空 Join Buffer 中的数据，继续将剩余的结果集写入此 Buffer 中，
#如此往复。这势必会造成被驱动表需要被多次读取，成倍增加 IO 访问，降低效率。
#若果多表连接需求大，则这个值要设置大一点。
join_buffer_size = 16M

#索引块的缓冲区大默认16M
key_buffer_size = 15M
# 消息缓冲区会用到该列，该值太小则会在处理大包时产生错误。如果使用大的text,BLOB列，必须增加该值
max_allowed_packet = 32M

# mysql服务器最大连接数值的设置范围比较理想的是：服务器响应的最大连接数值占服务器上限连接数值的比例值在10%以上
# Max_used_connections / max_connections * 100%
max_connections = 512
# 阻止过多尝试失败的客户端，如果值为10时，失败（如密码错误）10次，mysql会无条件阻止用户连接
max_connect_errors = 1000000

#表描述符缓存大小，可减少文件打开/关闭次数,一般max_connections*2。
table_open_cache = 1024
#MySQL 缓存 table 句柄的分区的个数,每个cache_instance<=table_open_cache/table_open_cache_instances
table_open_cache_instances = 32
#mysql打开最大文件数
open_files_limit = 65535


#InnoDB表中，表更新后，查询缓存失效，事务操作提交之前，所有查询都无法使用缓存。影响查询缓存命中率
#查询缓存是靠一个全局锁操作保护的，如果查询缓存配置的内存比较大且里面存放了大量的查询结果，
#当查询缓存失效的时候，会长时间的持有这个全局锁。
#因为查询缓存的命中检测操作以及缓存失效检测也都依赖这个全局锁，所以可能会导致系统僵死的情况
#在高并发，写入量大的系统，建义把该功能禁掉
query_cache_size = 0
#决定是否缓存查询结果。这个变量有三个取值：0,1,2，分别代表了off、on、demand。
query_cache_type = 0
#指定单个查询能够使用的缓冲区大小，缺省为1M
query_cache_limit = 1M


##############################################
#################线程相关配置#################
##############################################

#线程缓存；主要用来存放每一个线程自身的标识信息，线程栈大小
thread_stack = 256K

#thread_cahe_size线程池，线程缓存。用来缓存空闲的线程，以至于不被销毁，
#如果线程缓存在的空闲线程，需要重新建立新连接，则会优先调用线程池中的缓存，很快就能响应连接请求。
#每建立一个连接，都需要一个线程与之匹配。
thread_cache_size = 384

#External-locking用于多进程条件下为MyISAM数据表进行锁定
#服务器访问数据表时经常需要等待解锁,因此在单服务器环境下external locking开启会让MySQL性能下降

#单服务器环境,使用skip-external-locking，关闭外部锁定，
#多服务器使用同一个数据库目录时，必须开启external-locking,也就是说注释掉skip-external-locking
skip-external-locking


#最大的空闲等待时间，默认是28800，单位秒，即8个小时
#通过mysql客户端连接数据库是交互式连接，通过jdbc连接数据库是非交互式连接
#交互式连接超时时间，超过这个时间自动断开连接
interactive_timeout = 600
#非交互式连接超时时间，超过这个时间自动断开连接
wait_timeout = 600

#它规定了内部内存临时表的最大值，每个线程都要分配。（实际起限制作用的是tmp_table_size和max_heap_table_size的最小值。）
#如果内存临时表超出了限制，MySQL就会自动地把它转化为基于磁盘的MyISAM表，存储在指定的tmpdir目录下
tmp_table_size = 96M
max_heap_table_size = 96M


############################
##########日志设置##########
############################

# 日志时间戳，mysql5.7.2版本之后才有的属性，控制写入到文件上显示日志的时间，
# 不会影响general log 和 slow log 写到表(mysql.general_log, mysql.slow_log)中的日志的时间
# 可以设置的有：UTC 和 SYSTEM，默认UTC，即0时区的时间，比北京时间慢8小时，所以要设置为SYSTEM
log_timestamps = SYSTEM

#日志的输出位置一般有三种方式：file(文件)，table(表)，none(不保存)
#其中前两个输出位置可以同时定义，none表示是开启日志功能但是不记录日志信息。
#file就是通过general_log_file=/mydata/data/general.log 等方式定义的，
#而输出位置定义为表时查看日志的内容：mysql.general_log表

##二进制日志设置
#默认不开启二进制日志
log_bin = OFF
#log-bin = /data/mysqldata/3307/binlog/mysql-bin 设置二进制路径时，如果没有生命log_bin=OFF，会开启日志
#二进制日志缓冲大小
#我们知道InnoDB存储引擎是支持事务的，实现事务需要依赖于日志技术，为了性能，日志编码采用二进制格式。那么，我们如何记日志呢？有日志的时候，就直接写磁盘？
#可是磁盘的效率是很低的，如果你用过Nginx，，一般Nginx输出access log都是要缓冲输出的。因此，记录二进制日志的时候，我们是否也需要考虑Cache呢？
#答案是肯定的，但是Cache不是直接持久化，于是面临安全性的问题——因为系统宕机时，Cache中可能有残余的数据没来得及写入磁盘。因此，Cache要权衡，要恰到好处：
#既减少磁盘I/O，满足性能要求；又保证Cache无残留，及时持久化，满足安全要求。
binlog_cache_size = 16M


##慢查询，开发调式阶段才需要开启慢日志功能。上线后关闭
slow_query_log = OFF
#慢日志文件路径
slow_query_log_file = /data/mysql/logs/slow_query.log
#该值是ON，则会记录所有没有利用索引来进行查询的语句，前提是slow_query_log 的值也是ON
log_queries_not_using_indexes = ON
#记录管理语句
log-slow-admin-statements
#如果运行的SQL语句没有使用索引，
#则mysql数据库同样会将这条SQL语句记录到慢查询日志文件中。调试时候使用
#log-queries-not-using-indexes
#设定每分钟记录到日志的未使用索引的语句数目，超过这个数目后只记录语句数量和花费的总时间
#log_throttle_queries_not_using_indexes = 60

#MySQL能够记录执行时间超过参数 long_query_time 设置值的SQL语句，默认是不记录的。超过这个时间的sql语句会被记录到慢日志文件中
long_query_time = 2

##错误日志：记录启动，运行，停止mysql时出现的信息
log-error = /data/mysql/logs/error.log

##一般查询日志,记录建立的客户端连接用户的所有操作，增上改查等，
#不是为了调式数据库，建议不要开启，0关闭，1开启
general_log = OFF
general_log_file = /data/mysql/logs/general.log

#log-long-format 扩展方式记录有关事件
#它是记录激活的更新日志、二进制更新日志、和慢查询日志的大量信息。例如，所有查询的用户名和时间戳将记录下来
#log-short-format,相反，记录少量的信息



############################
######数据库存储引擎########
############################

#默认使用InnoDB存储引擎
default_storage_engine = InnoDB

############################
######innoDB setting########
############################

#控制打开.ibd文件的数量。
#如果未启用innodb_file_per_table，则默认值为300
#否则取决于300和innodb_open_files中的较大值
innodb_file_per_table = 1
innodb_open_files = 350
#表定义缓存(数据字典)数量400-2000,默认为400 + (table_open_cache / 2)，小网站可以设置为最低
table_definition_cache = 400
#InnoDB 用来高速缓冲数据和索引内存缓冲大小。更大的设置可以使访问数据时减少磁盘 I/O。
innodb_buffer_pool_size = 64M

#单独指定数据文件的路径与大小
#默认会在datadir目录下创建ibdata1，表空间tablespace
#如果想为innodb tablespace指定不同目录下的文件，必须指定innodb_data_home_dir，home目录
innodb_data_file_path = ibdata1:32M:autoextend
#对于多核的CPU机器，可以修改innodb_read_io_threads和innodb_write_io_threads来增加IO线程，来充分利用多核的性能。默认4
#innodb_write_io_threads = 4
#innodb_read_io_threads = 4

#并发线程数的限制值，表示默认0情况下不限制线程并发执行的数量
innodb_thread_concurrency = 0
#开始碎片回收线程。这个应该能让碎片回收得更及时而且不影响其他线程的操作，
#默认值1表示innodb的purge操作被分离到purge线程中，master thread不再做purge操作。
#innodb_purge_threads = 1

#配置MySql日志何时写入硬盘的参数，默认为1
#0：log buffer将每秒一次地写入log file中，并且log file的flush(刷到磁盘)操作同时进行。该模式下在事务提交的时候，不会主动触发写入磁盘的操作。
#1：每次事务提交时MySQL都会把log buffer的数据写入log file，并且flush(刷到磁盘)中去
#2：每次事务提交时mysql都会把log buffer的数据写入log file，但是flush(刷到磁盘)操作并不会同时进行。该模式下，MySQL会每秒执行一次 flush(刷到磁盘)操作
#通常设置为 1，意味着在事务提交前日志已被写入磁盘， 事务可以运行更长以及服务崩溃后的修复能力。
innodb_flush_log_at_trx_commit = 1

#InnoDB 将日志写入日志磁盘文件前的缓冲大小。理想值为 1M 至 8M。大的日志缓冲允许事务运行时不需要将日志保存入磁盘而只到事务被提交(commit)。
#因此，如果有大的事务处理，设置大的日志缓冲可以减少磁盘I/O。
innodb_log_buffer_size = 2M
#日志组中的每个日志文件的大小(单位 MB)。如果 n 是日志组中日志文件的数目，那么理想的数值为 1M 至下面设置的缓冲池(buffer pool)大小的 1/n。较大的值，
#可以减少刷新缓冲池的次数，从而减少磁盘 I/O。但是大的日志文件意味着在崩溃时需要更长的时间来恢复数据。
innodb_log_file_size = 128M
#指定有三个日志组
innodb_log_files_in_group = 3
#innodb_max_dirty_pages_pct作用：控制Innodb的脏页在缓冲中在那个百分比之下，值在范围1-100,默认为90.这个参数的另一个用处：
#当Innodb的内存分配过大，致使swap占用严重时，可以适当的减小调整这个值，使达到swap空间释放出来。建义：这个值最大在90%，最小在15%。
#太大，缓存中每次更新需要致换数据页太多，太小，放的数据页太小，更新操作太慢。
innodb_max_dirty_pages_pct = 75
#在回滚(rooled back)之前，InnoDB 事务将等待超时的时间(单位 秒)
innodb_lock_wait_timeout = 120

#Innodb Plugin引擎开始引入多种格式的行存储机制，目前支持：Antelope、Barracuda两种。其中Barracuda兼容Antelope格式。
#innodb_file_format = Barracuda
#限制Innodb能打开的表的数量
#innodb_open_files = 65536


#分布式事务
#innodb_support_xa = FALSE

#innodb_buffer_pool_size 一致 可以开启多个内存缓冲池，把需要缓冲的数据hash到不同的缓冲池中，这样可以并行的内存读写。
#innodb_buffer_pool_instances = 4
#这个参数据控制Innodb checkpoint时的IO能力
#innodb_io_capacity = 500
#作用：使每个Innodb的表，有自已独立的表空间。如删除文件后可以回收那部分空间。
#分配原则：只有使用不使用。但ＤＢ还需要有一个公共的表空间。
#innodb_file_per_table = 1

#当更新/插入的非聚集索引的数据所对应的页不在内存中时（对非聚集索引的更新操作通常会带来随机IO），会将其放到一个insert buffer中，
#当随后页面被读到内存中时，会将这些变化的记录merge到页中。当服务器比较空闲时，后台线程也会做merge操作
#innodb_change_buffering = inserts
#该值影响每秒刷新脏页的操作，开启此配置后，刷新脏页会通过判断产生重做日志的速度来判断最合适的刷新脏页的数量；
#innodb_adaptive_flushing = 1

#数据库事务隔离级别 ，读取提交内容
#transaction-isolation = READ-COMMITTED

#innodb_flush_method这个参数控制着innodb数据文件及redo log的打开、刷写模式
#InnoDB使用O_DIRECT模式打开数据文件，用fsync()函数去更新日志和数据文件。
#innodb_flush_method = O_DIRECT
#默认设置值为1.设置为0：表示Innodb使用自带的内存分配程序；设置为1：表示InnoDB使用操作系统的内存分配程序　　　　　
#innodb_use_sys_malloc = 1

############################
######myisam setting########
############################
bulk_insert_buffer_size = 8M
myisam_sort_buffer_size = 8M
# MySQL重建索引时所允许的最大临时文件的大小
myisam_max_sort_file_size = 10G
myisam_repair_threads = 1
#忽略表名大小写
lower_case_table_names=1

#数据库全量备份
[mysqldump]
#强制mysqldump从服务器一次一行地检索表中的行
quick
#可接收数据包大小
max_allowed_packet = 16M

#在mysqld服务器不使用的情况下修复表或在崩溃状态下恢复表
[myisamchk]
key_buffer_size = 8M
sort_buffer_size = 8M
read_buffer = 4M
write_buffer = 4M

# https://lnmp.org/  linux nginx mysql php一键安装配置
```

# 三范式

​ **第一范式**：无重复的列。当关系模式 R 的所有属性都不能在分解为更基本的数据单位时，称 R 是满足第一
范式的，简记为 1NF。满足第一范式是关系模式规范化的最低要求，否则，将有很多基本操作在这样的
关系模式中实现不了。
​ **第二范式**：属性完全依赖于主键 [ 消除部分子函数依赖 ]。如果关系模式 R 满足第一范式，并且 R 得所有
非主属性都完全依赖于 R 的每一个候选关键属性，称 R 满足第二范式，简记为 2NF。第二范式（2NF）是
在第一范式（1NF）的基础上建立起来的，即满足第二范式（2NF）必须先满足第一范式（1NF）。第
二范式（2NF）要求数据库表中的每个实例或行必须可以被唯一地区分。为实现区分通常需要为表加上
一个列，以存储各个实例的唯一标识。这个唯一属性列被称为主关键字或主键、主码。
​ **第三范式**：属性不依赖于其它非主属性 [ 消除传递依赖 ]。设 R 是一个满足第一范式条件的关系模式，X
是 R 的任意属性集，如果 X 非传递依赖于 R 的任意一个候选关键字，称 R 满足第三范式，简记为 3NF.
满足第三范式（3NF）必须先满足第二范式（2NF）。第三范式（3NF）要求一个数据库表中不包含已在其它表中已包含的非主关键字信息。
注：关系实质上是一张二维表，其中每一行是一个元组，每一列是一个属性
第二范式（2NF）和第三范式（3NF）的概念很容易混淆，区分它们的关键点在于，2NF：非主键列是
否完全依赖于主键，还是依赖于主键的一部分；3NF：非主键列是直接依赖于主键，还是直接依赖于非
主键列。

# SQL 语句注意事项

1. 以 `;` 结尾
2. 不区分大小写
3. 注释
   1. `--` 单行注释,后面至少跟一个空格.
   2. `#` 单行注释.
   3. `/* ... */` 多行注释
4. 使用空格 在处理 SQL 语句时，其中所有空格都被忽略。SQL 语句可以在一行上给出，也可以分成许多行。多数 SQL 开发人员认为将 SQL 语句分成多行更容易阅读和调试。

# 全局配置

```sql
show global variables like 'port'; -- 查看端口
```

# 导出与导入

```shell
# 导出
mysqldump --set-charset  -u root -p e_contract2 >  C:\Users\liuxiaohui\Desktop\20220818.sql
# 导入
source
mysqlhotcopy
```

# 主从数据库

1. 一主多从,读写分离,写主读从
   1. 检查数据库,要求版本一致,日志为二进制日志
      1. `vi /etc/my.cnf`修改配置,`log-bin=mysql-bin`二进制日志,`server-id=222`服务器默认 id
      2. 重启使配置生效
   2. 主库配置:
      1. 创建从库账号并授权 slave`  GRANT REPLICATION SLAVE ON *.* to 'mysync'@'%' identified by 'q123456';`
      2. 查看主库状态`show master status;`
   3. 配置从库:
      1. `change master to master_host='192.168.145.222',master_user='mysync',master_password='q123456',         master_log_file='mysql-bin.000004',master_log_pos=308;   -- 注意不要断开，308数字前后无单引号`
      2. 启动从库复制:`start slave;`
      3. 检查状态`  show slave status\G`,Slave_IO 及 Slave_SQL 进程必须正常运行，即 YES 状态，否则都是错误的状态(如：其中一个 NO 均属错误)。
      4. 监听 Slave_IO 及 Slave_SQL 是否一直都是 yes
2. 主主数据库,数据相互同步

# 优化

MySQL 优化可以从以下几个方面入手：

1. 索引优化：对于经常用于查询和连接的列，可以创建索引来提高查询效率。同时，可以使用 explain 命令查看 SQL 执行计划，评估索引使用情况和效果。

2. SQL 优化：优化 SQL 语句可以减少数据库服务器的负担，提高查询效率。可以通过减少不必要的查询、避免在查询中使用通配符、使用优化的 JOIN 语句等方式进行优化。

3. 硬件优化：增加内存、优化磁盘、增加 CPU 等硬件优化可以提高 MySQL 的性能。

4. MySQL 配置优化：MySQL 的配置也会影响其性能。可以通过修改配置参数来提高 MySQL 的性能，如修改缓存大小、修改连接数等。

5. 数据库设计优化：优化数据库设计可以提高 MySQL 的性能，如避免使用过多的表连接、规范表结构、合理使用数据类型等。

6. 数据库分区：对于大型数据库，可以使用分区技术将数据分散到多个表中，提高查询效率。

7. 定期清理无用数据：清理无用数据可以释放空间，减少数据库负担，提高性能。

需要注意的是，在进行 MySQL 优化时，需要根据具体情况进行优化，不能简单地套用优化方法，否则可能会出现意想不到的问题。同时，定期进行优化也是非常必要的，以保持 MySQL 的最佳性能状态。

