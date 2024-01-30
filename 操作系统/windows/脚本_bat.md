# 脚本\_bat

1. 脚本文件以 `.bat` 结尾
2. 脚本里可以使用系统已安装的应用程序
3. 脚本运行方式
   1. 鼠标双击
   2. 在 cmd 命令行里运行`绝对路径+回车`或`相对路径+回车`或`cd bat_root_path & bat_name`,可以不用带后缀 bat
4. 命令不区分大小写

## 打开 cmd

1. 开始 + 系统+ 命令提示符
2. win+r cmd
3. 任意文件夹下，shift+鼠标右键
4. 文件导航地址栏 cmd
5. 管理员方式运行

常用终端操作

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
```

## 文档

[windows 命令大全]() https://learn.microsoft.com/zh-cn/windows-server/administration/windows-commands/windows-commands

## 注释

1. 行注释 `::` , `rem`
2. 块注释`goto flag_name ...(text) :flag_name`

```bat
:: 注释
rem+空格+注释

::块注释
goto xx
注释内容
:xx
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

## 脚本格式

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
