# Dockerfile

Dockerfile 是一个用来构建镜像的文本文件，文本内容包含了一条条构建镜像所需的指令和说明。

# 指令

## ARG

设置参数，该参数值可以从 **--build-arg <varname>=<value>** 接收值

在 FROM 之前的 ARG 参数只在 FROM 语句中生效，若在 FROM 之后想要继续使用 ARG，需要再次设置

```dockerfile
ARG <name>[=<default value>]

ARG version="1.0.0"
```

## FROM

指定基础镜像，FROM 必须为 Dockerfile 非注释行的第一行。

```dockerfile
FROM <image>
FROM <image>:<tag>
FROM <image>@<digest>

FROM ubuntu:14.04
```

## LABEL

设置镜像标签

```dockerfile

LABEL <key>=<value> <key>=<value> <key>=<value> ...
LABEL maintainer="demo@demo.com"
```

## ENV

设置环境变量

- **建议**：不论用哪种书写方式，在实际使用中，一行都只写一个环境变量，方便阅读。
- **特别地**：在使用 docker run 命令添加参数 **--env** 时，若有相同的环境变量，以 run 命令为准。

```dockerfile
ENV <key> <value>此方法一次只能设置一个
ENV <key>=<value> ... 该方法一次可以设置多个环境变量

ENV JAVA_HOME=/home/jdk-8
```

## ADD

添加文件，将宿主机的文件添加到容器中。

## COPY

添加文件，将宿主机的文件添加到容器中。

```dockerfile

COPY [--chown=<user>:<group>] <src>... <dest>
COPY [--chown=<user>:<group>] ["<src>",... "<dest>"]
```

## USER

指定运行容器的用户

```dockerfile

USER <user>[:<group>]
USER <UID>[:<GID>]
USER root
```

## WORKDIR

工作目录，进入容器后，以 **WORKDIR** 为当前路径

```dockerfile
WORKDIR /workdir

WORKDIR /home
```

## EXPOSE

说明容器暴露的端口，默认协议为 **tcp** ，若是 **udp** 协议，则需要在后面添加 **udp** ，如 **80/udp**

```dockerfile
EXPOSE <port> [<port>/<protocol>...]

EXPOSE 8080
# 表明容器在运行时提供8080端口，在启动该容器时需端口映射。
```

## VOLUME

设置挂载点，将容器内的路径挂载到宿主机，该挂载方式是将容器内的路径挂载到 docker 数据路径下

```dockerfile
VOLUME <url>

VOLUME /var/log
```

## RUN

执行命令并创建新的镜像层，通常用于更新或安装软件。

## CMD

设置容器启动后默认执行的命令，CMD 命令会被 docker run 的参数覆盖。

```dockerfile
CMD <command>
CMD ["executable","param1","param2"]

CMD systemclt start docker.service
#启动容器时启动docker服务
```

## ENTRYPOINT

和 CMD 一样，设置容器启动后默认执行的命令，但是该命令不会被 docker run 覆盖，会始终执行，CMD 会被 docker run 传入的命令覆盖。

```dockerfile
ENTRYPOINT <command>

ENTRYPOINT ["executable", "param1", "param2"]

ENTRYPOINT /usr/local/apache-tomcat-8.5.33/bin/startup.sh
```

# 构建命令

```sh
docker build \ #
  -t <镜像名>:<标签> \ # 生成对象
  . # 上下文路径，是指 docker 在构建镜像，有时候想要使用到本机的文件（比如复制），docker build 命令得知这个路径后，会将路径下的所有内容打包。
```

# Demo

```dockerfile
FROM tomcat:9.0
LABEL maintainer="demo@demo.com"
ADD ${WORKSPACE}/target/cip-file-1.0.0-SNAPSHOT /opt/apache-tomcat- 9.0.12/webapps/file
ENV LC_ALL en_US.UTF-8 EXPOSE 8080
ENTRYPOINT /opt/apache-tomcat-9.0.12/bin/startup.sh && tail -f /opt/apache- tomcat-9.0.12/logs/catalina.out
```

## go

dockerfile

```dockerfile
FROM golang:alpine AS builder

LABEL stage=gobuilder

ENV CGO_ENABLED 0
ENV GOOS linux
ENV GOPROXY https://goproxy.cn,direct

WORKDIR /build

ADD go.mod .
ADD go.sum .
RUN go mod download
COPY . .
RUN go build -ldflags="-s -w" -o /app/hello ./hello.go


FROM alpine

RUN apk update --no-cache && apk add --no-cache ca-certificates tzdata
ENV TZ Asia/Shanghai

WORKDIR /app
COPY --from=builder /app/hello /app/hello

CMD ["./hello"]
```

## mysql

```dockerfile
FROM mysql:5.7

COPY script/db/config/mysql.cnf /etc/mysql/conf.d/mysql.cnf

ENV MYSQL_ROOT_PASSWORD baas
ENV MYSQL_USER baas
ENV MYSQL_PASSWORD baas
ENV MYSQL_DATABASE baasdb

COPY script/db/create_table.sql /docker-entrypoint-initdb.d/
COPY script/db/blkproxy_mysql_table.sql /docker-entrypoint-initdb.d/
COPY script/db/prepare_data.sql /docker-entrypoint-initdb.d/
```

**mysql.cnf**

```
[client]
default-character-set=utf8

[mysql]
default-character-set=utf8

[mysqld]
character-set-server=utf8
collation-server=utf8_unicode_ci
```

## java

```dockerfile
#依赖的父镜像
FROM java:8
#jar包添加到镜像中
ADD product-0.0.1-SNAPSHOT.jar first.jar
#容器暴露的端口 即jar程序在容器中运行的端口
EXPOSE 8088
#容器启动之后要执行的命令
ENTRYPOINT ["java","-jar","first.jar"]
```
