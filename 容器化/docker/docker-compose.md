# docker-compose

---

**docker-compose** 是 docker 的原生编排技术，你可以定义一个或多个应用在 **docker-compose.yaml** 里面，然后使用 **docker-compose** 命令行来启动你的应用。

# 安装

使用以下命令安装指定版本的 docker-compose ，若下载太慢，可通过浏览器手动下载指定版本后添加到系统环境变量中。

GitHub 地址： [docker-compose](https://github.com/docker/compose)

```sh
curl -L "https://github.com/docker/compose/releases/download/v2.2.2/docker- compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

curl -L https://github.com/docker/compose/releases/download/1.8.1/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose



https://github.com/docker/compose/releases/download/v2.2.3/docker-compose-linux-x86_64
```

**方法二：**

```sh
 yum -y install epel-release && yum -y install python-pip && pip install docker-compose
```

待安装完成后，执行查询版本的命令，即可安装 docker-compose

```
 docker-compose version
```

# 命令

```js
Usage:
  docker-compose [-f <arg>...] [options] [COMMAND] [ARGS...]
  docker-compose -h|--help
Options:
  -f, --file FILE 指定docker-compose.yaml，默认为当前路径下docker-compose.yaml
  -p, --project-name NAME 指定项目名称
```

### config

验证 **docker-compose.yaml** 文件编写是否正确

```js
Usage: config [options]
Options:
  -q, --quiet 安静模式，不输出任何信息，除非文件错误
  --services 显示服务名称
  --volumes 显示挂载卷名称
特别地：这个命令不能指定yaml文件，默认为docker-compose.yaml或docker-compose.yml
```

### down

停止容器并且删除容器，网络，数据卷，镜像

```js
Usage: down [options]
Options:
  --rmi type 删除镜像
    all：删除service用到的所有镜像
    local：只删除没有自定义标签的镜像
  -v, --volumes 删除volumes定义的数据卷
  --remove-orphans 删除未定义在docker-compose.yaml里面的容器
```

### pull

拉取 **docker-compose.yaml** 里面定义的镜像

```js
Usage: pull [options] [SERVICE...]
Options:
  --ignore-pull-failures 当有失败时，继续执行其它镜像拉取
  --parallel 开启并行拉取
  --no-parallel 禁止并行拉取
  -q, --quiet 安静模式
  --include-deps 拉取依赖项
```

### restart

重启一个或多个 service

### start

启动 service

```js
Usage: start [SERVICE...]
```

### stop

停止 service

```js
Usage: stop [options] [SERVICE...]
```

### up

构建，创建，启动某个 service 的容器

```js
Usage: up [options] [--scale SERVICE=NUM...] [SERVICE...]
Options:
  -d, --detach 后台运行
  --quiet-pull 安静模式 --force-recreate 重新创建容器，即使镜像没有任何改动
  --no-recreate 当容器存在时不重新创建
  --no-build 不要构建镜像，即使镜像不存在
  --no-start 创建之后不要启动service.
  --build 在启动容器前创建镜像
  --abort-on-container-exit 当有一个容器停止时，停止其他容器
  --remove-orphans 删除未定义在compose file中的容器
  --scale SERVICE=NUM 设置服务运行的容器个数
```

# 配置文件

```yaml
version: "2" # 版本号

services: #服务

networks: #网络
  front-tier:
    driver: bridge
  back-tier:

driver: bridge #网络驱动
```

## networks

```yaml
networks: #网络
  front-tier:
    driver: bridge
  back-tier:
```

## services

1. 定义服务名
2. 定义容器名 `container_name`
3. 镜像 `image` `build`
4. 容器启动命令 `entrypoint` `command`
5. 前置容器 `depends_on`
6. 网络 `dns` `expose` `extra_hosts`
7. 环境变量 `environment` `env_file`
8.

```yaml
web:  # 自定义服务名称
  container_name: app # 自定义容器名
  image: dockercloud/hello-world # docker 镜像
  build: /path/to/build/dir # dockerfile 所在的文件夹,自构建镜像
  build:
    context: . # 根目录
    dockerfile: /path/to/build/dir # 相对于根目录dockerfile 所在的文件夹
    args: # Dockerfile 中的 ARG 指令
      buildno: 1
      password: secret
  command: bundle exec thin -p 3000 | [bundle, exec, thin, -p, 3000] # 覆盖容器启动后默认执行的命令
  depends_on: # 先启动 db redis 在启动APP
    - db
    - redis
  dns: 8.8.8.8
  dns: # --dns 参数
    - 8.8.8.8
    - 9.9.9.9
  dns_search: example.com
  dns_search:
    - dc1.example.com
    - dc2.example.com
  tmpfs: /run #挂载临时目录到容器内部，与 run 的参数一样效果
  tmpfs:
    - /run
    - /tmp
  entrypoint: /code/entrypoint.sh  # 定义接入点，覆盖 Dockerfile 中的定义
  entrypoint:
    - php
    - -d
    - zend_extension=/usr/local/lib/php/extensions/no-debug-non-zts-20100525/xdebug.so
    - -d
    - memory_limit=-1
    - vendor/bin/phpunit
  env_file: .env # 如果有变量名称与 environment 指令冲突，则以后者为准
  env_file:
    - ./common.env
    - ./apps/web.env
    - /opt/secrets.env
  environment:
    RACK_ENV: development
    SHOW: 'true'
    SESSION_SECRET:
  environment:
    - RACK_ENV=development
    - SHOW=true
    - SESSION_SECRET
  expose: # 与Dockerfile中的EXPOSE指令一样，用于指定暴露的端口,实际上docker-compose.yml的端口映射还得ports这样的标签
    - "3000"
    - "8000"
  external_links: # 让Compose项目里面的容器连接到那些项目配置外部的容器（前提是外部容器中必须至少有一个容器是连接到与项目内的服务的同一个网络里面）
    - redis_1
    - project_db_1:mysql
    - project_db_1:postgresql
  extra_hosts: #添加主机名的标签，就是往/etc/hosts文件中添加一些记录，与Docker client的--add-host类似
    - "somehost:162.242.195.82"
    - "otherhost:50.31.209.229"
  labels: #向容器添加元数据，和Dockerfile的LABEL指令一个意思
    com.example.description: "Accounting webapp"
    com.example.department: "Finance"
    com.example.label-with-empty-value: ""
  labels:
    - "com.example.description=Accounting webapp"
    - "com.example.department=Finance"
    - "com.example.label-with-empty-value"
  links: # 解决的是容器连接问题，与Docker client的--link一样效果，会连接到其它服务中的容器
    - db
    - db:database
    - redis
  logging: # 配置日志服务 https://docs.docker.com/engine/admin/logging/overview/
    driver: syslog
    options:
      syslog-address: "tcp://192.168.0.42:123"
  pid: "host" #将PID模式设置为主机PID模式，跟主机系统共享进程命名空间。容器使用这个标签将能够访问和操纵其他容器和宿主机的名称空间。
  ports: # 映射端口的标签。
    - 8080 # 使用HOST:CONTAINER格式或者只是指定容器的端口，宿主机会随机映射端口
    - "8000:8000"
  security_opt: # 为每个容器覆盖默认的标签。简单说来就是管理全部服务的标签。比如设置全部服务的user标签值为USER
    - label:user:USER
    - label:role:ROLE
  stop_signal: SIGUSR1 # 设置另一个信号来停止容器。在默认情况下使用的是SIGTERM停止容器。设置另一个信号可以使用stop_signal标签。
  volumes: # 挂载一个目录或者一个已存在的数据卷容器，可以直接使用 [HOST:CONTAINER] 这样的格式，或者使用 [HOST:CONTAINER:ro] 这样的格式，后者对于容器来说，数据卷是只读的，
    #// 只是指定一个路径，Docker 会自动在创建一个数据卷（这个路径是容器内部的）。
    - /var/lib/mysql
    #// 使用绝对路径挂载数据卷
    - /opt/data:/var/lib/mysql
    #// 以 Compose 配置文件为中心的相对路径作为数据卷挂载到容器。
    - ./cache:/tmp/cache
    #// 使用用户的相对路径（~/ 表示的目录是 /home/<用户目录>/ 或者 /root/）。
    - ~/configs:/etc/configs/:ro
    #// 已经存在的命名的数据卷。
    - datavolume:/var/lib/mysql
  volume_driver: mydriver # 如果你不使用宿主机的路径，你可以指定一个volume_driver。
  volumes_from: # 从其它容器或者服务挂载数据卷，可选的参数是 :ro或者 :rw，前者表示容器只读，后者表示容器对数据卷是可读可写的。默认情况下是可读可写的。
    - service_name
    - service_name:ro
    - container:container_name
    - container:container_name:rw

  networks: # 加入指定网络
    - front-tier
    - back-tier
  networks:
    some-network:
      aliases: # 设置服务别名的标签 相同的服务可以在不同的网络有不同的别名。
        - alias1
        - alias3
    other-network:
      aliases:
        - alias2
  cpu_shares: 73
  cpu_quota: 50000
  cpuset: 0,1

  user: postgresql
  working_dir: /code

  domainname: foo.com
  hostname: foo
  ipc: host
  mac_address: 02:42:ac:11:65:43

  mem_limit: 1000000000
  memswap_limit: 2000000000
  privileged: true # docker 容器内部权限提升到 root

  restart: always

  read_only: true
  shm_size: 64M
  stdin_open: true
  tty: true

```

# demo

## jmeter

```yaml
version: "2"

services:
  jmeter-single:
    container_name: jmeter-single
    image: justb4/jmeter:5.4
    command:
      - -n # 表示在非 GUI 模式下运行 JMeter
      - -t /results/test.jmx #表示要运行的 JMeter 测试脚本文件，一般是 jmx 结尾的文件
      - -l /results/log.jtl # 表示记录结果的文件，默认以 jtl 结尾
      - -e # 表示测试完成后生成测试报表
      - -o /results/res # 表示指定的生成结果文件夹位置
    volumes:
      - ./results2:/results
```
