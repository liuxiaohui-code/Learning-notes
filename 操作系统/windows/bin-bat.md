# 打开 cmd

1. 开始 + 系统+ 命令提示符
2. win+r cmd
3. 任意文件夹下，shift+鼠标右键
4. 文件导航地址栏 cmd
5. 管理员方式运行

# 基础命令

1. 命令不区分大小写

```bat
::查看帮助
命令 /?
:: 退出终端
exit
:: 启动一个单独的窗口以运行指定的程序或命令
start
:: #查看windows激活权限
slmgr.vbs /xpr
::   清屏
cls
%注释%
::注释
rem+空格+注释
::块注释
goto xx
注释内容
:xx
```

# 目录与文件相关

## 切换与查看

### cd|chdir

```bat
:: 显示当前路径
cd
::盘符切换
C:
::不同盘符切换布鲁
cd /d 盘符:\路径
::切换目录
cd 目录名
:: 返回上级布鲁
cd ..

:: 也可以使用 chdir 命令,用法与cd相同

:: 如果命令扩展被启用，CHDIR 会如下改变:
:: 1. 当前的目录字符串会被转换成使用磁盘名上的大小写。所以，如果磁盘上的大小写如此，CD C:\TEMP 会将当前目录设为C:\Temp。
:: 2. CHDIR 命令不把空格当作分隔符，因此有可能将目录名改为一个带有空格但不带有引号的子目录名。例如: cd \winnt\profiles\username\programs\start menu 与下列相同:     cd "\winnt\profiles\username\programs\start menu"

```

### dir

```bat
::查看目录下文件
dir
dir /a
```

### tree

```bat
::树形结构查看当前所有文件
tree
```

## 文件创建与删除

### cd> 创建文件

### md 创建目录

### del 删除文件

### rd 删除目录

```bat
:: 创建文件夹
md 文件夹名
:: 创建文件
cd> 文件名
:: 删除文件
del 文件名
:: 删除文件夹
rd 文件夹
```

## 复制与剪贴

### copy 复制

```bat
copy  源文件路径 目标文件路径
```

### move 移动

```bat
move  源文件路径 目标文件路径
```

### ren 重命名

```bat
ren  源文件路径 目标文件路径
```

## 文件查找

### where

```s
# 显示符合搜索模式的文件位置。在默认情况下，搜索是在当前目录和 PATH 环境变量指定的路径中执行的。
where [/R dir] [/Q] [/F] [/T] pattern...
```

# 系统相关

## Systeminfo 系统信息

## set 环境变量

## path

## 打开系统应用

```s
calc #计算器
mspaint #画图
notepad #记事本
explorer # 资源管理器
dvdplay # DVD播放器
taskmgr   		#打开任务管理器
gpedit.msc		#打开本地组策略编辑器
services.ms	#打开服务
regedit        #打开注册表
winver			#检查Windows版本
mstsc 			#远程桌面连接
mmc 			#打开控制台
chkdsk.exe     #打开Chkdsk磁盘检查
osk			#打开屏幕键盘

```

# 网络相关

## ipconfig ip 适配器信息

```bat
:: 查看电脑ip
ipconfig [/allcompartments] [选项]

选项:
       /?               显示此帮助消息
       /all             显示完整配置信息。
       /release         释放指定适配器的 IPv4 地址。
       /release6        释放指定适配器的 IPv6 地址。
       /renew           更新指定适配器的 IPv4 地址。
       /renew6          更新指定适配器的 IPv6 地址。
       /flushdns        清除 DNS 解析程序缓存。
       /registerdns     刷新所有 DHCP 租用并重新注册 DNS 名称
       /displaydns      显示 DNS 解析程序缓存的内容。
       /showclassid     显示适配器允许的所有 DHCP 类 ID。
       /setclassid      修改 DHCP 类 ID。
       /showclassid6    显示适配器允许的所有 IPv6 DHCP 类 ID。
       /setclassid6     修改 IPv6 DHCP 类 ID。
ipconfig                       ... 显示信息
ipconfig /all                  ... 显示详细信息
ipconfig /renew                ... 更新所有适配器
ipconfig /renew EL*            ... 更新所有名称以 EL 开头的连接
ipconfig /release *Con*        ... 释放所有匹配的连接，例如“有线以太网连接 1”或“有线以太网连接 2”
ipconfig /allcompartments      ... 显示有关所有隔离舱的信息
ipconfig /allcompartments /all ... 显示有关所有隔离舱的详细信息
```

## ping 网络联通

## nslookup dns 解析

## tracert 路由追踪

## netsh 连接情况

## netstat 协议统计与 tcp/ip 信息

# bat 脚本

[博客](https://blog.csdn.net/chuangxin/article/details/104100725)

```bat
:: 关闭之后所有命令的回显，不然bat文件中每条指令会在cmd命令窗口显示
@echo off
rem This is a "Hello World" program.
:: 输出
echo Hello World!
:: 输出空白行
echo=

rem 变量无需声明可直接引用，其值为空字符串，并且大小写不敏感。可使用defined关键字或是否为空字符串""判断变量是否为空
rem set var2="var2"
if not defined var2 (
    echo var2 is not defined, the value is: %var2%
) else (
    echo var2 is defined, the value is: %var2%
)

if "%var2%"=="" (
    echo var2 is not defined, the value is: %var2%
) else (
    echo var2 is defined, the value is: %var2%
)

```

## 变量相关

```bat
:: 变量定义赋值,全局变量,一个脚本都可以使用;
:: 变量赋值时等号前后不能有空格
set var2="var2"
:: 清除变量
set var2=
:: 输出 2+2
set var1=2+2
:: /a 是表达式运算，仅适合32位整型运算，可以是负数;输出4
set /a var2=2+2
:: 提示输入,将输入的值赋值给变量
set /p var3=Please input a number:
:: 读取md5文件内容并赋值给md5变量
set /p md5=<file_info.md5
:: 读取变量
echo var1: %var1%


```

# 问题

## cmd 乱码

```bat
:: 查看编码方式
chcp
# 936 gbk
# 65001 utf-8
:: 切换
chcp 65001
# 永久改变
# 状态栏-右键-选项-丢弃旧的副本
```
