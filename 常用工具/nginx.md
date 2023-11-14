# Nginx

Nginx 是什么，总结一下就是这些：

- 一种轻量级的 web 服务器
- 设计思想是事件驱动的异步非阻塞处理（类 node.js）
- 占用内存少、启动速度快、并发能力强
- 使用 C 语言开发
- 扩展性好，第三方插件非常多
- 在互联网项目中广泛应用

[中文文档](https://blog.redis.com.cn/doc/)
[英文文档](http://nginx.org/en/docs/)

# install

```s
# Ubuntu
sudo apt-get install nginx
# 编译安装
./configure
make
sudo make install
# 其中关于configure是可以按照自己的需求来配置安装的参数的。比如：

./configure \
--user=nginx                          \
--group=nginx                         \
--prefix=/etc/nginx                   \
--sbin-path=/usr/sbin/nginx           \
--conf-path=/etc/nginx/nginx.conf     \
--pid-path=/var/run/nginx.pid         \
--lock-path=/var/run/nginx.lock       \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--with-http_gzip_static_module        \
--with-http_stub_status_module        \
--with-http_ssl_module                \
--with-pcre                           \
--with-file-aio                       \
--with-http_realip_module             \
--without-http_scgi_module            \
--without-http_uwsgi_module           \
--without-http_fastcgi_module
```

# nginx 命令

```s
# Ubuntu 重启,启动,停止nginx
sudo /etc/init.d/nginx restart # or start, stop
sudo service nginx restart # or start, stop
# centos
systemctl start nginx    # 启动 Nginx
systemctl stop nginx     # 停止 Nginx
systemctl restart nginx  # 重启 Nginx
systemctl reload nginx   # 重新加载 Nginx，用于修改配置后
systemctl enable nginx   # 设置开机启动 Nginx
systemctl disable nginx  # 关闭开机启动 Nginx
systemctl status nginx   # 查看 Nginx 运行状态
```

Nginx 的命令在控制台中输入 `nginx -h` 就可以看到完整的命令，这里列举几个常用的命令：

```s
-c <配置文件路径> # 自定义配置文件路径,代替缺省的
-t # 不运行,仅测试配置文件格式
-v # 版本
-V # 版本和编译器版本和配置参数

nginx # 启动
nginx -s reload  # 向主进程发送信号，重新加载配置文件，热重启
nginx -s reopen	 # 重启 Nginx
nginx -s stop    # 快速关闭
nginx -s quit    # 等待工作进程处理完成后关闭
nginx -T         # 查看当前 Nginx 最终的配置
nginx -t -c <配置路径>    # 检查配置是否有问题，如果已经在配置目录，则不需要-c
kill -QUIT  <<nginx主进程号> # nginx从容停止命令，等所有请求结束后关闭服务
kill -TERM <nginx主进程号> #nginx 快速停止命令，立刻关闭nginx进程
kill -9 <nginx主进程号> # 强制停止

kill -QUIT `cat /usr/local/nginx/nginx.pid`
```

可以使用信号系统来控制主进程。默认，nginx 将其主进程的 pid 写入到 `/usr/local/nginx/nginx.pid` 文件中。通过传递参数给 ./configure 或使用 pid 指令，来改变该文件的位置。

主进程可以处理以下的信号：
`TERM`, `INT` 快速关闭
`QUIT` 从容关闭
`HUP` 重载配置
用新的配置开始新的工作进程,从容关闭旧的工作进程
`USR1` 重新打开日志文件
`USR2` 平滑升级可执行程序。
`WINCH` 从容关闭工作进程
尽管你不必自己操作工作进程，但是，它们也支持一些信号：
`TERM`, `INT` 快速关闭
`QUIT` 从容关闭
`USR1` 重新打开日志文件

`systemctl` 是 Linux 系统应用管理工具 `systemd` 的主命令，用于管理系统，我们也可以用它来对 Nginx 进行管理，相关命令如下：

# 配置文件

## 结构

Nginx 的主配置文件是 `/etc/nginx/nginx.conf`，你可以使用 `cat -n nginx.conf` 来查看配置。

`nginx.conf` 结构图可以这样概括：

```bash
# 全局配置，对全局生效
├── events  # 配置影响 Nginx 服务器或与用户的网络连接
|-- stream  # TCP代理
|    |-- server
|	 |-- upstream
├── http    # 配置代理，缓存，日志定义等绝大多数功能和第三方模块的配置
│   ├── upstream # 配置后端服务器具体地址，负载均衡配置不可或缺的部分
│   ├── server   # 配置虚拟主机的相关参数，一个 http 块中可以有多个 server 块
│   ├── server
│   │   ├── location  # server 块可以包含多个 location 块，location 指令用于匹配 uri
│   │   ├── location
│   │   └── ...
│   └── ...
└── ...
```

一个 Nginx 配置文件的结构就像 `nginx.conf` 显示的那样，配置文件的语法规则：

1. 配置文件由指令与指令块构成；
2. 每条指令以 `;` 分号结尾，指令与参数间以空格符号分隔；
3. 指令块以 `{}` 大括号将多条指令组织在一起；
4. `include` 语句允许组合多个配置文件以提升可维护性；
5. 使用 `#` 符号添加注释，提高可读性；
6. 使用 `$` 符号使用变量；
7. 部分指令的参数支持正则表达式；

```s
# 全局配置
user  nginx;                        # 运行用户，默认即是nginx，可以不进行设置
worker_processes  1;                # Nginx 进程数，一般设置为和 CPU 核数一样
error_log  /var/log/nginx/error.log warn;   # Nginx 的错误日志存放目录
pid        /var/run/nginx.pid;      # Nginx 服务启动时的 pid 存放位置

#
events {
    use epoll;     # 使用epoll的I/O模型(如果你不知道Nginx该使用哪种轮询方法，会自动选择一个最适合你操作系统的)
    worker_connections 1024;   # 每个进程允许最大并发数
}
# 配置使用最频繁的部分，代理、缓存、日志定义等绝大多数功能和第三方模块的配置都在这里设置
http {
    # 设置日志模式
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;   # Nginx访问日志存放位置

    sendfile            on;   # 开启高效传输模式
    tcp_nopush          on;   # 减少网络报文段的数量
    tcp_nodelay         on;
    keepalive_timeout   65;   # 保持连接的时间，也叫超时时间，单位秒
    types_hash_max_size 2048;

    include             /etc/nginx/mime.types;      # 文件扩展名与类型映射表
    default_type        application/octet-stream;   # 默认文件类型

    include /etc/nginx/conf.d/*.conf;   # 加载子配置项

    server {
    	listen       80;       # 配置监听的端口
    	server_name  localhost;    # 配置的域名

    	location / {
    		root   /usr/share/nginx/html;  # 网站根目录
    		index  index.html index.htm;   # 默认首页文件
    		deny 172.168.22.11;   # 禁止访问的ip地址，可以为all
    		allow 172.168.33.44； # 允许访问的ip地址，可以为all
    	}

    	error_page 500 502 503 504 /50x.html;  # 默认50x对应的访问页面
    	error_page 400 404 error.html;   # 同上
    }
}
```

## 全局变量

Nginx 有一些常用的全局变量，你可以在配置的任何位置使用它们，如下表：

```yaml
$host: 请求信息中的 Host，如果请求中没有Host 行，则等于设置的服务器名，不包含端口
$request_method: 客户端请求类型，如GET、 POST $remote_addr 客户端的 IP 地址
$args:           请求中的参数
$arg_PARAMETER :  GET 请求中变量名 PARAMETER 参数的值，例如：GET 请求中变量名 PARAMETER 参数的值，例如：$http_user_agent(Uaer-Agent 值), $http_referer...
$content_length : 请求头中的 Content-length 字段
$http_user_agent : 客户端 agent 信息
$http_cookie :    客户端 cookie 信息
$remote_addr  :   客户端的 IP 地址
$remote_port   :  客户端的端口
$http_user_agent: 客户端 agent 信息
$server_protocol: 请求使用的协议，如 HTTP/1.0、 HTTP/1.1 $server_addr 服务器地址
$server_name:    服务器名称
$server_port:     服务器的端口号
$scheme: HTTP 方法（如 http，https）
$uri: root设置的根目录
```

还有更多的内置预定义变量，可以直接搜索关键字「nginx 内置预定义变量」可以看到一堆博客写这个，这些变量都可以在配置文件中直接使用。

## 全局配置

```s
worker_processes 2;

```

## event

## http

### server

```s
server {
  # 当nginx接到请求后，会匹配其配置中的service模块
  # 匹配方法就是将请求携带的host和port去跟配置中的server_name和listen相匹配
  listen       8080;
  server_name  localhost; # 定义当前虚拟主机（站点）匹配请求的主机名

  location / {
      root   html; # Nginx默认值
      # 设定Nginx服务器返回的文档名
      index  index.html index.htm; # 先找根目录下的index.html，如果没有再找index.htm
  }
}
#当一个请求叫做`localhost:8080`请求nginx服务器时，该请求就会被匹配进该代码块的 server{ } 中执行。
```

server 块可以包含多个 location 块，location 指令用于匹配 uri，语法：

```s
location [ = | ~ | ~* | ^~] uri {
	...
}
```

指令后面：

1. `=` 精确匹配路径，用于不含正则表达式的 uri 前，如果匹配成功，不再进行后续的查找；
2. `^~` 用于不含正则表达式的 uri； 前，表示如果该符号后面的字符是最佳匹配，采用该规则，不再进行后续的查找；
3. `~` 表示用该符号后面的正则去匹配路径，区分大小写；
4. `~*` 表示用该符号后面的正则去匹配路径，不区分大小写。跟 `~` 优先级都比较低，如有多个 location 的正则能匹配的话，则使用正则表达式最长的那个；

如果 uri 包含正则表达式，则必须要有 `~` 或 `~*` 标志。

# 应用

## 动静分离

动静分离其实就是 Nginx 服务器将接收到的请求分为**动态请求**和**静态请求**。

静态请求直接从 nginx 服务器所设定的根目录路径去取对应的资源，动态请求转发给真实的后台（前面所说的应用服务器，如图中的 Tomcat）去处理。

这样做不仅能给应用服务器减轻压力，将后台 api 接口服务化，还能将前后端代码分开并行开发和部署。

```s
server {
  server_name  localhost;
  listen 8080 default_server; # 监听8080端口
  listen [::]:80 default_server ipv6only=on; # 开启ipv6

  # 静态访问
  location / {
      root   html; # Nginx默认值
      index  index.html index.htm;
  }

  # 静态化配置，所有静态请求都转发给 nginx 处理，存放目录为 my-project
  location ~ .*\.(html|htm|gif|jpg|jpeg|bmp|png|ico|js|css)$ {
      root /usr/local/var/www/my-project; # 静态请求所代理到的根目录
  }

  # 动态请求匹配到path为'node'的就转发到8002端口处理
  location /node/ {
      proxy_pass http://localhost:8002; # 充当服务代理
  }
  # 设置静态资源跟目录
  root  /home/yinsigan/rails365/current/public; # root 设置默认查找的目录
  try_files $uri/index.html $uri @rails365; # try_files 依次访问目录和文件,查找目标文件
  location @rails365 { # 在 root 里查询不到,执行以下
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;
    proxy_redirect off;
    proxy_pass http://rails365; # 反向代理
  }
}
```

## 负载均衡

在服务器集群中，Nginx 可以将接收到的客户端请求“均匀地”（严格讲并不一定均匀，可以通过设置权重）分配到这个集群中所有的服务器上。这个就叫做**负载均衡**。

- 分摊服务器集群压力
- 保证客户端访问的稳定性

前面也提到了，负载均衡可以解决分摊服务器集群压力的问题。除此之外，Nginx 还带有**健康检查**（服务器心跳检查）功能，会定期轮询向集群里的所有服务器发送健康检查请求，来检查集群中是否有服务器处于异常状态。

一旦发现某台服务器异常，那么在这以后代理进来的客户端请求都不会被发送到该服务器上（直健康检查发现该服务器已恢复正常），从而保证客户端访问的稳定性。

配置一个简单的负载均衡并不复杂，代码如下：

```s
# 负载均衡：设置domain
upstream domain {
    server localhost:8000;
    server localhost:8001;
}

server {
  listen       8080;
  server_name  localhost;

  location / {
      # root   html; # Nginx默认值
      # index  index.html index.htm;

      proxy_pass http://domain; # 负载均衡配置，请求会被平均分配到8000和8001端口
      proxy_set_header Host $host:$server_port;
  }
}
# 轮询（默认） 每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器 down 掉，能自动剔除。
upstream backserver {
  server 192.168.0.14;
  server 192.168.0.15;
}
# 权重查询,指定轮询几率，weight 和访问比率成正比，用于后端服务器性能不均的情况。权重越高，在被访问的概率越大，如上例，分别是 30%，70%。
upstream backserver {
  server 192.168.0.14 weight=3;
  server 192.168.0.15 weight=7;
}
# 3、ip_hash,解决session问题,已登录用户根据hash,访问上一次访问的服务
upstream backserver {
  ip_hash;
  server 192.168.0.14:88;
  server 192.168.0.15:80;
}
# 按后端服务器的响应时间来分配请求，响应时间短的优先分配。
upstream backserver {
    server server1;
    server server2;
    fair;
}
# 按访问 url 的 hash 结果来分配请求，使每个 url 定向到同一个（对应的）后端服务器，后端服务器为缓存时比较有效。
upstream backserver {
    server squid1:3128;
    server squid2:3128;
    hash $request_uri;
    hash_method crc32;
}
upstream backserver{
    ip_hash;
    server 127.0.0.1:9090 down; # (down 表示单前的server暂时不参与负载)
    server 127.0.0.1:8080 weight=2; # (weight 默认为1.weight越大，负载的权重就越大)
    server 127.0.0.1:6060;
    server 127.0.0.1:7070 backup; # (其它所有的非backup机器down或者忙的时候，请求backup机器)
}
```

max_fails ：允许请求失败的次数默认为 1.当超过最大次数时，返回 proxy_next_upstream 模块定义的错误
fail_timeout:max_fails 次失败后，暂停的时间

## 正向代理

例子: A 通过正向代理服务器 B 访问 C: A 请求 B ,B 将请求转发给 C,C 返回数据给 B,B 把数据返回给 A.

使用场景: VPN

```s
server {
  resolver 114.114.114.114;       #指定DNS服务器IP地址
  listen 80;
  location / {
    proxy_pass http://$host$request_uri;     #设定代理服务器的协议和地址
    proxy_set_header HOST $host;
    proxy_buffers 256 4k;
    proxy_max_temp_file_size 0k;
    proxy_connect_timeout 30;
    proxy_send_timeout 60;
    proxy_read_timeout 60;
    proxy_next_upstream error timeout invalid_header http_502;
  }
}
server {
  resolver 114.114.114.114;       #指定DNS服务器IP地址
  listen 443;
  location / {
    proxy_pass https://$host$request_uri;    #设定代理服务器的协议和地址
          proxy_buffers 256 4k;
          proxy_max_temp_file_size 0k;
    proxy_connect_timeout 30;
    proxy_send_timeout 60;
    proxy_read_timeout 60;
    proxy_next_upstream error timeout invalid_header http_502;
  }
}
```

```S
# 测试
curl  -I --proxy 192.168.10.10:80 www.baidu.com
curl  -I --proxy 192.168.10.10:443 www.baidu.com
# 全局代理
# vim /etc/profile
export http_proxy='192.168.10.10:80'
export http_proxy='192.168.10.10:443'
export ftp_proxy='192.168.10.10:80'
```

## 反向代理

​ 反向代理其实就类似你去找代购帮你买东西（浏览器或其他终端向 nginx 请求），你不用管他去哪里买，只要他帮你买到你想要的东西就行（浏览器或其他终端最终拿到了他想要的内容，但是具体从哪儿拿到的这个过程它并不知道）。

1. 保障应用服务器的安全（增加一层代理，可以屏蔽危险攻击，更方便的控制权限）
2. 实现负载均衡（稍等~下面会讲）
3. 实现跨域（号称是最简单的跨域方式）

配置一个简单的反向代理是很容易的，代码如下：

```s
server {
  listen       8080;
  server_name  localhost;

  location / {
      root   html; # Nginx默认值
      index  index.html index.htm;
  }

  proxy_pass http://localhost:8000; # 反向代理配置，请求会被转发到8000端口
}
```

反向代理还有一些其他的指令，可以了解一下：

`proxy_set_header`: 在将客户端请求发送给后端服务器之前，更改来自客户端的请求头信息。
`proxy_connect_timeout`:配置 Nginx 与后端代理服务器尝试建立连接的超时时间。
`proxy_read_timeout`:配置 Nginx 向后端服务器组发出 read 请求后，等待相应的超时时间。
`proxy_send_timeout`:配置 Nginx 向后端服务器组发出 write 请求后，等待相应的超时时间。
`proxy_redirect`:用于修改后端服务器返回的响应头中的 Location 和 Refresh。

### websocket

```s
upstream ws {
  server unix:///home/eason/tt_deploy/shared/tmp/sockets/puma.sock fail_timeout=0;
}
server {
  location /ws/ {
    proxy_pass http://ws;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
  }
}
```

# 示例

## 跨域 header

```s
server {
  listen       80;
  server_name  be.sherlocked93.club;

	add_header 'Access-Control-Allow-Origin' $http_origin;   # 全局变量获得当前请求origin，带cookie的请求不支持*
	add_header 'Access-Control-Allow-Credentials' 'true';    # 为 true 可带上 cookie
	add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';  # 允许请求方法
	add_header 'Access-Control-Allow-Headers' $http_access_control_request_headers;  # 允许请求的 header，可以为 *
	add_header 'Access-Control-Expose-Headers' 'Content-Length,Content-Range';

  if ($request_method = 'OPTIONS') {
		add_header 'Access-Control-Max-Age' 1728000;   # OPTIONS 请求的有效期，在有效期内不用发出另一条预检请求
		add_header 'Content-Type' 'text/plain; charset=utf-8';
		add_header 'Content-Length' 0;

		return 204;                  # 200 也可以
	}

	location / {
		root  /usr/share/nginx/html/be;
		index index.html;
	}
}
```

## 前端静态文件

```s
server{
    #是否启动gzip压缩,on代表启动,off代表开启
    gzip  on;

    #需要压缩的常见静态资源
    gzip_types text/plain application/javascript   application/x-javascript text/css application/xml text/javascript;

    #由于nginx的压缩发生在浏览器端而微软的ie6很坑爹,会导致压缩后图片看不见所以该选
    项是禁止ie6发生压缩
    gzip_disable "MSIE [1-6]\.";

    #如果文件大于1k就启动压缩
    gzip_min_length 200k;

    #以16k为单位,按照原始数据的大小以4倍的方式申请内存空间,一般此项不要修改
    gzip_buffers 4 16k;

    #压缩的等级,数字选择范围是1-9,数字越小压缩的速度越快,消耗cpu就越大
    gzip_comp_level 3;
}

server {
    listen 80;
    server_name www.local.liaoxuefeng.com
    # 静态文件根目录:
    root /path/to/src/main/webapp;
    access_log /var/log/nginx/webapp_access_log;
    error_log  /var/log/nginx/webapp_error_log;
    # 处理静态文件请求:
    location /static {
    }
    # 处理静态文件请求:
    location /favicon.ico {
    }
    # 不允许请求/WEB-INF:
    location /WEB-INF {
        return 404;
    }
    # 其他请求转发给Tomcat:
    location / {
        proxy_pass       http://127.0.0.1:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```
