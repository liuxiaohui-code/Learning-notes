# 简介

## 定义

- Docker 是一个开源的应用容器引擎，基于 Go 语言并遵从 Apache2.0 协议开源；
- Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化；
- 容器是完全使用沙箱机制，相互之间不会有任何接口，更重要的是容器性能开销极低；
- Docker 从 17.03 版本之后分为 CE（Community Edition-社区版）和 EE（Enterprise Edition-企业版）。

## 应用场景

- Web 应用的自动化打包和发布，自动化测试和持续集成、发布；
- 在服务型环境中部署和调整数据库或其他的后台应用；
- 从头编译或者扩展现有的 OpenShift 或 Cloud Foundry 平台来搭建自己的 PaaS 环境。

## 常用网站

- [Docker 官网](https://www.docker.com/)
- [Docker 中文社区](https://www.docker.org.cn/)
- [Docker Hub](https://hub.docker.com/) 镜像搜索网站
- [docker 文档](https://docs.docker.com/)
- 新手教程： https://docs.docker.com/get-started/
  大量的例子： https://docs.docker.com/samples/
  用户文档： https://docs.docker.com/engine/userguide/
  镜像： https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/
  存储： https://docs.docker.com/engine/userguide/storagedriver/
  网络： https://docs.docker.com/engine/userguide/networking/
  管理文档： https://docs.docker.com/engine/admin/
  存储： https://docs.docker.com/engine/admin/volumes/
  安全： https://docs.docker.com/engine/security/security/
  集群： https://docs.docker.com/engine/swarm/

# 安装

## centos

### 脚本自动安装

安装命令如下：

```
curl -fsSL https://get.docker.com | bash -s docker --mirror aliyun
```

也可以使用国内 daocloud 一键安装命令：

```
curl -sSL https://get.daocloud.io/docker | sh
```

启动

```bash
service docker start
```

### 卸载 docker

删除安装包：

```
yum remove docker-ce
```

删除镜像、容器、配置文件等内容：

```
rm -rf /var/lib/docker
```

## Debian

ubuntu 安装类似

### 安装环境

```js
Debian - 10.7 - amd64
```

### 安装依赖

```js
apt update && apt -y install apt-transport-https ca-certificates curl gnupg2 software-properties-common
```

### 安装 GPG 证书

```js
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/debian/gpg | sudo apt-key add
```

### 添加软件源

这里添加的源为 [北京外国语大学开源软件镜像站](https://mirrors.bfsu.edu.cn/)

```js
add-apt-repository "deb [arch=amd64] https://mirrors.bfsu.edu.cn/docker- ce/linux/debian $(lsb_release -cs) stable"
```

### 更新并安装

```js
apt update && apt -y install docker-ce
```

## 重启

重启 docker 服务会将 docker 端口插入 iptables

```sh
#Ubuntu 14.04:
sudo service docker restart
#Ubuntu 16.04, CentOS 7:
sudo systemctl daemon-reload && sudo systemctl restart docker
# 开机启动
sudo systemctl enable docker
```

# docker 批量删除镜像

```sh
docker rmi $(docker images | grep "none" | awk '{print $3}')
docker rmi $(docker images | grep "dev-peer" | awk '{print $3}')
docker images会查看所有的镜像，grep "none"命令会筛选所有名字为none以及标签为none的镜像。awk '{print $3}'会处理筛选后的文本，打印所有镜像id的内容。

docker restart $(docker ps -a -q)

WARNING: IPv4 forwarding is disabled. Networking will not work.
# vim /etc/sysctl.conf
net.ipv4.ip_forward=1    #添加此行配置
# systemctl restart network && systemctl restart docker
# sysctl net.ipv4.ip_forward
方法一：
#显示所有的容器，过滤出Exited状态的容器，取出这些容器的ID，
sudo docker ps -a|grep Exited|awk '{print $1}'
#查询所有的容器，过滤出Exited状态的容器，列出容器ID，删除这些容器
sudo docker rm -v `docker ps -a|grep Exited|awk '{print $1}'`
sudo docker rm `docker ps -a|grep k8s|awk '{print $1}'`
方法二：
#删除所有未运行的容器（已经运行的删除不了，未运行的就一起被删除了）
sudo docker rm $(sudo docker ps -a -q)
方法三：
#根据容器的状态，删除Exited状态的容器
sudo docker rm $(sudo docker ps -qf status=exited)
方法四：
#Docker 1.13版本以后，可以使用 docker containers prune 命令，删除孤立的容器。
sudo docker container prune
#删除所有镜像
sudo docker rmi $(docker images -q)


rm -rf ~/.kube/
rm -rf /etc/kubernetes/
rm -rf /etc/systemd/system/kubelet.service.d
rm -rf /etc/systemd/system/kubelet.service
rm -rf /usr/bin/kube*
rm -rf /etc/cni
rm -rf /opt/cni
rm -rf /var/lib/etcd

registry.aliyuncs.com/google_containers/pause:3.5 "/pause"
```

# 基础配置

---

​ Docker 有两处可以配置参数：

​ 一个是 **docker.service** 服务配置文件,一个是 Docker daemon 配置文件 **daemon.json** 。

​ 建议所有修改都在 **daemon.json** 中进行。若没有 **daemon.json** ，自行创建 **/etc/docker/daemon.json**

官方 [daemon.json](https://docs.docker.com/engine/reference/commandline/dockerd/#daemon-configuration-file)配置说明。这里对一些常用的配置进行说明。

```s
/usr/lib/systemd/system/docker.service

# 修改配置后不需要重启
/etc/docker/daemon.json
```

## 镜像下载和上传并发数

```js
{
 "max-concurrent-downloads": 3,
 "max-concurrent-uploads": 5
}
```

## 镜像加速地址

国内直接拉取镜像会有些缓慢，为了加速镜像的下载，可以给 Docker 配置国内的镜像地址。

```js
{
 "registry-mirrors": [
  "https://ucjisdvf.mirror.aliyuncs.com",
  "http://hub-mirror.c.163.com",
  "https://docker.mirrors.ustc.edu.cn",
  "https://registry.docker-cn.com",
  "http://hub-mirror.c.163.com",
  "https://docker.mirrors.ustc.edu.cn",
  "https://docker.mirrors.ustc.edu.cn"
  ]

 // https://1sldomc8.mirror.aliyuncs.com
}
```

## 私有仓库

Docker 默认只信任

**insecure-registries** 字面意思为不安全的仓库，通过添加这个参数对非 **https** 仓库进行授信。可以设置多个 **insecure-registries** 地址，以数组形式书写，地址不能添加协议头 **http**。

```js
{
 "insecure-registries": ["<IP>:<PORT>","<IP>:<PORT>"]
}
```

## 存储驱动

**OverlayFS**

启用 **overlay2** 条件：Linux 内核版本 4.0 或更高版本。

```js
{ "storage-driver": "overlay2", "storage-opts": ["overlay2.override_kernel_check=true"] }
```

## 日志驱动

​ 容器在运行时会产生大量日志文件，很容易占满磁盘空间。通过配置日志驱动来限制文件大小与文件的数量。

日志等级分为 **debug|info|warn|error|fatal** ，默认为 **info**

```js
{
 "log-level": "warn",
 "log-driver": "json-file",
 "log-opts": {
   "max-size": "100m",
   "max-file": "3"
  },
}
```

## 数据存储路径

默认路径为

```js
{
"data-root": "/home/docker"
}
```

## Demo

这里提供一个经常使用的配置模板

```js
{
 "insecure-registries": ["192.168.2.2:8080"],
 "oom-score-adjust": -1000,
 "exec-opts": ["native.cgroupdriver=systemd"],
 "registry-mirrors": ["https://ucjisdvf.mirror.aliyuncs.com"],
 "storage-driver": "overlay2",
 "storage-opts": ["overlay2.override_kernel_check=true"],
"log-level": "warn",
"log-driver": "json-file",
"log-opts": {
     "max-size": "100m",
     "max-file": "3"
},
"data-root": "/home/docker"
}
```

# Docker 命令

Docker 命令的详细使用方法请参考 [官网](https://docs.docker.com/engine/reference/run/)或者 **docker --help** 进行查询，这里只记录部分常用命令。

## 操作镜像

### 拉取 pull

从镜像仓库中拉取或者更新指定镜像，在未声明镜像标签时，默认标签为 latest。

```js
Usage: docker pull [OPTIONS] NAME[:TAG|@DIGEST]
Options:
    -a 拉取某个镜像的所有版本
    --disable-content-trust 跳过校验，默认开启
```

### 查看 images

列出镜像

```js
Usage: docker images [OPTIONS] [REPOSITORY[:TAG]]
Options:
  -a, --all 显示所有镜像
  -f, --filter filter 使用过滤器过滤镜像
    dangling true or false, true列出没有标签的，false相反
    label (label=<key> or label=<key>=<value>)，如果镜像设置有label，则可以通过label过 滤
    before (<image-name>[:<tag>], <image id> or <image@digest>) - 某个镜像前的镜像
    since (<image-name>[:<tag>], <image id> or <image@digest>) - 某个镜像后的镜像
    reference (pattern of an image reference) - 模糊查询,例：--
    filter=reference='busy*:*libc'
  --format string 格式化输出
    .ID 镜像ID
    .Repository 镜像仓库
    .Tag 镜像tag
    .Digest Image digest
    .CreatedSince 创建了多久
    .CreatedAt 镜像创建时间
    .Size 镜像大小
-q, --quiet 只显示镜像ID
```

示例

```bash
# 过滤器的使用
docker images -f
	'dangling=true' # 列出没有标签的
	'dangling=false' # 列出有标签的
	'lable=<key>[=<value>]' #如果镜像设置有label，则可以通过label过滤
	'before=<image_id>|<image_name>[:<tag>]|
```

### 导出

#### save

将一个或多个镜像打包为 tar 包

```js
Usage:	docker save [OPTIONS] IMAGE [IMAGE...]


Options:
  -o, --output string   Write to a file, instead of STDOUT

```

#### export

将容器导出为一个 tar 包

```js
Usage: docker export [OPTIONS] CONTAINER
Options:
    -o, --output string tar包名称
```

### 导入

#### load

从 tar 包加载一个镜像

```js
Usage: docker load [OPTIONS]
Options:
   -i, --input string 指定tar包
   -q, --quiet 只显示ID
```

#### import

通过导入 tar 包的方式创建镜像

```js
Usage: docker import [OPTIONS] file|URL|- [REPOSITORY[:TAG]]
Options:
   -m, --message string 设置提交信息
```

### rmi

删除一个或多个镜像

### search

查找镜像

```js
Usage: docker search [OPTIONS] TERM
```

### 构建

#### build

通过 **Dockerfile** 构建镜像

```sh
Usage: docker build [OPTIONS] PATH | URL | -
Options:
    -f, --file string 指定Dockerfile，默认为当前路径的Dockerfile
    -q, --quiet 安静模式，构建成功后输出镜像ID
    -t, --tag list 给镜像设置tag，name:tag
```

#### commit

通过容器创建一个新镜像

```sh
Usage:  docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]

Create a new image from a container's changes

Options:
  -a, --author string    Author (e.g., "John Hannibal Smith <hannibal@a-team.com>")
  -c, --change list      Apply Dockerfile instruction to the created image
  -m, --message string   Commit message
  -p, --pause            Pause container during commit (default true)
```

> 不要用 commit，去写 Dockerfile。

## 操作容器

### 启动 run

创建并启动一个容器

```js
Usage: docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
Options:
  -d, --detach 后台运行容器，并输出容器ID
  -e, --env list 设置环境变量，该变量可以在容器内使用
  -h, --hostname string 指定容器的hostname
  -i, --interactive 以交互模式运行容器，通常与-t同时使用
  -l, --label list 给容器添加标签
  --name string 设置容器名称，否则会自动命名
  --network string 将容器加入指定网络
  -p, --publish list 设置容器映射端口
  -P,--publish-all 将容器设置的所有exposed端口进行随机映射
  --restart string 容器重启策略，默认为不重启
    on-failure[:max-retries]：在容器非正常退出时重启，可以设置重启次数。
    unless-stopped：总是重启，除非使用stop停止容器
    always：总是重启
  --rm 容器退出时则自动删除容器
  -t, --tty 分配一个伪终端
  -u, --user string 运行用户或者UID
  -v, --volume list 数据挂载
  -w, --workdir string 容器的工作目录
  --privileged=true 给容器特权


```

常用

```js
docker run -d --name=nginx1 -p=[8777:80] nginx:latest

docker run --rm=true --network host -v "${pwd}/FinReport-front":"/opt/FinReport-front"  node:16 sh -c "cd /opt/FinReport-front && npm  install --registry=https://registry.npm.taobao.org && npm run build"

docker run -d --name superset -p 9221:8080 apache/superset sh -c "superset  fab create-admin --username admin --firstname Superset --lastname Admin --email admin@superset.com  --password admin && superset  db upgrade &&  superset load_examples &&  superset init "

docker run --rm=true --network host \
    -v "/data/fin-report/FinReport-front":"/opt/FinReport-front" \
    node:16 \
    sh -c "cd /opt/FinReport-front && npm  install --registry=https://registry.npm.taobao.org && npm run build"
```

### 重启 restart

### 文件拷贝 cp

在容器和宿主机之间拷贝文件

```js
Usage:
    docker cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH|-
    docker cp [OPTIONS] SRC_PATH|- CONTAINER:DEST_PATH
Options:
    -a, --archive 保留文件权限
```

### 进入容器

#### exec

进入容器后，退出，容器正常运行

```sh
Usage: docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
Options:
    -d, --detach 在后台运行命令
    -e, --env list 设置环境变量
    -i, --interactive 以交互模式运行
    -t, --tty 分配一个伪终端
    -u, --user string 执行命令的用户
    -w, --workdir string 工作目录
```

常用

```bash
#使用bash进入容器
docker exec -it <docker_name> bash
#退出容器
exit  | CTRL+D
#直接执行命令
docker exec -it <docker_name> <command>
```

#### attach

进入容器后，退出，容器停止

```bash
Usage:  docker attach [OPTIONS] CONTAINER

Attach local standard input, output, and error streams to a running container

Options:
      --detach-keys string   Override the key sequence for detaching a container
      --no-stdin             Do not attach STDIN
      --sig-proxy            Proxy all received signals to the process (default true)
```

### 停止

#### stop

### 删除

#### kill

杀死一个或多个容器

```js
Usage: docker kill [OPTIONS] CONTAINER [CONTAINER...]
```

### 日志 logs

显示容器日志

```js
Usage: docker logs [OPTIONS] CONTAINER
Options:
  --details 显示详细日志
  -f, --follow 跟随日志输出
  --tail string 显示行数
  -t, --timestamps 显示时间戳
```

### 列表 ps

列出容器

```js
Usage: docker ps [OPTIONS]
Options:
  -a, --all 列出所有容器
  -f, --filter filter 使用过滤器过滤
  --format string 格式化输出
  -n, --last int 显示最后创建的n个容器
  -l, --latest 显示最后一个创建的容器
  -q, --quiet 只显示容器ID
  -s, --size 显示大小
```

## 镜像仓库

### login

登录 Docker 镜像仓库

```
Usage: docker login [OPTIONS] [SERVER]
Options:
  -p, --password string 密码
  -u, --username string 账户
```

### logout

退出 Docker 镜像仓库

```js
Usage: docker logout [SERVER]
```

### push

将容器推送到镜像仓库

```js
Usage: docker push [OPTIONS] NAME[:TAG]
```

## rename

给容器重命名

```js
Usage: docker rename CONTAINER NEW_NAME
```

## start

启动一个或多个容器

```js
Usage: docker start [OPTIONS] CONTAINER [CONTAINER...]
```

## stats

显示容器资源使用情况

```js
Usage: docker stats [OPTIONS] [CONTAINER...]
Options:
  -a, --all 显示所有容器，默认只显示正在运行的容器
```

## stop

停止一个或多个容器

```js
Usage: docker stop [OPTIONS] CONTAINER [CONTAINER...]
```

## tag

给镜像设置新的 tag

```js
Usage: docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
```

## inspect

获取容器或镜像的元数据

```js
Usage: docker inspect [OPTIONS] NAME|ID [NAME|ID...]
```

# FAQ

---

## 1. WARNING: No swap limit support

- **原因**： **cgroups** 中的 swap account 未开启
- **解决**：

编辑文件 **/etc/default/grub** ，找到 **GRUB_CMDLINE_LINUX** 修改为以下内容

```js
GRUB_CMDLINE_LINUX = "cgroup_enable=memory swapaccount=1"
```

修改完之后执行

```js
sudo update-grub && sudo reboot
```

## 2. Permission denied (socket: /run/docker.sock)

- **原因**：没有把当前用户加入到 docker 组
- **解决**：

1、将当前用户加到 docker 组

```js
gpasswd -a {user} docker
```

2、更新用户组

```js
newgrp docker
```

3、修改权限

```js
chmod 666 /var/run/docker.sock
chown root:docker /var/run/docker.sock
```

## 3.Docker 容器网络不通

- **原因**：docker 网桥故障
- **解决**：

在修改配置并重启 **docker** 后若网络还不通，可能是 **docker network** 的故障，需要自定义网桥

```js
# 停止docker
systemctl stop docker.service
# 添加网桥
brctl addbr br0
# 添加IP网段
ip addr add 172.16.0.1/24 dev br0
# 启用网桥br0
ip link set dev br0 up
```

修改 **docker** 默认网桥，在配置文件 **/etc/docker/daemon.json** 中新增以下配置

```js
"bridge":"br0"
```

重启 **docker**

```js
systemctl start docker.service
```

## 4.WARNING: IPv4 forwarding is disabled.Networking will not work.

- **原因**：未开启转发
- **解决**：开启转发

```js
# 编辑文件/etc/sysctl.conf
vim /etc/sysctl.conf
# 新增配置
net.ipv4.ip_forward=1
# 重启网络和docker
systemctl restart network
systemctl restart docker
```

## 5.不要用 commit 来构建镜像

​ Docker 不是虚拟机。这句话要在学习 Docker 的过程中反复提醒自己。所以不要把虚拟机中的一些概念带过来。

​ Docker 提供了很好的 Dockerfile 的机制来帮助定制镜像，可以直接使用 Shell 命令，非常方便。而且，这样制作的镜像更加透明，也容易维护，在基础镜像升级后，可以简单地重新构建一下，就可以继承基础镜像的安全维护操作。

​ 使用 docker commit 制作的镜像被称为**黑箱镜像**，换句话说，就是里面进行的是黑箱操作，除本人外无人知晓。即使这个制作镜像的人，过一段时间后也不会完整的记起里面的操作。那么当有些东西需要改变时，或者因基础镜像更新而需要重新制作镜像时，会让一切变得异常困难，就如同重新安装调试配置服务器一样，失去了 Docker 的优势了。

​ 另外，Docker 不是虚拟机，其文件系统是 Union FS，分层式存储，**每一次 commit 都会建立一层，上一层的文件并不会因为 rm 而删除，只是在当前层标记为删除而看不到了而已**，每次 docker pull 的时候，那些不必要的文件都会如影随形，所得到的镜像也必然臃肿不堪。而且，随着文件层数的增加，不仅仅镜像更臃肿，其运行时性能也必然会受到影响。这一切都违背了 Docker 的最佳实践。

​ 使用 commit 的场合是一些特殊环境，比如**入侵后保存现场**等等，这个命令不应该成为定制镜像的标准做法。所以，请用 Dockerfile 定制镜像。

## 6.为什么说不要使用 import, export, save, load, commit 来构建镜像？

commit 命令在前一个问答已经说过，这是制作黑箱镜像，无法维护，不应该被使用。

import 和 export 的做法，实际上是将一个容器来保存为 tar 文件，然后在导入为镜像。这样制作的镜像同样是黑箱镜像，不应该使用。而且这类导入导出会导致原有分层丢失，合并为一层，而且会丢失很多相关镜像元数据或者配置，比如 CMD 命令就可能丢失，导致镜像无法直接启动。

save 和 load 确实是镜像保存和加载，但是这是在没有 registry 的情况下，手动把镜像考来考去，这是回到了十多年的 U 盘时代。这同样是不推荐的，镜像的发布、更新维护应该使用 registry。无论是自己架设私有 registry 服务，还是使用公有 registry 服务，如 Docker Hub。

## 7.重启容器使配置生效的方式

- 修改 Dockerfile 只能删除镜像和容器，并重新生成镜像才能生效
- 修改 docker-compose.yml 只能先 down 再 up 才能生效，其它重启操作都无效
- 修改 nginx 配置，任何重启操作都可以生效

# 容器使用示例

## 8.1 MySQL

```yaml
version: "2"

services:
  database:
    container_name: mysql
    image: mysql:5.7
    restart: always
    ports:
      - 3306:3306
    environment:
      - MYSQL_ROOT_PASSWORD=123456
      - MYSQL_DATABASE=test1
      - MYSQL_USER=root
      - MYSQL_PASSWORD=123456
    volumes:
      # - ./mysqldata/:/var/lib/mysql
      - ./create_table.sql:/docker-entrypoint-initdb.d/create_table.sql
```

## 8.2 Redis

```yaml
version: "3"

services:
  redis:
    image: redis:latest
    container_name: redis
    restart: always
    ports:
      - 6379:6379
    networks:
      - mynetwork
    volumes:
      #[HOST:CONTAINER:ro]   ro只读 rw读写
      - ./redis.conf:/usr/local/etc/redis/redis.conf:rw
      - ./data:/data:rw
    command:
      #使用command可以覆盖容器启动后默认执行的命令。这里启动执行指定的redis.conf文件
      /bin/bash -c "redis-server /usr/local/etc/redis/redis.conf "
networks:
  mynetwork:
    external: true
```

```
#redis.conf
bind 0.0.0.0
protected-mode no
port 6379
timeout 0
save 900 1 # 900s内至少一次写操作则执行bgsave进行RDB持久化
save 300 10
save 60 10000
rdbcompression yes
dbfilename dump.rdb
dir /data
appendonly yes
appendfsync everysec
requirepass 12345678
```

```sh
docker ps 查看当前运行的服务
docker exec -it 6d784c652a20 redis-cli 连接redis服务
keys * 查看当前redis中的key
auth 12345678 验证密码
```

## 8.3 postgres

```shell
$ dokcer pull postgres
$ docker run --name postgres -e POSTGRES_PASSWORD=123456 -p 5432:5432 -d postgres
$ docker exec -it postgres /bin/bash
bash: psql -U postgres

postgres=#
```
