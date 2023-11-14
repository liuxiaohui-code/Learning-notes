# 路线

linux





# 交叉编译

Golang 支持交叉编译，在一个平台上生成另一个平台的可执行程序，最近使用了一下，非常好用，这里备忘一下。

Mac 下编译 Linux 和 Windows 64位可执行程序

```
CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build main.go
CGO_ENABLED=0 GOOS=windows GOARCH=amd64 go build main.go
```


Linux 下编译 Mac 和 Windows 64位可执行程序

```
CGO_ENABLED=0 GOOS=darwin GOARCH=amd64 go build main.go
CGO_ENABLED=0 GOOS=windows GOARCH=amd64 go build main.go
```


Windows 下编译 Mac 和 Linux 64位可执行程序

```
SET CGO_ENABLED=0
SET GOOS=darwin
SET GOARCH=amd64
go build main.go

SET CGO_ENABLED=0
SET GOOS=linux
SET GOARCH=amd64
go build main.go
```

GOOS：目标平台的操作系统（darwin、freebsd、linux、windows）
GOARCH：目标平台的体系架构（386、amd64、arm）
交叉编译不支持 CGO 所以要禁用它

上面的命令编译 64 位可执行程序，你当然应该也会使用 386 编译 32 位可执行程序
很多博客都提到要先增加对其它平台的支持，但是我跳过那一步，上面所列的命令也都能成功，且得到我想要的结果，可见那一步应该是非必须的，或是我所使用的 Go 版本已默认支持所有平台。

# internal文件夹

项目的internal文件件里的包，私有，是不能被第三方调用的。

解决方法：拷贝一份，重命名为 internal2或其他

# 解决 exec:"gcc" executable file not found in %PATH%报错问题

https://blog.csdn.net/xia_2017/article/details/105545789

这是因为Windows系统上没有GCC编译器。而编译代码中的包里面可能需要用到gcc编译器。

解决办法如下：

下载链接：https://sourceforge.net/projects/mingw-w64/files/mingw-w64/

个人建议：不要下载exe文件，网不好安装下载资源包很费劲。还是下载64位x86_64-posix-seh压缩包，直接解压就行。

安装或是解压完之后，配置环境变量。将安装路径对应的bin目录添加到PATH环境变量中。

查看是否安装成功。gcc -v

重启自己的语言编译工具。然后重新build就好了





```
http://localhost:8365/signhash?hash=MfZ6GPsp0SmW1f30uGSWRwDFiEU%3D&sign_type=PKCS%231&callback=jsonp_431de7183f59b0


{
	code:'', //number
	data:'', //string object array...
	message:'',//string
}
```

