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
mysql> set password=password('123456');
mysql> grant all privileges on *.* to root@'%' identified by '123456';
mysql> flush privileges;
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
mysql> use mysql
# mysql 5.7版本之前的用这种方式修改密码
mysql> update user set password=password("123456") where user='root';
# mysql 5.7版本及以后版本的用这种方式修改密码
mysql> update user set authentication_string=password("123456") where user='root';
# 刷新一下
mysql> flush privileges;
############################重启MySQL
service mysql restart
#################################安装及使用过程中遇到的问题
#问题一. 当本地使用Navicat连接数据库是，出现下面这种报错，是因为mysql配置了不支持远程
Host XXX is not allowed to connect to this MySQL server
登录root用户，进入mysql, 在 /usr/mysql/mysql-5.7.35/bin 目录下执行
mysql -u root -p
再进入mysql库，并执行下面命令，发现当前主机配置信息host为localhost.
mysql> use mysql
mysql> select host from user where user='root';
将host设置为通配符%
mysql> update user set host = '%' where user ='root';
#刷新一下，成功！
mysql> flush privileges;
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

# DDL 操作数据库

- **DDL（Data Definition Language）**：数据定义语言，用来定义数据库对象：库、表、列等。

```sql
  alter user 'root'@'localhost' identified with mysql_native_password BY '新密码';
  alter user 'root'@'localhost' identified with mysql_native_password BY '123456';
```

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
3. `SHOW STATUS`，用于显示广泛的服务器状态信息；
4. `SHOW CREATE DATABASE`和`SHOW CREATE TABLE`，分别用来显示创建特定数据库或表的 MySQL 语句；
5. `SHOW GRANTS`，用来显示授予用户（所有用户或特定用户）的安全权限；
6. `SHOW ERRORS`和`SHOW WARNINGS`，用来显示服务器错误或警告消息。
7. 显示帮助 `help show`
8. `show character set;` 显示所有可用字符\描述\默认校对
9. `show collation;` 显示所有可用校对

## 修改 alter

1. 修改数据库的字符集

```sql
alter database 数据库名 character set 编码方式
ALTER DATABASE mydb2 character SET utf8;
```

## 删除 drop

```sql
drop database 数据库名;
```

## 切换 use

1. 查看当前使用的数据库`select database();`
2. 切换数据库 `use 数据库名;`
3. 如果不使用 use,可以使用限定符`.`访问某个数据库的表\列,例:`数据库.表.列`

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

# DCL 操作用户权限

- **DCL（Data Control Language）**：数据控制语言，用来定义访问权限和安全级别。

## 1. 创建用户

```sql
create user 用户名@指定ip identified by 密码;

create user 用户名@客户端ip identified by 密码; #指定IP才能登陆

create user 用户名@‘% ’ identified by 密码; #任意IP均可登陆
```

## 2. 用户授权

```sql
grant 权限1,权限2,........,权限n on 数据库名.* to 用户名@IP; #给指定用户授予指定指定数据库指定权限

grant all on . to 用户名@IP; 给指定用户授予所有数据库所有权限


```

> 权限 ： select,insert,update,delete,create
>
> 数据库名.\*：该数据库的所有表

## 3. 用户权限查询

```sql
show grants for 用户名@IP;
```

## 4. 撤销用户权限

```sql
revoke 权限1,权限2,........,权限n on 数据库名.* from 用户名@IP;
```

## 5. 删除用户

```sql
drop user 用户名@IP;
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

# JSON

## 插入

插入时:相当于插入字符串

```sql
insert into dept VALUES(1,'部门1','{"deptName": "部门1", "deptId": "1", "deptLeaderId": "3"}');
```

## 一般查询

使用 `json字段名->'$.json属性'` 进行查询条件

```sql
SELECT * from dept WHERE json_value->'$.deptLeaderId'='5';
SELECT * from dept WHERE json_value->'$.deptLeaderId'='5' and dept='部门3';
SELECT * from dept WHERE json_value->'$.deptLeaderId'='5' and json_value->'$.deptId'='5';
/* 多表联查 */
SELECT * from dept,dept_leader WHERE dept.json_value->'$.deptLeaderId'=dept_leader.json_value->'$.id' ;
```

## 函数查询

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

# 运算符

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

# 常用数据类型

- int：整型
- double：浮点型，例如 double(5,2)表示最多 5 位，其中必须有 2 位小数，即最大值为
  999.99；默认支持四舍五入
- char：固定长度字符串类型； char(10) 'aaa ' 占 10 位
- varchar：可变长度字符串类型； varchar(10) 'aaa' 占 3 位
- text：字符串类型，比如小说信息；
- blob：字节类型，保存文件信息(视频，音频，图片)；
- date：日期类型，格式为：yyyy-MM-dd；
- time：时间类型，格式为：hh:mm:ss
- timestamp：时间戳类型 yyyy-MM-dd hh:mm:ss 会自动赋值
- datetime:日期时间类型 yyyy-MM-dd hh:mm:ss

> 日期类型值的区别:
> date：yyyy-MM-dd （年月日）
> time：hh:mm:ss (时分秒)
> datetime:yyyy-MM-dd hh:mm:ss (年月日时分秒)
> 获取当前系统时间:now()

数值类型
| 类型 | 大小 | 范围（有符号） | 范围（无符号 ） | 用途 |
| --------- | ------ | ------------------------------- | ------------------ | -------- |
| tinyint | 1 字节 | (-128，127) | (0，255) | 小整数值 |
| smallint | 2 字节 | (-32 768，32 767) | (0，65 535) | 大整数值 |
| mediumint | 3 字节 | (-8 388 608，8 388 607) | (0，16 777 215) | 大整数值 |
| INT | 4 字节 | (-2 147 483 648，2 147 483 647) | (0，4 294 967 295) | 大整数值 |
| bigint | 8 字节 | (-9 233 372 036 854 775 808，9 223 372 036 854 775 807) | (0，18 446 744
073 709 551 615) | 极大整数值 |
| float | 4 字节 | (-3.402 823 466 E+38，-1.175 494 351 E-38)，0，(1.175 494 351 E-38，3.402
823 466 351 E+38) | 0，(1.175 494 351 E-38，3.402 823 466 E+38) | 单精度浮点数值 |
| double | 8 字节 | (-1.797 693 134 862 315 7 E+308，-2.225 073 858 507 201 4 E-308)，0，
(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) | 0，(2.225 073 858 507 201 4
E-308，1.797 693 134 862 315 7 E+308) | 双精度浮点数值 |
日期类型:
表示时间值的日期和时间类型为 DATETIME、DATE、TIMESTAMP、TIME 和 YEAR。
每个时间类型有一个有效值范围和一个"零"值，当指定不合法的 MySQL 不能表示的值时使用"零"值。
TIMESTAMP 类型有专有的自动更新特性

| 类型                                                         | 大小(字节)      | 范围                                                                      | 格式                | 用途             |
| ------------------------------------------------------------ | --------------- | ------------------------------------------------------------------------- | ------------------- | ---------------- |
| DATE                                                         | 3               | 1000-01-01/9999-12-31                                                     | YYYY-MM-DD          | 日期值           |
| TIME                                                         | 3               | '-838:59:59'/'838:59:59'                                                  | HH:MM:SS            | 时间值或持续时间 |
| YEAR                                                         | 1               | 1901/2155                                                                 | YYYY                | 年份值           |
| DATETIME                                                     | 8               | 1000-01-01 00:00:00/9999-12-31 23:59:59                                   | YYYY-MM-DD HH:MM:SS | 混合日           |
| 期和时间值                                                   |
| TIMESTAMP                                                    | 4               | 1970-01-01 00:00:00/2038 结束时间是第 _2147483647_ 秒，北京时间 \*2038-1- |
| 19 11:14:07\*，格林尼治时间 2038 年 1 月 19 日 凌晨 03:14:07 | YYYYMMDD HHMMSS | 混合日期和                                                                |

时间值，时间戳 ,当更新数据的时候自动添加更新时间
字符串类型:
字符串类型指 CHAR、VARCHAR、BINARY、VARBINARY、BLOB、TEXT、ENUM 和 SET
| 类型 | 大小 | 用途 |
| ---------- | ------------------- | ------------------------------- |
| CHAR | 0-255 字节 | 定长字符串 |
| VARCHAR | 0-65535 字节 | 变长字符串 |
| TINYBLOB | 0-255 字节 | 不超过 255 个字符的二进制字符串 |
| TINYTEXT | 0-255 字节 | 短文本字符串 |
| BLOB | 0-65 535 字节 | 二进制形式的长文本数据 |
| TEXT | 0-65 535 字节 | 长文本数据 |
| MEDIUMBLOB | 0-16 777 215 字节 | 二进制形式的中等长度文本数据 |
| MEDIUMTEXT | 0-16 777 215 字节 | 中等长度文本数据 |
| LONGBLOB | 0-4 294 967 295 字节 | 二进制形式的极大文本数据 |
| LONGTEXT | 0-4 294 967 295 字节 | 极大文本数据 |
CHAR 和 VARCHAR 类型类似，但它们保存和检索的方式不同。它们的最大长度和是否尾部空格被保留等
方面也不同。在存储或检索过程中不进行大小写转换。
BINARY 和 VARBINARY 类类似于 CHAR 和 VARCHAR，不同的是它们包含二进制字符串而不要非二进制字
符串。也就是说，它们包含字节字符串而不是字符字符串。这说明它们没有字符集，并且排序和比较基
于列值字节的数值值。
BLOB 是一个二进制大对象，可以容纳可变数量的数据。有 4 种 BLOB 类型：TINYBLOB、BLOB、
MEDIUMBLOB 和 LONGBLOB。它们只是可容纳值的最大长度不同。
有 4 种 TEXT 类型：TINYTEXT、TEXT、MEDIUMTEXT 和 LONGTEXT。这些对应 4 种 BLOB 类型，有相同的
最大长度和存储需求。

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

# 事务

![1635733145176](..\photo\mysql事务.png)

## 1. 语法

1. `start transaction;` `begin;`
2. `commit;` 使得当前的修改确认
3. `rollback; `使得当前的修改被放弃
4. `savepoint <xxx>` 保留点
   1. `rollback to xxx` 回退到保留点
   2. 保留点在事务提交或回退后自动释放,也可以通过`release savepoint` 手动释放
5. `set autocommit=0` 取消自动提交,为 1 是开启

## 2. 事务的 ACID 特性

1. 原⼦性（Atomicity）
   事务的原⼦性是指事务必须是⼀个原子的操作序列单元。事务中包含的各项操作在⼀次执⾏过程中，只
   允许出现两种状态之一。
   （1）全部执行成功
   （2）全部执行失败
   事务开始后所有操作，要么全部做完，要么全部不做，不可能停滞在中间环节。事务执⾏过程中出错，
   会回滚到事务开始前的状态，所有的操作就像没有发⽣一样。也就是说事务是⼀个不可分割的整体，就
   像化学中学过的原子，是物质构成的基本单位。
2. ⼀致性（Consistency）
   事务的一致性是指事务的执⾏不能破坏数据库数据的完整性和一致性，一个事务在执⾏之前和执行之
   后，数据库都必须处以⼀致性状态。
   比如：如果从 A 账户转账到 B 账户，不可能因为 A 账户扣了钱，⽽ B 账户没有加钱。
3. 隔离性（Isolation）
   事务的隔离性是指在并发环境中，并发的事务是互相隔离的。也就是说，不同的事务并发操作相同的数
   据时，每个事务都有各自完整的数据空间。
   ⼀个事务内部的操作及使用的数据对其它并发事务是隔离的，并发执行的各个事务是不能互相干扰的。
   隔离性分 4 个级别，下面会介绍。
4. 持久性（Duration）
   事务的持久性是指事务⼀旦提交后，数据库中的数据必须被永久的保存下来。即使服务器系统崩溃或服
   务器宕机等故障。只要数据库重新启动，那么一定能够将其恢复到事务成功结束后的状态

## 3. 事务的并发问题

- **脏读**：读取到了没有提交的数据, 事务 A 读取了事务 B 更新的数据，然后 B 回滚操作，那么 A 读取到的 数据是脏数据。
- **不可重复读**：同⼀条命令返回不同的结果集（更新）.事务 A 多次读取同一数据，事务 B 在事务 A 多次读取的过程中，对数据做了更新并提交，导致事务 A 多次读取同一数据时，结果不一致。
- **幻读**：重复查询的过程中，数据就发⽣了量的变化（insert， delete）。

## 4. 事务的隔离等级

​ 4 种事务隔离级别从上往下，级别越高，并发性越差，安全性就越来越高。 ⼀般数据默认级别是 读以提交或可重复读。

```sql
# 查看当前会话中事务的隔离级别
select @@tx_isolation;
# 设置当前会话中的事务隔离级别
set session transaction isolation level read uncommitted;
```

1. 读未提交（READ_UNCOMMITTED）
   读未提交，该隔离级别允许脏读取，其隔离级别是最低的。换句话说，如果一个事务正在处理理某一数
   据，并对其进⾏了更新，但同时尚未完成事务，因此还没有提交事务；而以此同时，允许另一个事务也
   能够访问该数据。
2. 读已提交（READ_COMMITTED）
   读已提交是不同的事务执行的时候只能获取到已经提交的数据。 这样就不会出现上面的脏读的情况了。
   但是在同一个事务中执行同一个读取,结果不一致
   不可重复读示例
   可是解决了脏读问题，但是还是解决不了可重复读问题。
3. 可重复读（REPEATABLE_READ）
   可重复读就是保证在事务处理理过程中，多次读取同一个数据时，该数据的值和事务开始时刻是一致的。
   因此该事务级别限制了不可重复读和脏读，但是有可能出现幻读的数据。
   幻读
   幻读就是指同样的事务操作，在前后两个时间段内执行对同一个数据项的读取，可能出现不一致的结果。
   诡异的更新事件
4. 顺序读（SERIALIZABLE）
   顺序读是最严格的事务隔离级别。它要求所有的事务排队顺序执⾏行行，即事务只能一个接一个地处理，不
   能并发。

## 5. 不同的隔离级别的锁的情况(了解)

1. 读未提交（RU）: 有行级的锁，没有间隙锁。它与 RC 的区别是能够查询到未提交的数据。

2. 读已提交（RC）：有行级的锁，没有间隙锁，读不到没有提交的数据。

3. 可重复读（RR）：有行级的锁，也有间隙锁，每次读取的数据都是一样的，并且没有幻读的情况。

4. 序列化（S）：有行级锁，也有间隙锁，读表的时候，就已经上锁了

## 6. 隐式提交(了解)

DQL:查询语句句

DML:写操作(添加,删除,修改)

DDL:定义语句句(建库,建表,修改表,索引操作,存储过程,视图)

DCL:控制语⾔言(给⽤用户授权,或删除授权)

DDL（Data Define Language）：都是隐式提交。

隐式提交：执⾏行行这种语句句相当于执⾏行行 commit; DDL

# 视图

视图并不同于数据表，它们的区别在于以下几点：

- 视图不是数据库中真实的表，而是一张虚拟表，其结构和数据是建立在对数据中真实表的查询基础上的。
- 存储在数据库中的查询操作 SQL 语句定义了视图的内容，列数据和行数据来自于视图查询所引用的实际表，引用视图时动态生成这些数据。
- 视图没有实际的物理记录，不是以数据集的形式存储在数据库中的，它所对应的数据实际上是存储在视图所引用的真实表中的。
- 视图是数据的窗口，而表是内容。表是实际数据的存放单位，而视图只是以不同的显示方式展示数据，其数据来源还是实际表。
- 视图是查看数据表的一种方法，可以查询数据表中某些字段构成的数据，只是一些 SQL 语句的集合。
- 从安全的角度来看，视图的数据安全性更高，使用视图的用户不接触数据表，不知道表结构。
- 视图的建立和删除只影响视图本身，不影响对应的基本表。

优点：

1. 定制用户数据，聚焦特定的数据
2. 简化数据操作
3. 提高数据的安全性
4. 共享所需数据
5. 更改数据格式
6. 重用 SQL 语句

视图可以嵌套,但不能有索引,也不能有关联的触发器或默认值
性能问题: 视图因为只有结构而没有数据,因此,每次使用视图时,都会进行一次查询操作,如果视图比较复杂,性能下降的比较厉害

## 1. 创建

```sql
# 创建视图
CREATE VIEW <视图名> AS <SELECT语句> ;
/*
对于创建视图中的 SELECT 语句的指定存在以下限制：
用户除了拥有 CREATE VIEW 权限外，还具有操作中涉及的基础表和其他视图的相关权限。
SELECT 语句不能引用系统或用户变量。
SELECT 语句不能包含 FROM 子句中的子查询。
SELECT 语句不能引用预处理语句参数。
*/
```

> ​ 视图定义中引用的表或视图必须存在。但是，创建完视图后，可以删除定义引用的表或视图。可使用 CHECK TABLE 语句检查视图定义是否存在这类问题。
>
> ​ 视图定义中允许使用 ORDER BY 语句，但是若从特定视图进行选择，而该视图使用了自己的 ORDER BY 语句，则视图定义中的 ORDER BY 将被忽略。
>
> ​ 视图定义中不能引用 TEMPORARY 表（临时表），不能创建 TEMPORARY 视图。
>
> ​ WITH CHECK OPTION 的意思是，修改视图时，检查插入的数据是否符合 WHERE 设置的条件。

## 2. 查询

```sql
DESCRIBE 视图名;  -- 可简写为 desc;查询视图字段讯息
SHOW CREATE VIEW 视图名 \G; --  \G 格式化输出;查看视图的详细信息
SELECT * FROM information_schema.views; -- 查看所有视图;所有视图的定义都是存储在 information_schema 数据库下的 views 表中，也可以在这个表中查看所有视图的详细信息
select * from 视图名; -- 查看视图数据类似于表
```

## 3. 修改

```sql
ALTER VIEW <视图名> AS <SELECT语句>; -- 修改视图
```

某些视图是可更新的。也就是说，可以使用 UPDATE、DELETE 或 INSERT 等语句更新基本表的内容。对于可更新的视图，视图中的行和基本表的行之间必须具有一对一的关系。

还有一些特定的其他结构，这些结构会使得视图不可更新。更具体地讲，如果视图包含以下结构中的任何一种，它就是不可更新的：

- 聚合函数 SUM()、MIN()、MAX()、COUNT() 等。
- DISTINCT 关键字。
- GROUP BY 子句。
- HAVING 子句。
- UNION 或 UNION ALL 运算符。
- 位于选择列表中的子查询。
- FROM 子句中的不可更新视图或包含多个表。
- WHERE 子句中的子查询，引用 FROM 子句中的表。
- ALGORITHM 选项为 TEMPTABLE（使用临时表总会使视图成为不可更新的）的时候。

## 4. 删除

```sql
DROP VIEW <视图名1> [ , <视图名2> …];
```

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

# 存储过程

​ 存储过程是一组为了完成特定功能的 SQL 语句集合。使用存储过程的目的是将常用或复杂的工作预先用 SQL 语句写好并用一个指定名称存储起来，这个过程经编译和优化后存储在数据库服务器中，因此称为存储过程。当以后需要数据库提供与已定义好的存储过程的功能相同的服务时，只需调用“CALL 存储过程名字”即可自动完成。

​ 一个存储过程是一个可编程的函数，它在数据库中创建并保存，一般由 SQL 语句和一些特殊的控制结构组成。当希望在不同的应用程序或平台上执行相同的特定功能时，存储过程尤为合适。

## 1. 优点

**1) 封装性**

​ 通常完成一个逻辑功能需要多条 SQL 语句，而且各个语句之间很可能传递参数，所以，编写逻辑功能相对来说稍微复杂些，而存储过程可以把这些 SQL 语句包含到一个独立的单元中，使外界看不到复杂的 SQL 语句，只需要简单调用即可达到目的。并且数据库专业人员可以随时对存储过程进行修改，而不会影响到调用它的应用程序源代码。

**2) 可增强 SQL 语句的功能和灵活性**

​ 存储过程可以用流程控制语句编写，有很强的灵活性，可以完成复杂的判断和较复杂的运算。

**3) 可减少网络流量**

​ 由于存储过程是在服务器端运行的，且执行速度快，因此当客户计算机上调用该存储过程时，网络中传送的只是该调用语句，从而可降低网络负载。

**4) 高性能**

​ 当存储过程被成功编译后，就存储在数据库服务器里了，以后客户端可以直接调用，这样所有的 SQL 语句将从服务器执行，从而提高性能。但需要说明的是，存储过程不是越多越好，过多的使用存储过程反而影响系统性能。

**5) 提高数据库的安全性和数据的完整性**

​ 存储过程提高安全性的一个方案就是把它作为中间组件，存储过程里可以对某些表做相关操作，然后存储过程作为接口提供给外部程序。这样，外部程序无法直接操作数据库表，只能通过存储过程来操作对应的表，因此在一定程度上，安全性是可以得到提高的。

**6) 使数据独立**

​ 数据的独立可以达到解耦的效果，也就是说，程序可以调用存储过程，来替代执行多条的 SQL 语句。这种情况下，存储过程把数据同用户隔离开来，优点就是当数据表的结构改变时，调用表不用修改程序，只需要数据库管理者重新编写存储过程即可。

## 2. 创建

```sql
create procedure <过程名> ( [过程参数[,…] ] ) <过程体>

[过程参数[,…] ] 格式：
	[ IN | OUT | INOUT ] <参数名> <类型>


-- ------------------------------例:
delimiter // -- 使用//作为mysql分隔符,替代;
create procedure hello(
   out id int,
   in id2 int,
   inout id3 int
)
begin
select id  into id2 from hello where id = id2 or id=id3;
end //
delimiter ; -- 恢复 ;

-- 使用
call hello(@id,'id0000',@id3)  -- 所有MySQL变量都必须以@开始。
select @id;-- 查看结果
-- 删除
drop procedure  if exists hello;
```

## 3. 查看

```sql
# 查看存储过程的状态
SHOW PROCEDURE STATUS LIKE 存储过程名;
# 查看存储过程的定义
SHOW CREATE PROCEDURE 存储过程名;
```

​ SHOW STATUS 语句只能查看存储过程是操作的哪一个数据库、存储过程的名称、类型、谁定义的、创建和修改时间、字符编码等信息。但是，这个语句不能查询存储过程的集体定义，如果需要查看详细定义，需要使用 SHOW CREATE 语句。

​ 存储过程的信息都存储在 information_schema 数据库下的 Routines 表中，可以通过查询该表的记录来查询存储过程的信息，SQL 语句如下：

```sql
SELECT * FROM information_schema.Routines WHERE ROUTINE_NAME=存储过程名;
```

​ 在 information_schema 数据库下的 routines 表中，存储着所有存储过程的定义。所以，使用 SELECT 语句查询 routines 表中的存储过程和函数的定义时，一定要使用 routine_name 字段指定存储过程的名称，否则，将查询出所有的存储过程的定义。

## 4. 修改

MySQL 中修改存储过程的语法格式如下：

```sql
ALTER PROCEDURE 存储过程名 [ 特征 ... ]
/*
特征指定了存储过程的特性，可能的取值有：
	CONTAINS SQL 表示子程序包含 SQL 语句，但不包含读或写数据的语句。
	NO SQL 表示子程序中不包含 SQL 语句。
	READS SQL DATA 表示子程序中包含读数据的语句。
	MODIFIES SQL DATA 表示子程序中包含写数据的语句。
	SQL SECURITY { DEFINER |INVOKER } 指明谁有权限来执行。
	DEFINER 表示只有定义者自己才能够执行。
	INVOKER 表示调用者可以执行。
	COMMENT 'string' 表示注释信息。
*/
```

## 5. 删除

```sql
DROP PROCEDURE [ IF EXISTS ] <过程名> ;
# IF EXISTS：指定这个关键字，用于防止因删除不存在的存储过程而引发的错误。
```

## 6. 存储函数

```sql
# 创建
CREATE FUNCTION sp_name ([func_parameter[...]])
RETURNS type
[characteristic ...]
routine_body

/*
	sp_name 参数：表示存储函数的名称；
	func_parameter：表示存储函数的参数列表；
	RETURNS type：指定返回值的类型；
	characteristic 参数：指定存储函数的特性，该参数的取值与存储过程是一样的；
	routine_body 参数：表示 SQL 代码的内容，可以用 BEGIN...END 来标示 SQL 代码的开始和结束。

注意：在具体创建函数时，函数名不能与已经存在的函数名重名。除了上述要求外，推荐函数名命名（标识符）为 function_xxx 或者 func_xxx。
*/

eg:
mysql> DELIMITER //
mysql> CREATE FUNCTION func_student(id INT(11))
    -> RETURNS VARCHAR(20)
    -> COMMENT '查询某个学生的姓名'
    -> BEGIN
    -> RETURN(SELECT name FROM tb_student WHERE tb_student.id = id);
    -> END //
Query OK, 0 rows affected (0.10 sec)
mysql> DELIMITER ;
```

func_parameter 可以由多个参数组成，其中每个参数由参数名称和参数类型组成，其形式如下：

```sql
[IN | OUT | INOUT] param_name type;
```

其中：

- IN 表示输入参数，OUT 表示输出参数，INOUT 表示既可以输入也可以输出；
- param_name 参数是存储函数的参数名称；
- type 参数指定存储函数的参数类型，该类型可以是 MySQL 数据库的任意数据类型。

查看存储函数的语法如下：

```sql
SHOW FUNCTION STATUS LIKE 存储函数名;
SHOW CREATE FUNCTION 存储函数名;
SELECT * FROM information_schema.Routines WHERE ROUTINE_NAME=存储函数名;
```

可以发现，操作存储函数和操作存储过程不同的是将 PROCEDURE 替换成了 FUNCTION。同样，修改存储函数的语法如下：

```sql
ALTER FUNCTION 存储函数名 [ 特征 ... ]
```

存储函数的特征与存储过程的基本一样。
删除存储过程的语法如下：

```sql
DROP FUNCTION [ IF EXISTS ] <函数名>
```

## 7. 调用

存储过程通过 **CALL** 语句来调用，存储函数的使用方法与 MySQL 内部函数的使用方法相同。执行存储过程和存储函数需要拥有 EXECUTE 权限（EXECUTE 权限的信息存储在 `information_schema `数据库下的 `USER_PRIVILEGES `表中）。

```sql
# CALL 语句接收存储过程的名字以及需要传递给它的任意参数，基本语法形式如下：
CALL sp_name([parameter[...]]);
# 存储函数的使用方法与 MySQL 内部函数的使用方法是一样的
SELECT func_student(3);
```

## 8. 变量

```sql
# 定义,声明
DECLARE var_name[,...] type [DEFAULT value]
# 赋值
SET var_name = expr[,var_name = expr]...
# 赋值2
SELECT col_name [...] INTO var_name[,...]
FROM table_name WEHRE condition;
```

> 注意：@X 表示用户变量，使用 SET 语句为其赋值，用户变量与连接有关，一个客户端定义的变量不能被其他客户端所使用，当客户端退出时，该客户端连接的所有变量将自动释放。

## 9. 定义条件和处理程序

```sql
# 定义条件
DECLARE condition_name CONDITION FOR condition_value;
condition value:
SQLSTATE [VALUE] sqlstate_value | mysql_error_code
/*
其中：
condition_name 参数表示条件的名称；
condition_value 参数表示条件的类型；
sqlstate_value 参数和 mysql_error_code 参数都可以表示 MySQL 的错误。sqlstate_value 表示长度为 5 的字符串类型错误代码，mysql_error_code 表示数值类型错误代码。例如 ERROR 1146(42S02) 中，sqlstate_value 值是 42S02，mysql_error_code 值是 1146。
*/

eg:定义“ERROR 1146 (42S02)”这个错误，名称为 can_not_find
-- 方法一：使用sqlstate_value
DECLARE can_not_find CONDITION FOR SQLSTATE '42S02';

-- 方法二：使用 mysql_error_code
DECLARE can_not_find CONDITION FOR 1146;
```

**处理程序**

```sql
DECLARE handler_type HANDLER FOR condition_value[...] sp_statement ;

handler_type:
	CONTINUE | EXIT | UNDO
condition_value:
	SQLSTATE [VALUE] sqlstate_value | condition_name | SQLWARNING | NOT FOUND | SQLEXCEPTION | mysql_error_code
/*
	其中，handler_type 参数指明错误的处理方式，该参数有 3 个取值。这 3 个取值分别是 CONTINUE、EXIT 和 UNDO。
	CONTINUE 表示遇到错误不进行处理，继续向下执行；
	EXIT 表示遇到错误后马上退出；
	UNDO 表示遇到错误后撤回之前的操作，MySQL 中暂时还不支持这种处理方式。
注意：	通常情况下，执行过程中遇到错误应该立刻停止执行下面的语句，并且撤回前面的操作。但是，MySQL 中现在还不能支持 UNDO 操作。因此，遇到错误时最好执行 EXIT 操作。如果事先能够预测错误类型，并且进行相应的处理，那么可以执行 CONTINUE 操作。

参数指明错误类型，该参数有 6 个取值：
sqlstate_value：包含 5 个字符的字符串错误值；
condition_name：表示 DECLARE 定义的错误条件名称；
SQLWARNING：匹配所有以 01 开头的 sqlstate_value 值；
NOT FOUND：匹配所有以 02 开头的 sqlstate_value 值；
SQLEXCEPTION：匹配所有没有被 SQLWARNING 或 NOT FOUND 捕获的 sqlstate_value 值；
mysql_error_code：匹配数值类型错误代码。

sp_statement 参数为程序语句段，表示在遇到定义的错误时，需要执行的一些存储过程或函数。
*/

eg:
# 方法一：捕获 sqlstate_value
DECLARE CONTINUE HANDLER FOR SQLSTATE '42S02' SET @info='CAN NOT FIND';
# 捕获 sqlstate_value 值。如果遇到 sqlstate_value 值为 42S02，执行 CONTINUE 操作，并且输出“CAN NOT FIND”信息。

//方法二：捕获 mysql_error_code
DECLARE CONTINUE HANDLER FOR 1146 SET @info='CAN NOT FIND';

//方法三：先定义条件，然后调用
DECLARE can_not_find CONDITION FOR 1146;
DECLARE CONTINUE HANDLER FOR can_not_find SET @info='CAN NOT FIND';

//方法四：使用 SQLWARNING
DECLARE EXIT HANDLER FOR SQLWARNING SET @info='ERROR';

//方法五：使用 NOT FOUND
DECLARE EXIT HANDLER FOR NOT FOUND SET @info='CAN NOT FIND';

//方法六：使用 SQLEXCEPTION
DECLARE EXIT HANDLER FOR SQLEXCEPTION SET @info='ERROR';
```

## 10. 游标

只能在存储过程中使用

```sql
-- 声明
DECLARE cursor_name CURSOR FOR select_statement;
-- select_statement 表示 SELECT 语句，可以返回一行或多行数据。

-- 打开游标
OPEN cursor_name;
-- 打开一个游标时，游标并不指向第一条记录，而是指向第一条记录的前边。

-- 使用游标 FETCH...INTO
FETCH cursor_name INTO var_name [,var_name]...
-- 将游标 cursor_name 中 SELECT 语句的执行结果保存到变量参数 var_name 中
-- 使用用游标类似高级语言中的数组遍历，当第一次使用游标时，此时游标指向结果集的第一条记录。

-- 关闭游标
CLOSE cursor_name;
/*
	CLOSE 释放游标使用的所有内部内存和资源，因此每个游标不再需要时都应该关闭。
	在一个游标关闭后，如果没有重新打开，则不能使用它。但是，使用声明过的游标不需要再次声明，用 OPEN 语句打开它就可以了。
	如果你不明确关闭游标，MySQL 将会在到达 END 语句时自动关闭它。游标关闭之后，不能使用 FETCH 来使用该游标。
*/
```

## 11. 流程控制

### (1) if

```sql
IF search_condition THEN statement_list
    [ELSEIF search_condition THEN statement_list]...
    [ELSE statement_list]
END IF
```

> 注意：MySQL 中的 `IF( ) 函数`不同于这里的 IF 语句。

### (2) case

```sql
CASE case_value
    WHEN when_value THEN statement_list
    [WHEN when_value THEN statement_list]...
    [ELSE statement_list]
END CASE

/*
其中：
case_value 参数表示条件判断的变量，决定了哪一个 WHEN 子句会被执行；
when_value 参数表示变量的取值，如果某个 when_value 表达式与 case_value 变量的值相同，则执行对应的 THEN 关键字后的 statement_list 中的语句；
statement_list 参数表示 when_value 值没有与 case_value 相同值时的执行语句。
CASE 语句都要使用 END CASE 结束。
*/
# 另一种形式
CASE
    WHEN search_condition THEN statement_list
    [WHEN search_condition THEN statement_list] ...
    [ELSE statement_list]
END CASE
/*
	其中，search_condition 参数表示条件判断语句；statement_list 参数表示不同条件的执行语句。
	与上述语句不同的是，该语句中的 WHEN 语句将被逐个执行，直到某个 search_condition 表达式为真，则执行对应 THEN 关键字后面的 statement_list 语句。如果没有条件匹配，ELSE 子句里的语句被执行。
*/
```

### (3) LOOP

```sql
# LOOP 语句本身没有停止循环的语句，必须使用 LEAVE 语句等才能停止循环，跳出循环过程
[begin_label:]LOOP
    statement_list
END LOOP [end_label]
/*
其中，begin_label 参数和 end_label 参数分别表示循环开始和结束的标志，这两个标志必须相同，而且都可以省略；statement_list 参数表示需要循环执行的语句。
*/
```

### (4) LEAVE

```SQL
-- 跳出循环控制
LEAVE label
```

### (5) ITERATE

```sql
-- 用来跳出本次循环，直接进入下一次循环
ITERATE label
```

### (6) REPEAT

```sql
[begin_label:] REPEAT
    statement_list
    UNTIL search_condition
END REPEAT [end_label]
/*
有条件控制的循环语句，每次语句执行完毕后，会对条件表达式进行判断，如果表达式返回值为 TRUE，则循环结束，否则重复执行循环中的语句。
*/
```

### (7) WHILE

```sql
[begin_label:] WHILE search_condition DO
    statement list
END WHILE [end label]
/*
有条件控制的循环语句。WHILE 语句和 REPEAT 语句不同的是，WHILE 语句是当满足条件时，执行循环内的语句，否则退出循环
*/
```

# 触发器

​ MySQL 的触发器和存储过程一样，都是嵌入到 MySQL 中的一段程序，是 MySQL 中管理数据的有力工具。不同的是执行存储过程要使用 CALL 语句来调用，而触发器的执行不需要使用 CALL 语句来调用，也不需要手工启动，而是通过对数据表的相关操作来触发、激活从而实现执行。比如当对 student 表进行操作（INSERT，DELETE 或 UPDATE）时就会激活它执行。

​ 触发器与数据表关系密切，主要用于保护表中的数据。特别是当有多个表具有一定的相互联系的时候，触发器能够让不同的表保持数据的一致性。

​ 在 MySQL 中，只有执行 **INSERT、UPDATE 和 DELETE** 操作时才能激活触发器，其它 SQL 语句则不会激活触发器。

## 1. 优缺点

触发器的优点如下：

- 触发器的执行是自动的，当对触发器相关表的数据做出相应的修改后立即执行。
- 触发器可以实施比 FOREIGN KEY 约束、CHECK 约束更为复杂的检查和操作。
- 触发器可以实现表数据的级联更改，在一定程度上保证了数据的完整性。

触发器的缺点如下：

- 使用触发器实现的业务逻辑在出现问题时很难进行定位，特别是涉及到多个触发器的情况下，会使后期维护变得困难。
- 大量使用触发器容易导致代码结构被打乱，增加了程序的复杂性，
- 如果需要变动的数据量较大时，触发器的执行效率会非常低。

## 2. mysql 支持的触发器

### (1) INSERT 触发器

在 INSERT 语句执行之前或之后响应的触发器。
使用 INSERT 触发器需要注意以下几点：

- 在 INSERT 触发器代码内，可引用一个名为 NEW（不区分大小写）的虚拟表来访问被插入的行。
- 在 BEFORE INSERT 触发器中，NEW 中的值也可以被更新，即允许更改被插入的值（只要具有对应的操作权限）。
- 对于 AUTO_INCREMENT 列，NEW 在 INSERT 执行之前包含的值是 0，在 INSERT 执行之后将包含新的自动生成值。

### (2).UPDATE 触发器

在 UPDATE 语句执行之前或之后响应的触发器。
使用 UPDATE 触发器需要注意以下几点：

- 在 UPDATE 触发器代码内，可引用一个名为 NEW（不区分大小写）的虚拟表来访问更新的值。
- 在 UPDATE 触发器代码内，可引用一个名为 OLD（不区分大小写）的虚拟表来访问 UPDATE 语句执行前的值。
- 在 BEFORE UPDATE 触发器中，NEW 中的值可能也被更新，即允许更改将要用于 UPDATE 语句中的值（只要具有对应的操作权限）。
- OLD 中的值全部是只读的，不能被更新。

注意：当触发器设计对触发表自身的更新操作时，只能使用 BEFORE 类型的触发器，AFTER 类型的触发器将不被允许。

### (3).DELETE 触发器

在 DELETE 语句执行之前或之后响应的触发器。
使用 DELETE 触发器需要注意以下几点：

- 在 DELETE 触发器代码内，可以引用一个名为 OLD（不区分大小写）的虚拟表来访问被删除的行。
- OLD 中的值全部是只读的，不能被更新。

### (5)执行机制

总体来说，触发器使用的过程中，MySQL 会按照以下方式来处理错误。

对于事务性表，如果触发程序失败，以及由此导致的整个语句失败，那么该语句所执行的所有更改将回滚；对于非事务性表，则不能执行此类回滚，即使语句失败，失败之前所做的任何更改依然有效。

若 BEFORE 触发程序失败，则 MySQL 将不执行相应行上的操作。

若在 BEFORE 或 AFTER 触发程序的执行过程中出现错误，则将导致调用触发程序的整个语句失败。

仅当 BEFORE 触发程序和行操作均已被成功执行，MySQL 才会执行 AFTER 触发程序。

## 3.创建

```sql
create trigger  <触发器名>
< before | after > <insert | update | delete > ON <表名>
for each Row
<触发器主体> ;
```

**1) 触发器名**

​ 触发器的名称，触发器在当前数据库中必须具有唯一的名称。如果要在某个特定数据库中创建，名称前面应该加上数据库的名称。

**2) INSERT | UPDATE | DELETE**

​ 触发事件，用于指定激活触发器的语句的种类。
​ 注意：三种触发器的执行时间如下。

- INSERT：将新行插入表时激活触发器。例如，INSERT 的 BEFORE 触发器不仅能被 MySQL 的 INSERT 语句激活，也能被 LOAD DATA 语句激活。
- DELETE： 从表中删除某一行数据时激活触发器，例如 DELETE 和 REPLACE 语句。
- UPDATE：更改表中某一行数据时激活触发器，例如 UPDATE 语句。

**3) BEFORE | AFTER**

​ BEFORE 和 AFTER，触发器被触发的时刻，表示触发器是在激活它的语句之前或之后触发。若希望验证新数据是否满足条件，则使用 BEFORE 选项；若希望在激活触发器的语句执行之后完成几个或更多的改变，则通常使用 AFTER 选项。

**4) 表名**

​ 与触发器相关联的表名，此表必须是永久性表，不能将触发器与临时表或视图关联起来。在该表上触发事件发生时才会激活触发器。同一个表不能拥有两个具有相同触发时刻和事件的触发器。例如，对于一张数据表，不能同时有两个 BEFORE UPDATE 触发器，但可以有一个 BEFORE UPDATE 触发器和一个 BEFORE INSERT 触发器，或一个 BEFORE UPDATE 触发器和一个 AFTER UPDATE 触发器。

**5) 触发器主体**

​ 触发器动作主体，包含触发器激活时将要执行的 MySQL 语句。如果要执行多个语句，可使用 BEGIN…END 复合语句结构。

**6) FOR EACH ROW**

​ 一般是指行级触发，对于受触发事件影响的每一行都要激活触发器的动作。例如，使用 INSERT 语句向某个表中插入多行数据时，触发器会对每一行数据的插入都执行相应的触发器动作。

> 注意：每个表都支持 INSERT、UPDATE 和 DELETE 的 BEFORE 与 AFTER，因此**每个表最多支持 6 个触发器**。每个表的每个事件每次只允许有一个触发器。单一触发器不能与多个事件或多个表关联。

另外，在 MySQL 中，若需要查看数据库中已有的触发器，则可以使用 `SHOW TRIGGERS;` 语句。

## 4. 查看

```sql
# 查看当前创建的所有触发器的基本信息
SHOW TRIGGERS;
/*
由运行结果可以看到触发器的基本信息。对以上显示信息的说明如下：
Trigger 表示触发器的名称，在这里触发器的名称为 trigupdate；
Event 表示激活触发器的事件，这里的触发事件为更新操作 UPDATE；
Table 表示激活触发器的操作对象表，这里为 account 表；
Statement 表示触发器执行的操作，这里是向 myevent 数据表中插入一条数据；
Timing 表示触发器触发的时间，这里为更新操作之后（AFTER）；
还有一些其他信息，比如触发器的创建时间、SQL 的模式、触发器的定义账户和字符集等，这里不再一一介绍。
*/

-- 如果要查看特定触发器的信息或者数据库中触发器较多时，可以直接从 information_schema 数据库中的 triggers 数据表中查找。
SELECT * FROM information_schema.triggers WHERE trigger_name= '触发器名';
/*
由运行结果可以看到触发器的详细信息。对以上显示信息的说明如下：
TRIGGER_SCHEMA 表示触发器所在的数据库；
TRIGGER_NAME 表示触发器的名称；
EVENT_OBJECT_TABLE 表示在哪个数据表上触发；
ACTION_STATEMENT 表示触发器触发的时候执行的具体操作；
ACTION_ORIENTATION 的值为 ROW，表示在每条记录上都触发；
ACTION_TIMING 表示触发的时刻是 AFTER；
还有一些其他信息，比如触发器的创建时间、SQL 的模式、触发器的定义账户和字符集等，这里不再一一介绍。
*/
```

## 5.删除

```sql
# 删除
DROP TRIGGER [ IF EXISTS ] [数据库名] <触发器名> ;
```

# Event 事件（定时任务）

​ 事件（Event）也可称为事件调度器（Event Scheduler），是用来执行定时任务的一组 SQL 集合，可以通俗理解成 MySQL 中的定时器。一个事件可调用一次，也可周期性的启动。

​ 事件可以作为定时任务调度器，取代部分原来只能用操作系统的计划任务才能执行的工作。另外，更值得一提的是，MySQL 的事件可以实现每秒钟执行一个任务，非常适合对实时性要求较高的环境，而操作系统的计划任务只能精确到每分钟一次。

​ 事件和触发器类似，都是在某些事情发生时启动。当数据库启动一条语句的时候，触发器就启动了，而事件是根据调度事件来启动的。由于他们彼此相似，所以事件也称为临时性触发器。

```sql
# 查看事件是否开启
SHOW VARIABLES LIKE 'event_scheduler'; # ON
SELECT @@event_scheduler; # ON
SHOW PROCESSLIST; #

# 开启事件
SET GLOBAL event_scheduler = ON ;
# 方法二，配置文件 【mysqld】添加
event_scheduler = ON
#在配置文件中添加代码并保存文件后，重启 MySQL 服务才能生效。
```

## 1. 创建

```sql
CREATE EVENT [IF NOT EXISTS] event_name
    ON SCHEDULE schedule
    [ON COMPLETION [NOT] PRESERVE]
    [ENABLE | DISABLE | DISABLE ON SLAVE]
    [COMMENT 'comment']
    DO event_body;
```

| 子句                                  | 说明                                                                                                                                                                                                                                                                                                        |
| ------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| DEFINER                               | 可选 用于定义事件执行时检查权限的用户                                                                                                                                                                                                                                                                       |
| IF NOT EXISTS                         | 可选 用于判断要创建的事件是否存在                                                                                                                                                                                                                                                                           |
| EVENT event_name                      | 必选 用于指定事件名称，event_name 的最大长度为 64 个字符 如果未指定 event_name，则默认为当前的 MySQL 用户名（不区分大小写）                                                                                                                                                                                 |
| ON SCHEDULE schedule                  | 必选 用于定义执行的时间和时间间隔 schedule 表示触发点                                                                                                                                                                                                                                                       |
| ON COMPLETION [NOT] PRESERVE          | 可选 用于定义事件是否循环执行，即是一次执行还是永久执行，默认为一次执行，即 NOT PRESERVE                                                                                                                                                                                                                    |
| ENABLE \| DISABLE \| DISABLE ON SLAVE | 可选，用于指定事件的一种属性。 其中，关键字 ENABLE 表示该事件是活动的，即调度器检查事件是否必须调用； 关键字 DISABLE 表示该事件是关闭的，即事件的声明存储到目录中，但是调度器不会检查它是否应该调用； 关键字 DISABLE ON SLAVE 表示事件在从机中是关闭的。 如果不指定以上 3 个选项中的任何一个，默认为 ENABLE |
| COMMENT 'comment'                     | 可选，用于定义事件的注释                                                                                                                                                                                                                                                                                    |
| DO event_body                         | 必选 用于指定事件启动时所要执行的代码，可以是任何有效的 SQL 语句、存储过程或者一个计划执行的事件。 如果包含多条语句，则可以使用 BEGIN..END 复合结构                                                                                                                                                         |

在 ON SCHEDULE 子句中，参数 schedule 的值为一个 AT 子句，用于指定事件在某个时刻发生，其语法格式如下：

```sql
AT timestamp [+ INTERVAL interval]...
    | EVERY interval
    [STARTS timestamp [+ INTERVAL interval] ...]
    [ENDS timestamp[+ INTERVAL interval]...]
```

参数说明如下：

- timestamp：一般用于只执行一次，表示一个具体的时间点，后面加上一个时间间隔，表示在这个时间间隔后事件发生。
- EVERY 子句：用于事件在指定时间区间内每隔多长时间发生一次，其中 STARTS 子句用于指定开始时间；ENDS 子句用于指定结束时间。
- interval：一般用于周期性执行，表示一个从现在开始的时间，其值由一个数值和单位构成。例如，使用“4 WEEK”表示 4 周，使用“'1:10'HOUR_MINUTE”表示 1 小时 10 分钟。间隔的长短用 DATE_ADD() 函数支配。

interval 参数可以是以下值：

**YEAR** | QUARTER | **MONTH** | **DAY** | **HOUR** | **MINUTE** |
WEEK | **SECOND** | YEAR_MONTH | DAY_HOUR | DAY_MINUTE |
DAY_SECOND | HOUR_MINUTE | HOUR_SECOND | MINUTE_SECOND

一般情况下，不建议使用不标准（以上未加粗关键字）的时间单位。

## 2. 查看

```sql
/*
创建好事件后，用户可以通过以下 3 种方式来查看事件的状态信息：
1. 查看 mysql.event
2. 查看 information_schema.events
3. 切换到相应的数据库后执行 SHOW EVENTS;
*/

SELECT * FROM information_schema.events limit 1\G
```

| 参数名               | 说明                                                                                                                                                                                                                                  |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| EVENT_CATALOG        | 事件存放目录，一般情况下，值为 def，不建议修改                                                                                                                                                                                        |
| EVENT_SCHEMA         | 事件所在的数据库                                                                                                                                                                                                                      |
| EVENT_NAME           | 事件名称                                                                                                                                                                                                                              |
| DEFINER              | 事件的定义者                                                                                                                                                                                                                          |
| TIME_ZONE            | 事件使用的时区，默认是 SYSTEM，不建议修改                                                                                                                                                                                             |
| EVENT_BODY           | 一般情况下，值为 SQL，不建议修改                                                                                                                                                                                                      |
| EVENT_DEFINITION     | 该事件的内容，可以是具体的 INSERT 等 SQL，也可以是一个调用的存储过程                                                                                                                                                                  |
| EVENT_TYPE           | 事件类型，这个参数比较重要，在定义时指定 有两个值：RECURRING 和 ONE TIME RECURRING 表示只要符合条件就会重复执行，RECURRING 类型的事件一般为 NULL，表示该事件的预计执行时间 ONE TIME 只会调用 EXECUTE_AT，针对 one-time 类型的事件有效 |
| INTERVAL_VALUE       | 针对 RECURRING 类型的事件有效，表示执行间隔长度                                                                                                                                                                                       |
| INTERVAL_FIELD       | 针对 RECURRING 类型的事件有效，表示执行间隔的单位，一般是 SECOND，DAY 等值，可参考创建语法                                                                                                                                            |
| SQL_MODE             | 当前事件采用的 SQL_MODE                                                                                                                                                                                                               |
| STARTS               | 针对 RECURRING 类型的事件有效，表示一个事件从哪个时间点开始执行，和 one-time 的 EXECUTE_AT 功能类似。 为 NULL 时表示一符合条件就开始执行                                                                                              |
| ENDS                 | 针对 RECURRING 类型的事件有效，表示一个事件到了哪个时间点后不再执行，如果为 NULL 就是永不停止                                                                                                                                         |
| STATUS               | 一般有三个值，ENABLED、DISABLED 和 SLAVESIDE_DISABLED                                                                                                                                                                                 |
| ON_COMPLETION        | 只有两个值，PRESERVE 和 NOT PRESERVE                                                                                                                                                                                                  |
| CREATED              | 事件的创建时间                                                                                                                                                                                                                        |
| LAST_ALTERED         | 事件最近一次被修改的时间                                                                                                                                                                                                              |
| LAST_EXECUTED        | 事件最近一次执行的时间，如果为 NULL 表示从未执行过                                                                                                                                                                                    |
| EVENT_COMMENT        | 事件的注释信息                                                                                                                                                                                                                        |
| ORIGINATOR           | 当前事件创建时的 server-id，用于主从上的处理，比如 SLAVESIDE_DISABLED                                                                                                                                                                 |
| CHARACTER_SET_CLIENT | 事件创建时的客户端字符集                                                                                                                                                                                                              |
| COLLATION_CONNECTION | 事件创建时的连接字符校验规则                                                                                                                                                                                                          |
| DATABASE_COLLATION   | 事件创建时的数据库字符集校验规则                                                                                                                                                                                                      |

## 3. 修改/删除

```sql
# 修改
ALTER EVENT event_name
    ON SCHEDULE schedule
    [ON COMPLETION [NOT] PRESERVE]
    [ENABLE | DISABLE | DISABLE ON SLAVE]
    [COMMENT 'comment']
    DO event_body;
/*

*/

# 删除
DROP EVENT [IF EXISTS] event_name;
```

# 存储引擎
在MySQL中，可以通过以下两种方式设置表的存储引擎：

1. 创建表时指定存储引擎：在创建表时，可以使用ENGINE关键字指定表的存储引擎。例如，创建一个使用InnoDB存储引擎的表的SQL语句如下：

   ```sql
   CREATE TABLE table_name (column_name column_type, ...) ENGINE=InnoDB;
   ```

2. 修改表的存储引擎：可以使用ALTER TABLE语句修改表的存储引擎。例如，将表的存储引擎修改为InnoDB的SQL语句如下：

   ```sql
   ALTER TABLE table_name ENGINE=InnoDB;
   ```

需要注意的是，不是所有的存储引擎都支持相同的功能和特性，因此在选择存储引擎时需要根据具体的应用场景和需求进行选择。同时，在使用存储引擎时需要注意其特性和限制，以充分发挥其性能和可靠性。

此外，在MySQL中，可以使用以下命令查看当前的默认存储引擎和支持的存储引擎：

``` sql
SHOW VARIABLES LIKE 'default_storage_engine';
SHOW ENGINES;
```

第一个命令用于查看当前的默认存储引擎，第二个命令用于查看支持的存储引擎列表和各个存储引擎的状态。

总之，在MySQL中，可以通过CREATE TABLE和ALTER TABLE语句来设置表的存储引擎，并且需要根据应用场景和需求选择合适的存储引擎。

[MySQL 存储引擎 InnoDB 与 Myisam 的六大区别](https://my.oschina.net/junn/blog/183341)

引擎可以混用,但外键和事务不能跨引擎

```sql
create teable xx()engine=innodb; -- 创建表时设定引擎
```

MySQL 支持数个存储引擎作为对不同表的类型的处理器。MySQL 存储引擎包括处理事务安全表的引擎和处理非事务安全表的引擎：

- `InnoDB` 可靠的`事务处理`引擎,支持外键和事务管理,性能中下
- `BDB` 提供事务安全表,被包含在为支持它的操作系统发布的 MySQL-Max 二进制分发版里。InnoDB 也默认被包括在所 有 MySQL 5.1 二进制分发版里，你可以按照喜好通过配置 MySQL 来允许或禁止任一引擎。
- `MyISAM` 默认引擎,管理**非事务**表。它提供`高速存储和检索`，以及`全文搜索`能力。MyISAM 在所有 MySQL 配置里被支持，它是默认的存储引擎，除非你配置 MySQL 默认使用另外一个引擎。
- `MEMORY` 功能类似于 `MyISAM` ,支持**全文搜索**,**非事务**,数据存储在**内存**而非磁盘,速度特别快,适合临时表;
- `EXAMPLE` 存储引擎是一个"存根"引擎，它不做什么。你可以用这个引擎创建表，但没有数据被存储于其中或从其中检索。这个引擎的目的是服务，在 MySQL 源代码中的一个例子，它演示说明如何开始编写新存储引擎。同样，它的主要兴趣是对开发者。
- NDB Cluster 是被 MySQL Cluster 用来实现分割到多台计算机上的表的存储引擎。它在 MySQL-Max 5.1 二进制分发版里提供。这个存储引擎当前只被 Linux, Solaris, 和 Mac OS X 支持。在未来的 MySQL 分发版中，我们想要添加其它平台对这个引擎的支持，包括 Windows。
- `ARCHIVE` 存储引擎被用来无索引地，非常小地覆盖存储的大量数据。
- `CSV` 存储引擎把数据以逗号分隔的格式存储在文本文件中,它的键没有索引，不支持为空，不支持自增。
- `BLACKHOLE` 存储引擎接受但不存储数据，并且检索总是返回一个空集。
- `FEDERATED` 存储引擎把数据存在远程数据库中。在 MySQL 5.1 中，它只和 MySQL 一起工作，使用 MySQL C Client API。在未来的分发版中，我们想要让它使用其它驱动器或客户端连接方法连接到另外的数据源。

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
      1. 创建从库账号并授权 slave` mysql> GRANT REPLICATION SLAVE ON *.* to 'mysync'@'%' identified by 'q123456';`
      2. 查看主库状态`mysql>show master status;`
   3. 配置从库:
      1. `mysql>change master to master_host='192.168.145.222',master_user='mysync',master_password='q123456',         master_log_file='mysql-bin.000004',master_log_pos=308;   -- 注意不要断开，308数字前后无单引号`
      2. 启动从库复制:`Mysql>start slave;`
      3. 检查状态` mysql> show slave status\G`,Slave_IO 及 Slave_SQL 进程必须正常运行，即 YES 状态，否则都是错误的状态(如：其中一个 NO 均属错误)。
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

