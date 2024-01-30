# 文档

[官方文档](https://zookeeper.apache.org/doc/current/index.html)

# 特性

ZooKeeper 是一个典型的发布/订阅模式的分布式数据管理与协调框架。数据存储在内存中

1. 数据发布/订阅,动态获取配置文件更新
2. 负载均衡,临时节点保存 ip/port 信息,获取数据时,自定义负载均衡算法
3. 命名服务
4. 分布式协调/通知
5. 集群管理
6. Master 选举
7. 分布式锁
8. 分布式队列

# cli

## 进入 cli

```sh
# 启动
bin/zkServer.sh start
# 连接本地主机,默认2181端口
bin/zkCli.sh
# 连接超时与远程主机
bin/zkCli.sh -timeout 3000 -server remoteIP:2181
# 使用-waitforconnection选项连接到远程主机，以在执行命令之前等待连接成功
bin/zkCli.sh -waitforconnection -timeout 3000 -server remoteIP:2181
# 连接到自定义客户端配置属性文件
bin/zkCli.sh -client-configuration /path/to/client.properties
# 退出
quit
```

## 帮助

```sh
help
h
# cli 版本
version
# 显示状态
stat /hbase

ctime	创建节点时的时间
mZxid	最后修改节点时的事务ID
mtime	最后修改节点时的时间
pZxid	表示该节点的子节点列表最后一次修改的事务ID，添加子节点或删除子节点就会影响子节点列表，但是修改子节点的数据内容则不影响该ID（注意，只有子节点列表变更了才会变更pzxid，子节点内容变更不会影响pzxid）
cversion	子节点版本号，子节点每次修改版本号加1
dataversion	数据版本号，数据每次修改该版本号加1
aclversion	权限版本号，权限每次修改该版本号加1
ephemeralOwner	创建该临时节点的会话的sessionID。（**如果该节点是持久节点，那么这个属性值为0）**
dataLength	该节点的数据长度
numChildren	该节点拥有子节点的数量（只统计直接子节点的数量）
```

## 权限

zookeeper 的 acl 通过 [scheme:id:permissions] 来构成权限列表。

1、scheme：代表采用的某种权限机制，包括 world、auth、digest、ip、super 几种。
2、id：代表允许访问的用户。
3、permissions：权限组合字符串，由 cdrwa 组成，其中每个字母代表支持不同权限， 创建权限 create(c)、删除权限 delete(d)、读权限 read(r)、写权限 write(w)、管理权限 admin(a)。

```sh
# 查看权限
getAcl /acl_digest_test
# 添加权限
addauth digest user1:12345
# 超级用户
addauth digest zookeeper:admin
# 修改
setAcl /acl_auth_test auth:user1:12345:crwad
# 递归设置
setAcl -R /acl_auth_test auth:user1:12345:crwad
# -v 设置aclVersion
setAcl -v 3 /acl_auth_test auth:user1:12345:crwad
```

## 关闭与连接

```sh
# 关闭此客户端/会话。
close
# 连接 ZooKeeper 服务器。
connect
connect remoteIP:2181
```

## 配置

```sh
# 显示仲裁成员的配置
config
```

## 节点

```sh
# 显示节点
ls /
# 显示 stat
ls -s /quota_test
# 显示子节点结构
ls -R /quota_test
# 显示节点的监视器
ls -w /brokers

# 持久节点,创建一个新的 znode 并将字符串“my_data”与该节点相关联
create /zk_test my_data
# 临时节点
create -e /ephemeral_node mydata
# 持久顺序节点
create -s /persistent_sequential_node mydata
# 临时顺序节点
create -s -e /ephemeral_sequential_node mydata
# 使用模式创建节点
create /zk-node-create-schema mydata digest:user1:+owfoSBn/am19roBPzR1/MfCblE=:crwad
# 创建容器节点。当删除容器的最后一个子节点时，容器将被删除
create -c /container_node mydata
# create the ttl node. set zookeeper.extendedTypesEnabled=true
create -t 3000 /ttl_node mydata

# 查看节点数据
get /zk_test
# 显示 stat
get -s /latest_producer_id_block
# 显示监视器
get -w /latest_producer_id_block

# 获取所有子节点个数
getAllChildrenNumber /

# 获取本次会话创建的所有临时节点
getEphemerals
getEphemerals /

# 修改
set /zk_test junk
set -s /quota_test mydata_for_quota_test
# -v使用CAS设置数据，可以使用stat从dataVersion中找到版本。
set -v 0 /brokers myNewData

# 删除节点
delete /zk_test
# 删除所有
deleteall /config
```

## 配额

```sh
# 显示
listquota /quota_test
# 删除
delquota /quota_test
delquota -n /c1
delquota -N /c2
delquota -b /c3
delquota -B /c4
# -n限制子节点的数量（包括自身）
setquota -n 2 /quota_test
#-b限制一个路径的字节（数据长度
setquota -b 5 /brokers
# -N count硬配额
setquota -N 2 /c1
#-B字节硬配额
setquota -B 4 /c2
```

## 历史记录

```sh
# 显示您最近执行的 11 个命令的历史记录
history
# 重新执行历史记录的第3条命令
redo 3
```

## 监视

```sh
# 开启打印监视器
printwatches
# 关闭
printwatches off
# 删除
removewatches /brokers
```

## 重新配置

在运行时更改整体的成员资格。

在使用此 cli 之前，请阅读动态重新配置中有关重新配置功能的详细信息，尤其是“安全”部分。https://zookeeper.apache.org/doc/current/zookeeperReconfig.html

先决条件：

1. 在 zoo.cfg 中设置 `reconfigEnabled=true`
2. 添加**超级用户**或 `skipAcl` ，否则会得到“Insufficient permission”。例如 addauth digest zookeeper:admin

```sh
#将从动件2更改为观察者，并将其端口从2182更改为12182
#将观测者5添加到集合中
#从集合中移除观察者4
reconfig --add 2=localhost:2781:2786:observer;12182 --add 5=localhost:2781:2786:observer;2185 -remove 4
# -members to appoint the membership 任命成员
reconfig -members server.1=localhost:2780:2785:participant;0.0.0.0:2181,server.2=localhost:2781:2786:observer;0.0.0.0:12182,server.3=localhost:2782:2787:participant;0.0.0.0:12183
#将当前配置更改为myNewConfig.txt中的配置,但仅当当前配置版本为210000010时
reconfig -file /data/software/zookeeper/zookeeper-test/conf/myNewConfig.txt -v 2100000010
```

## 同步

```sh
# leader和follower之间同步一个节点的数据（异步同步）
sync /
```

# 配置文件

配置文件: `conf/zoo.cfg`

```sh
# 独立zookeeper
dataDir=/data # 快照存储地址
dataLogDir=/datalog # 日志
tickTime=2000 # ZooKeeper 使用的基本时间单位，以毫秒为单位.它用于执行心跳，最小会话超时将是 tickTime 的两倍.
clientPort=2181 # 客户端连接的端口

# 复制的zookeeper
initLimit=5 # 限制 quorum 中的 ZooKeeper 服务器必须连接到领导者的时间长度的超时,5表示 5*tickTime
syncLimit=2 # 限制服务器与领导者的过期时间
# 列出了构成 ZooKeeper 服务的服务器,使用第一个端口2888连接领导者,第二个端口3888进行领导者选举
server.1=zoo1:2888:3888;2181
server.2=zoo2:2888:3888;2181
server.3=zoo3:2888:3888;2181

autopurge.snapRetainCount=3
autopurge.purgeInterval=0
maxClientCnxns=60
standaloneEnabled=true
admin.enableServer=true
```

# 开发

## 数据模型

类似文件结构,保留节点`/zookeeper`,有一个分层命名空间,命名空间中的每个节点都可以有与其关联的数据以及子节点,节点的路径总是表示为规范的、绝对的、斜杠分隔的路径,没有相对路径.

限制路径名:

1. 空字符 (\u0000) 不能是路径名的一部分.（这会导致 C 绑定出现问题.）
2. 不能使用以下字符，因为它们显示效果不佳，或以令人困惑的方式呈现：\u0001 - \u001F 和 \u007F - \u009F.
3. 不允许使用以下字符：\ud800 - uF8FF、\uFFF0 - uFFFF.
4. 这 ”。” 字符可以用作另一个名称的一部分，但“.” 和“..”不能单独用于指示路径上的节点，因为 ZooKeeper 不使用相对路径.以下将是无效的：“/a/b/./c”或“/a/b/../c”.
5. 保留令牌“zookeeper”.

ZooKeeper 树中的每个节点都称为 `znode`.Znodes 维护一个 `stat` 结构，其中包括数据更改的版本号、acl 更改.stat 结构也有时间戳.版本号与时间戳一起允许 ZooKeeper 验证缓存并协调更新.每次 znode 的数据更改时，版本号都会增加.例如，每当客户端检索数据时，它也会收到数据的版本。当客户端执行更新或删除时，它必须提供它正在更改的 znode 数据的版本。如果它提供的版本与数据的实际版本不匹配，则更新将失败。（此行为可以被覆盖。

客户端可以在 znode 上**设置监视**。对该 znode 的更改会触发监视，然后清除监视。当 `watch` 触发时，ZooKeeper 会向客户端发送通知。

存储在命名空间中每个 znode 的数据是**原子读取和写入**的。读取获取与 znode 关联的所有数据字节，写入替换所有数据。每个节点都有一个访问控制列表 (`ACL`)，用于限制谁可以做什么。

ZooKeeper **并非设计为通用数据库或大型对象存储**。相反，它**管理协调数据**。该数据可以配置、状态信息、集合点等形式出现。各种形式的协调数据的一个共同属性是它们相对较小：以千字节为单位。ZooKeeper 客户端和服务器实现有健全性检查以确保 znode 的**数据少于 1M**，但数据应该比平均水平少得多。对相对较大的数据进行操作会导致某些操作比其他操作花费更多的时间，并且会影响某些操作的延迟，因为需要额外的时间通过网络将更多数据移动到存储介质上。如果需要大数据存储，处理此类数据的通常模式是将其存储在大容量存储系统上，

ZooKeeper 也有**临时节点**的概念。只要创建 znode 的会话处于活动状态，这些 znode 就会存在。当会话结束时，znode 将被删除。由于这种行为，临时 znode 不允许有孩子。可以使用 getEphemerals() api 检索会话的临时列表。

> getEphemerals() 检索会话为给定路径创建的临时节点列表。如果路径为空，它将列出会话的所有临时节点。

创建 znode 时，您还可以请求 ZooKeeper 在路径末尾附加一个**单调递增的计数器**。该计数器对于父 znode 是唯一的。计数器的格式为 %010d——即 10 位数字和 0（零）填充（计数器以这种方式格式化以简化排序），即“0000000001”。注意：用于存储下一个序列号的计数器是由父节点维护的有符号 int（4 字节），当递增超过 2147483647 时计数器将溢出

## znode 类型

有四种类型的 znode：

`PERSISTENT`-持久化目录节点

客户端与 zookeeper 断开连接后，该节点依旧存在

`PERSISTENT_SEQUENTIAL`-持久化顺序编号目录节点

客户端与 zookeeper 断开连接后，该节点依旧存在，只是 Zookeeper 给该节点名称进行顺序编号

`EPHEMERAL`-临时目录节点

客户端与 zookeeper 断开连接后，该节点被删除

`EPHEMERAL_SEQUENTIAL`-临时顺序编号目录节点

客户端与 zookeeper 断开连接后，该节点被删除，只是 Zookeeper 给该节点名称进行顺序编号

## 监听通知机制

客户端注册监听它关心的目录节点，当目录节点发生变化（数据改变、被删除、子目录节点增加删除）时，zookeeper 会通知客户端。

## zookeeper 同步

1. client: 发送写请求
2. follower: 请求提交给 leader
3. leader: 转为事务提议
4. 采用 ZAB 协议的原子广播协议,提议广播给 follower
5. follower 将反馈发送给 leader
6. leader 将过半正确 AFK 反馈提交给所有 follower;Observer 节点只负责同步 Leader 数据，不参与 2PC 数据同步过程。
7. 结果返回 client

# curator

1. 解决了 watcher 的一次性的问题，注册一个 watcher 可以触发多次
2. Api 简单易用
3. 可以递归创建节点
4. 提供 ZooKeeper 各种应用场景(recipe， 比如：分布式锁服务、集群领导选举、共享计数器、缓存机制、分布式队列等)的抽象封装
5. 提供了常用的 Zookeeper 工具类
6. 提供了一套 Fluent 风格的操作 API

## 重试策略

一共有五种重试策略

重试策略 `ExponentialBackoffRetry` ，重试 N 次限制总的重试时间
baseSleepTimeMs：两次重试之间的间隔时间
maxRetries：最大重试次数，如果超过次数就放弃
maxSleepMs：最大重试时间，如果超过该时间就放弃
推荐的使用方式为：`RetryPolicy retryPolicy = new ExponentialBackoffRetry(1000, 5);`

重试策略 RetryNTimes，重试 N 次
n：重试的次数
sleepMsBetweenRetries：两次重试之间的间隔时间
推荐的使用方式为：`RetryPolicy retryPolicy = new RetryNTimes(3, 5000);`

重试策略 RetryOneTime，重试一次
sleepMsBetweenRetry：两次重试之间的间隔时间
不推荐使用

重试策略 RetryForever，一直在重试
retryIntervalMs：两次重试之间的间隔时间
不推荐使用

重试策略 RetryUntilElapsed
maxElapsedTimeMs：最大重试时间，重试时间超过 maxElapsedTimeMs 后，就不再重试
sleepMsBetweenRetries：两次重试之间的间隔时间
推荐使用方式为：`RetryPolicy retryPolicy = new RetryUntilElapsed(2000, 3000);`

## 基本使用

### 连接与关闭 Zookeeper

```java
   //1 重试策略：初试时间为 1s 重试 10 次
   RetryPolicy retryPolicy = new ExponentialBackoffRetry(1000, 10);
   //2 通过工厂创建连接
   CuratorFramework client = CuratorFrameworkFactory.builder()
   .connectString("192.168.1.110:2181")//连接地址
   .connectionTimeoutMs(3_000)//连接超时时间
   .sessionTimeoutMs(30_000)//会话超时时间
   .retryPolicy(retryPolicy)//重试策略
   .namespace("super")//命名空间，连接后所有的操作都是在这个/super 节点之下
   .build();
   //3 开启连接
   client.start();
   //4 关闭连接
   client.close();
```

### 节点

creatingParentsIfNeeded 是递归创建节点，如果不存在父节点则同时会创建父节点

创建模式有以下几种:

CreateMode.PERSISTENT：永久节点
CreateMode.PERSISTENT_SEQUENTIAL：永久顺序节点
CreateMode.EPHEMERAL：临时节点
CreateMode.EPHEMERAL_SEQUENTIAL：临时顺序节点
权限控制：

通过 withACL，添加一个权限列表 List<ACL>
ACL 类有两个属性
第一个是 perms，可以通过 Perms 的枚举选择，代表了权限字符串列表：crdwa
第二个是 Id，代表着五种权限机制，比如说 world、digest 等
Id 类，有两个属性
第一个是 scheme，代表权限模式
第二个是 id，代表权限模式后面的字符串，如 digest 模式，user:BASE64(SHA1(password))
List<ACL> acls = new ArrayList<ACL>();
Id demo1 = new Id("digest", AclUtils.getDigestUserPwd("demo1:123456"));
Id demo2 = new Id("digest", AclUtils.getDigestUserPwd("demo2:123456"));
acls.add(new ACL(Perms.ALL, demo1));
acls.add(new ACL(Perms.READ, demo2));
acls.add(new ACL(Perms.DELETE | Perms.CREATE, demo2));

```Java
String nodePath = "/super/demo";
byte[] data = "superme".getBytes();

// 创建节点
client.create().creatingParentsIfNeeded()
.withMode(CreateMode.PERSISTENT)//创建模式
.withACL(ZooDefs.Ids.OPEN_ACL_UNSAFE)//权限列表，如果想要将所有递归创建的节点都指定当前的权限，可以多一个参数 true，如 withACL(ZooDefs.Ids.OPEN_ACL_UNSAFE，true)
.forPath(nodePath, data);

// 更新节点数据
byte[] newData = "batman".getBytes();
client.setData().withVersion(0).forPath(nodePath, newData);

// 删除节点
client.delete()
.guaranteed() // 如果删除失败，那么在后端还是继续会删除，直到成功
.deletingChildrenIfNeeded() // 如果有子节点，就删除
.withVersion(0) // 版本号，如果不匹配会报错
.forPath(nodePath); // 删除的节点地址

// 判断节点是否存在,如果不存在则为空
Stat statExist = cto.client.checkExists().forPath(nodePath + "/abc");
System.out.println(statExist);//statExist!=null，则代表节点存在

// 读取节点数据
Stat stat = new Stat();
byte[] data = client.getData()
.storingStatIn(stat)//将节点的状态信息也读取进来
.forPath(nodePath);
System.out.println("节点" + nodePath + "的数据为: " + new String(data));
System.out.println("该节点的版本号为: " + stat.getVersion());

// 查询子节点
List<String> childNodes = client.getChildren()
.forPath(nodePath);
System.out.println("开始打印子节点：");
for (String s : childNodes) {
System.out.println(s);
}
```

### watcher

```Java
//一次性的创建方式：
String nodePath = "/super/demo";
// 添加 watcher 事件 当使用 usingWatcher 的时候，监听只会触发一次，监听完毕后就销毁
cto.client.getData().usingWatcher((Watcher) event -> {
System.out.println("触发了 watcher"+event);
}).forPath(nodePath);

cto.client.getData().usingWatcher((CuratorWatcher) event -> {
System.out.println("触发了 watcher"+event);
}).forPath(nodePath);

//重复使用父节点的 watcher：
// NodeCache: 监听数据节点的变更，会触发事件
NodeCache nodeCache = new NodeCache(client, nodePath);
nodeCache.start(true);//buildInitial : 初始化的时候获取 node 的值并且缓存，true 代表进行初始化，缓存节点值，默认为 false
if (nodeCache.getCurrentData() != null) {
System.out.println("节点初始化数据为：" + new String(nodeCache.getCurrentData().getData()));
} else {
System.out.println("节点初始化数据为空...");
}

//创建监听器，当节点数据发生更改或者创建时，就会触发该方法
nodeCache.getListenable().addListener(() -> {
//如果是因为删除，导致获取不到节点
if (nodeCache.getCurrentData() == null) {
System.out.println("节点被删除了~");
return;
}
//获取数据
String data = new String(nodeCache.getCurrentData().getData());
//获取触发的节点路径
String path = nodeCache.getCurrentData().getPath()；
System.out.println("节点路径：" + path + "数据：" + data);
});

//重复使用子节点的 watcher：
// 为子节点添加 watcher
// PathChildrenCache: 监听数据节点的增删改，会触发事件
String childNodePathCache = nodePath;
//新建一个子节点缓存
PathChildrenCache childrenCache = new PathChildrenCache(client, childNodePathCache, true);//cacheData: 设置缓存节点的数据状态，如果为 true，也会将子节点的状态信息缓存下来

/**

- StartMode: 初始化方式
- POST_INITIALIZED_EVENT：异步初始化，初始化之后会触发初始化事件（推荐）
- NORMAL：异步初始化
- BUILD_INITIAL_CACHE：同步初始化
*/
childrenCache.start(StartMode.POST_INITIALIZED_EVENT);

//获取缓存的子节点数据
List<ChildData> childDataList = childrenCache.getCurrentData();
System.out.println("当前数据节点的子节点数据列表：");
for (ChildData cd : childDataList) {
String childData = new String(cd.getData());
System.out.println(childData);
}

//为子节点缓存添加监听器，可以对子节点触发的 event 的类型进行判断
childrenCache.getListenable().addListener((client, event) -> {
if (event.getType().equals(PathChildrenCacheEvent.Type.INITIALIZED)) {//初始化事件触发
System.out.println("子节点初始化 ok...");
} else if (event.getType().equals(PathChildrenCacheEvent.Type.CHILD_ADDED)) {//增加子节点
String path = event.getData().getPath();
if (path.equals(ADD_PATH)) {
System.out.println("添加子节点:" + event.getData().getPath());
System.out.println("子节点数据:" + new String(event.getData().getData()));
} else if (path.equals("/super/imooc/e")) {
System.out.println("添加不正确...");
}
} else if (event.getType().equals(PathChildrenCacheEvent.Type.CHILD_REMOVED)) {//删除子节点
System.out.println("删除子节点:" + event.getData().getPath());
} else if (event.getType().equals(PathChildrenCacheEvent.Type.CHILD_UPDATED)) {//子节点数据修改事件
System.out.println("修改子节点路径:" + event.getData().getPath());
System.out.println("修改子节点数据:" + new String(event.getData().getData()));
}
});
```

### watcher 统一配置修改

```Java
public class Client1 {

public CuratorFramework client = null;
public static final String zkServerPath = "192.168.1.110:2181";

public Client1() {
RetryPolicy retryPolicy = new RetryNTimes(3, 5000);
client = CuratorFrameworkFactory.builder()
.connectString(zkServerPath)
.sessionTimeoutMs(10000).retryPolicy(retryPolicy)
.namespace("workspace").build();
client.start();
}

public void closeZKClient() {
if (client != null) {
this.client.close();
}
}

public final static String CONFIG_NODE_PATH = "/super/demo";
public final static String SUB_PATH = "/redis-config";
public static CountDownLatch countDown = new CountDownLatch(1);

public static void main(String[] args) throws Exception {
//连接 Zookeeper
Client1 cto = new Client1();
System.out.println("client1 启动成功...");

       //需要在父节点添加对子节点的监听
      final PathChildrenCache childrenCache = new PathChildrenCache(cto.client, CONFIG_NODE_PATH, true);
      childrenCache.start(StartMode.BUILD_INITIAL_CACHE);

      // 添加监听器
      childrenCache.getListenable().addListener(new PathChildrenCacheListener() {
         public void childEvent(CuratorFramework client, PathChildrenCacheEvent event) throws Exception {
            // 监听节点变化
            if(event.getType().equals(PathChildrenCacheEvent.Type.CHILD_UPDATED)){
               String configNodePath = event.getData().getPath();
               if (configNodePath.equals(CONFIG_NODE_PATH + SUB_PATH)) {
                  System.out.println("监听到配置发生变化，节点路径为:" + configNodePath);

                  // 读取节点数据
                  String jsonConfig = new String(event.getData().getData());
                  System.out.println("节点" + CONFIG_NODE_PATH + "的数据为: " + jsonConfig);

                  // 从json转换配置
                  RedisConfig redisConfig = null;
                  if (StringUtils.isNotBlank(jsonConfig)) {
                     redisConfig = JsonUtils.jsonToPojo(jsonConfig, RedisConfig.class);
                  }

                  // 配置不为空则进行相应操作
                  if (redisConfig != null) {
                     String type = redisConfig.getType();
                     String url = redisConfig.getUrl();
                     String remark = redisConfig.getRemark();
                     // 判断事件
                     if (type.equals("add")) {
                        System.out.println("监听到新增的配置，准备下载...");
                        // ... 连接ftp服务器，根据url找到相应的配置
                        Thread.sleep(500);
                        System.out.println("开始下载新的配置文件，下载路径为<" + url + ">");
                        // ... 下载配置到你指定的目录
                        Thread.sleep(1000);
                        System.out.println("下载成功，已经添加到项目中");
                        // ... 拷贝文件到项目目录
                     } else if (type.equals("update")) {
                        System.out.println("监听到更新的配置，准备下载...");
                        // ... 连接ftp服务器，根据url找到相应的配置
                        Thread.sleep(500);
                        System.out.println("开始下载配置文件，下载路径为<" + url + ">");
                        // ... 下载配置到你指定的目录
                        Thread.sleep(1000);
                        System.out.println("下载成功...");
                        System.out.println("删除项目中原配置文件...");
                        Thread.sleep(100);
                        // ... 删除原文件
                        System.out.println("拷贝配置文件到项目目录...");
                        // ... 拷贝文件到项目目录
                     } else if (type.equals("delete")) {
                        System.out.println("监听到需要删除配置");
                        System.out.println("删除项目中原配置文件...");
                     }

                     // TODO 视情况统一重启服务
                  }
               }
            }
         }
      });

      countDown.await();

      cto.closeZKClient();

}

}
```
