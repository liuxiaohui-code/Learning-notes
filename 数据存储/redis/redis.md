# redis

## 特点

Redis 是完全开源免费的，是一个高性能的 key-value 内存数据库。

1. Redis 支持**数据的持久化**，可以将内存中的数据保持在磁盘中，重启的时候可以再次加载进行使用
2. Redis 不仅仅支持简单的 key-value 类型的数据，同时还提供 list，set，zset，hash 等数据结构的存储
3. Redis 支持数据的备份，即 master-slave 模式的数据备份。

Redis 优点

1. 性能极高 – Redis 能读的速度是 110000 次/s,写的速度是 81000 次/s 。
2. 支持丰富的数据类型 - Redis 支持最大多数开发人员已经知道如列表，集合，可排序集合，哈希等数据类型。这使得在应用中很容易解决的各种问题，因为我们知道哪些问题处理, 使用哪种数据类型更好解决。
3. 操作都是原子的 - 所有 Redis 的操作都是原子，从而确保当两个客户同时访问 Redis 服务器得到的是更新后的值（最新值）。原子性（atomicity）：一个事务是一个不可分割的最小工作单位，事务中包括的诸操作要么都做，要么都不做。Redis 所有单个命令的执行都是原子性的，这与它的单线程机制有关；

## 安装下载

```sh
yum install epel-release
yum install redis
# 启动redis
service redis start
# 停止redis
service redis stop
# 查看redis运行状态
service redis status
# 查看redis进程
ps -ef | grep redis
# 开机启动
chkconfig redis on
# 进入本机redis
redis-cli
# 列出所有key
keys *
# 开启6379
/sbin/iptables -I INPUT -p tcp --dport 6379 -j ACCEPT
# 开启6380
/sbin/iptables -I INPUT -p tcp --dport 6380 -j ACCEPT
# 保存
/etc/rc.d/init.d/iptables save
# centos 7下执行
service iptables save
# 默认端口一般是 6379 ，但也可以改成你想要的端口
vi /etc/redis.conf
```

# 附录
