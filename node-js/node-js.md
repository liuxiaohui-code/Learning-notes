# 介绍

Node.js 是一个开源和跨平台的 JavaScript 运行时环境。它是几乎任何类型的项目的流行工具！

Node.js 在浏览器之外运行 Google Chrome 的核心 V8 JavaScript 引擎。这使得 Node.js 非常高效。

Node.js 应用程序在单个进程中运行，无需为每个请求创建新线程。Node.js 在其标准库中提供了一组异步 I/O 原语，可防止 JavaScript 代码阻塞，通常，Node.js 中的库是使用非阻塞范例编写的，使阻塞行为成为例外而不是常态。

当 Node.js 执行 I/O 操作时，例如从网络读取、访问数据库或文件系统，而不是阻塞线程并浪费 CPU 周期等待，Node.js 将在响应返回时恢复操作。

这允许 Node.js 处理与单个服务器的数千个并发连接，而不会引入管理线程并发的负担，这可能是错误的重要来源。

Node.js 具有独特的优势，因为数百万为浏览器编写 JavaScript 的前端开发人员现在能够在编写客户端代码的同时编写服务器端代码，而无需学习完全不同的语言。

在 Node.js 中，可以毫无问题地使用新的 ECMAScript 标准，因为您不必等待所有用户更新他们的浏览器 - 您可以通过更改 Node.js 版本来决定使用哪个 ECMAScript 版本，您还可以通过运行带有标志的 Node.js 来启用特定的实验功能。

# 安装

## nvm

### 安装

安装前先彻底接卸本地 nodejs

```shell
download address ： https://nodejs.org/en/download/
```

在 nvm 安装目录，找到 setting.txt 文件加上如下两行：

```shell
node_mirror: https://npm.taobao.org/mirrors/node/
npm_mirror: https://npm.taobao.org/mirrors/npm/
```

### 问题

问题 1：安装目录 英文且不允许空格

问题 2：exit status 1:乱码，或出现 exit status 1: You do not have sufficient privilege to perform this operation.

solution：用管理员身份打开 cmd 命令行工具，再执行 nvm use \*\*\*即可。

### 使用

```
Usage:
  nvm on                       : Enable node.js version management.
  nvm off                      : Disable node.js version management.
  nvm proxy [url]              : Set a proxy to use for downloads. Leave [url] blank to see the current proxy.
                                 Set [url] to "none" to remove the proxy.
  nvm node_mirror [url]        : Set the node mirror. Defaults to https://nodejs.org/dist/. Leave [url] blank to use default url.
  nvm npm_mirror [url]         : Set the npm mirror. Defaults to https://github.com/npm/cli/archive/. Leave [url] blank to 	default url.

  nvm root [path]              : Set the directory where nvm should store different versions of node.js.
                                 If <path> is not set, the current root will be displayed.

  nvm version                  : Displays the current running version of nvm for Windows. Aliased as v.
```

### 查看

```bash
nvm list available   # 显示所有可用版本
nvm list # 显示已安装版本
nvm current # 显示当前版本
nvm arch #显示当前操作系统位数
```

### 安装

```sh
nvm install <版本号> [32|64|all|置空选默认本地操作系统位数]
nvm install lst  # 安装最新的长期支持的版本
nvm install latest # 安装最新版本
```

### 使用

```bash
# 使用admin打开cmd执行
nvm use <版本号> [32|64]
nvm use latest|tls|newest  # newest 安装已安装的版本中最新的版本
```

### 卸载

```
nvm uninstall <version>
```

## npm

NPM 是随同 NodeJS 一起安装的包管理工具，能解决 NodeJS 代码部署上的很多问题，常见的使用场景有以下几种：

- 允许用户从 NPM 服务器下载别人编写的第三方包到本地使用。
- 允许用户从 NPM 服务器下载并安装别人编写的命令行程序到本地使用。
- 允许用户将自己编写的包或命令行程序上传到 NPM 服务器供别人使用。

### 1.查看路径

```shell
npm root -g  #查看全局node_modules路径
npm config set prefix "目录路径"  #设置全局路径
npm config get prefix  #查看全局路径

npm install -g  <package> #安装到全局路径
npm install  <package>  #安装到当前文件夹下的node_modules文件夹

npm cache clear #清理缓存

npm config ls -l  ##　查看所有配置项
npm config get cache  ## 查看缓存配置，get后面可以跟任意配置项
npm config edit  ## 直接编辑config文件，这个会打开文本
```

### 2.换源

```shell
# 淘宝
npm config set registry https://registry.npm.taobao.org
#验证
npm config get registry
```

### 3.升级

```
npm install npm -g
```

### 4.安装模块

```sh
 npm install <Module Name>  #本地安装到node_modules，使用require引入
 npm install <Module Name> -g #全局安装
 npm install <pkg> --save #安装并作为依赖使用
 #查看所有全局安装的模块
 		npm list -g
 #查看某个模块的版本号
 		npm list grunt
 #卸载 Node.js 模块
 		npm uninstall <Module Name>
 #卸载后，你可以到 /node_modules/ 目录下查看包是否还存在
 		npm ls
 #更新
 		npm update <Module Name>
 #搜索模块
 		npm search <Module Name>
 #创建模块
 		npm init #生成package.json
 		npm adduser
 		npm publish
 		npm unpublish <package>@<version>可以撤销发布自己发布过的某个版本代码
 #版本号
 		NPM使用语义版本号来管理代码，语义版本号分为X.Y.Z三位，分别代表主版本号、次版本号和补丁版本号。当代码变更时，版本号按以下原则更新。
		- 如果只是修复bug，需要更新Z位。
    - 如果是新增了功能，但是向下兼容，需要更新Y位。
		- 如果有大变动，向下不兼容，需要更新X位。
```

### 5.问题

#### 1 npm err! Error: connect ECONNREFUSED 127.0.0.1:8087

```sh
npm config set proxy null
```

### 6.package.json

#### 属性说明

- **name** - 包名。
- **version** - 包的版本号。
- **description** - 包的描述。
- **homepage** - 包的官网 url 。
- **author** - 包的作者姓名。
- **contributors** - 包的其他贡献者姓名。
- **dependencies** - 依赖包列表。如果依赖包没有安装，npm 会自动将依赖包安装在 node_module 目录下。
- **repository** - 包代码存放的地方的类型，可以是 git 或 svn，git 可在 Github 上。
- **main** - main 字段指定了程序的主入口文件，require('moduleName') 就会加载这个文件。这个字段的默认值是模块根目录下面的 index.js。
- **keywords** - 关键字

#### 生成

```sh
npm init

This utility will walk you through creating a package.json file.
It only covers the most common items, and tries to guess sensible defaults.

See `npm help json` for definitive documentation on these fields
and exactly what they do.

Use `npm install <pkg> --save` afterwards to install a package and
save it as a dependency in the package.json file.

Press ^C at any time to quit.
name: (node_modules) runoob                   # 模块名
version: (1.0.0)
description: Node.js 测试模块(www.runoob.com)  # 描述
entry point: (index.js)
test command: make test
git repository: https://github.com/runoob/runoob.git  # Github 地址
keywords:
author:
license: (ISC)
About to write to ……/node_modules/package.json:      # 生成地址

{
  "name": "runoob",
  "version": "1.0.0",
  "description": "Node.js 测试模块(www.runoob.com)",
  ……
}


Is this ok? (yes) yes
```

## node

### 执行程序

```SH
node <file_name.js>  #运行
ctrl-c #中止
```

中止程序

```js
process.exit()
process.exitCode = 1
process.exit(1) //default 0
```

### PEPL

Node.js REPL(Read Eval Print Loop:交互式解释器) 表示一个电脑的环境，类似 Window 系统的终端或 Unix/Linux shell，我们可以在终端中输入命令，并接收系统的响应。

Node 自带了交互式解释器，可以执行以下任务：

- **读取** - 读取用户输入，解析输入的 Javascript 数据结构并存储在内存中。
- **执行** - 执行输入的数据结构
- **打印** - 输出结果
- **循环** - 循环操作以上步骤直到用户两次按下 **ctrl-c** 按钮退出。

```sh
$ node
>  1+1           #输入js代码
2	#打印结果
>	_   #可以使用下划线(_)获取上一个表达式的运算结果
2
```

命令

```sh
ctrl + c - 退出当前终端。
ctrl + c 按下两次 - 退出 Node REPL。
ctrl + d - 退出 Node REPL.
向上/向下 键 - 查看输入的历史命令
tab 键 - 列出当前命令
.help - 列出使用命令
.break - 退出多行表达式
.clear - 退出多行表达式
.save filename - 保存当前的 Node REPL 会话到指定文件
.load filename - 载入当前 Node REPL 会话的文件内容。
```

### 前端调用 nodejs 库 x

只能在本地使用，并不能在前端使用

```sh
#安装工具
npm install -g browserify
# 安装nodejs库
npm install uniq

vim index.js  #创建入口文件
:wq  #空文件即可

browserify -r uniq index.js > bundle.js  #生成

# 前端引入
<script type="text/javascript" src="bundle.js"></script>
```

# 组成

一个 node.js 的组成部分：

1. **引入 required 模块：**我们可以使用**require**指令来载入 Node.js 模块。
2. **创建服务器：**服务器可以监听客户端的请求，类似于 Apache 、Nginx 等 HTTP 服务器。
3. **接收请求与响应请求**：服务器很容易创建，客户端可以使用浏览器或终端发送 HTTP 请求，服务器接收请求后返回响应数据。

```js
var http = require("http")

http
  .createServer(function (request, response) {
    // 发送 HTTP 头部
    // HTTP 状态值: 200 : OK
    // 内容类型: text/plain
    response.writeHead(200, { "Content-Type": "text/plain" })

    // 发送响应数据 "Hello World"
    response.end("Hello World\n")
  })
  .listen(8888)

// 终端打印如下信息
console.log("Server running at http://127.0.0.1:8888/")
```

# 标准库

https://nodejs.org/api/

## 回调函数

node-js 通过回调函数实现异步处理

```js
var fs = require("fs");

fs.readFile('input.txt', function (err, data) {
   if (err){
      console.log(err.stack);
      return;
   }
   console.log(data.toString());
});
console.log("程序执行完毕");

//输出
程序执行完毕
菜鸟教程官网地址：www.runoob.com
//或
程序执行完毕
Error: ENOENT, open 'input.txt'
```

## events

Node.js 所有的异步 I/O 操作在完成时都会发送一个事件到事件队列。

Node.js 里面的许多对象都会分发事件：一个 net.Server 对象会在每次有新连接时触发一个事件， 一个 fs.readStream 对象会在文件被打开的时候触发一个事件。 所有这些产生事件的对象都是 events.EventEmitter 的实例。

events 模块只提供了一个对象： events.EventEmitter。EventEmitter 的核心就是事件触发与事件监听器功能的封装。

# 第三方

## 自动重启

只要应用程序发生更改，必须在 Bash 中重新执行节点命令，以自动重新启动应用程序，使用 Nodemon 模块。将全局安装到系统路径的 Nodemon 模块

```sh
npm i -g nodemon

npm i --save-dev nodemon
```

使用

```
nodemon app.js
```

https://www.runoob.com/nodejs/nodejs-express-framework.html

# 官网教程

https://nodejs.dev/learn
