# vscode

## 快捷键

ctrl+shift+p vscode 命令行;去除':'可搜索工作空间文件;

## 插件

1. 翻译
   1. Comment Translate
   2. 翻译（英汉词典）
2. 前端
   1. Auto close tag
   2. Auto Rename tag
3. c++
4. java
5. go
6. Text Edits 将选中文本转为大写/小写
7. One Monokai Theme 主题

## 配置

### 自动换行

```
**文件>首选项>设置**
Editor:Word Wrap
```

## 切换默认终端

ctrl + shift +p

> Select Default Profile
> 选择目标“Command Prompt”
> 快捷键（ctrl+` ）调出终端，如图所示已经默认 cmd

## 搜索正则表达式

(X\w+X)

alt+r

正则表达式用`()`包裹,替换时`$1`表示正则匹配的结果

## vue 安装

```sh
#1. vetur插件的安装
该插件是vue文件基本语法的高亮插件，在插件窗口中输入vetur点击安装插件就行，装好后点击文件->首选项->设置 打开设置界面，在设置界面右侧添加配置

"emmet.syntaxProfiles": {
  "vue-html": "html",
  "vue": "html"
}

#2、eslint插件的安装
eslint智能错误检测插件，在具体开发中作用很大，能够及时的帮我们发现错误。至于安装，同样打开插件扩展窗口输入eslint点击安装插件，装好后也需要进行配置，在同vetur插件一样的地方进行配置

"eslint.validate": [
        "javascript",
        "javascriptreact",
        "html",
        "vue"
    ],

 "eslint.options": {
        "plugins": ["html"]
}

#3. npm install
	npm run dev

#4. npm 安装
$ cd /usr/local
$ mkdir node
$ cd node
$ wget https://npm.taobao.org/mirrors/node/v4.4.7/node-v4.4.7-linux-x64.tar.gz
$ ls -l
$ tar -zxvf node-v4.4.7-linux-x64.tar.gz
$ pwd
$ ls
	# 创建sfot link
$ ln -s /usr/local/node/node-v4.4.7-linux-x64/bin/npm /usr/local/bin/npm
$ ln -s /usr/local/node/node-v4.4.7-linux-x64/bin/node /usr/local/bin/node
		#npm install 安装过慢的解决方案：
一，简单省事版
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.38.0/install.sh | bash

wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.38.0/install.sh | bash
因为资源点在国外，所以安装那些包要访问外网，而该命令可以改变装包用的资源点，使访问外网变为访问国内网
npm install -gd express --registry=http://registry.npm.taobao.org
npm install -gd express --registry=http://registry.npm.taobao.org
需要使用–registry参数指定镜像服务器地址，为了避免每次安装都需要–registry参数，可以使用如下命令进行永久设置：
npm config set registry http://registry.npm.taobao.org
```

2.全局安装 vue-cli，vue-cli 可以帮助我们快速构建 Vue 项目。

安装命令：

```
npm install -g vue-cli
```

打开 VScode 的终端，调出命令输入框。点击终端-新建终端，输入上述命令，回车，等待安装完成。

3.安装 webpack，它是打包 js 的工具

安装命令：

```
npm install -g webpack
```

安装方法同上。

4.安装完成之后就可以开始创建 vue 项目，首先创建一个文件夹用来存放你的项目，用 vscode 打开对应的文件夹，并在终端 cd 到对应的文件夹。比如我的文件夹就是 myvue

创建项目命令，输入回车:

```
vue init webpack myvue
```

其中 myvue 就是项目名称，根据喜好自己取。接着会出现一些配置项，可以根据需要配置，也可以默认，直接按回车。然后继续等待安装依赖项。完成之后，一个基本的 vue 项目就搭建完了。完成之后的 vscode 左边可以看到如下目录，其中 main.js 就是入口。

# 编译速度缓慢

```
**方法1：修复VScode 造成 rg.exe内存占用过大的问题**
“文件”菜单->“首选项”->“设置”
在搜索框中输入“Search:Follow Symlinks”搜索，将该项之前的复选框√去掉；如下图所示。
方法2：修复VScode 造成 git.exe内存占用过大的问题
2）在搜索框中输入“Git:Autorefresh”搜索，将该项之前的复选框√去掉；
```

# lanch.json

```json
# VS code的帮助https://code.visualstudio.com/docs/editor/tasks#vscode
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "golang",
            "type": "go",
            "request": "launch",
            "mode": "auto",
            //当运行单个文件时{workspaceFolder}可改为{file}
            "program": "${workspaceFolder}",
            "env": {},
            "args": []
        }
    ]
}


${workspaceRoot} 				当前打开的文件夹的绝对路径+文件夹的名字
${workspaceRootFolderName}   	当前打开的文件夹的名字
${file} 						当前打开正在编辑的文件名，包括绝对路径，文件名，文件后缀名
${relativeFile} 				从当前打开的文件夹到当前打开的文件的路径
# 如 当前打开的是test文件夹，当前的打开的是main.c，并有test / first / second / main.c,那么此变量代表的是  first / second / main.c
${fileBasename}  				当前打开的文件名+后缀名，不包括路径
${fileBasenameNoExtension} 		当前打开的文件的文件名，不包括路径和后缀名
${fileDirname} 					当前打开的文件所在的绝对路径，不包括文件名
${fileExtname} 					当前打开的文件的后缀名
${cwd} 							the task runner's current working directory on startup 不知道怎么描述，这是原文解释，跟 cmd 里面的 cwd 是一样的
${lineNumber}  					当前打开的文件，光标所在的行数
```

# c/c++

```s
1. ctrl+shift+p
2. 输入 c/c++
3. 选择 "Edit Configuration(UI)"
4. 配置编译器路径:  ~/g++.exe

配置完成后，此时在侧边栏可以发现多了一个.vscode文件夹，并且里面有一个c_cpp_properties.json文件，内容如下，说明上述配置成功。现在可以通过Ctrl+<`快捷键打开内置终端并进行编译运行了。
{
    "configurations": [
        {
            "name": "Win32",
            "includePath": [
                "${workspaceFolder}/**"
            ],
            "defines": [
                "_DEBUG",
                "UNICODE",
                "_UNICODE"
            ],
            //此处是编译器路径，以后可直接在此修改
            "compilerPath": "D:/mingw-w64/x86_64-8.1.0-win32-seh-rt_v6-rev0/mingw64/bin/g++.exe",
            "cStandard": "c11",
            "cppStandard": "c++17",
            "intelliSenseMode": "gcc-x64"
        }
    ],
    "version": 4
}

5. 接下来，创建一个tasks.json文件来告诉VS Code如何构建（编译）程序。该任务将调用g++编译器基于源代码创建可执行文件。 按快捷键Ctrl+Shift+P调出命令面板，输入tasks，选择“Tasks:Configure Default Build Task”：

再选择“C/C++: g++.exe build active file”：

此时会出现一个名为tasks.json的配置文件，内容如下：

{
    // See https://go.microsoft.com/fwlink/?LinkId=733558
    // for the documentation about the tasks.json format
    "version": "2.0.0",
    "tasks": [
        {
            "type": "shell",
            "label": "g++.exe build active file",//任务的名字，就是刚才在命令面板中选择的时候所看到的，可以自己设置
            "command": "D:/mingw-w64/x86_64-8.1.0-win32-seh-rt_v6-rev0/mingw64/bin/g++.exe",
            "args": [//编译时候的参数
                "-g",//添加gdb调试选项
                "${file}",
                "-o",//指定生成可执行文件的名称
                "${fileDirname}\\${fileBasenameNoExtension}.exe"
            ],
            "options": {
                "cwd": "D:/mingw-w64/x86_64-8.1.0-win32-seh-rt_v6-rev0/mingw64/bin"
            },
            "problemMatcher": [
                "$gcc"
            ],
            "group": {
                "kind": "build",
                "isDefault": true//表示快捷键Ctrl+Shift+B可以运行该任务
            }
        }
    ]
}

6. 这里主要是为了在.vscode文件夹中产生一个launch.json文件，用来配置调试的相关信息。点击菜单栏的Debug-->Start Debugging：

选择C++(GDB/LLDB)：

紧接着会产生一个launch.json的文件：
```

# 问题

## 1. 长行解析

VS Code 出于性能原因，对长行跳过令牌化。长行的长度可通过 “editor.maxTokenizationLineLength“ 进行配置

**解决：管理 >> 设置 >> 搜索"editor.maxTokenizationLineLength" >> 输入 1000000000000000000000000000000000000**

## 3. 前端无法点击跳转@路径

根目录添加 jsconfig.json

```json
{
  "compilerOptions": {
    "target": "ES6",
    "module": "commonjs",
    "allowSyntheticDefaultImports": true,
    "baseUrl": "./",
    "paths": {
      "@/*": ["src/*"]
    }
  },
  "exclude": ["node_modules"]
}
```
