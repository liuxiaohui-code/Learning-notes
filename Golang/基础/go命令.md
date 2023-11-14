# go

```s
$ go
Go is a tool for managing Go source code.

Usage:

    go command [arguments]

The commands are:

    build       compile packages and dependencies
    clean       remove object files
    doc         show documentation for package or symbol
    env         print Go environment information
    bug         start a bug report
    fix         run go tool fix on packages
    fmt         run gofmt on package sources
    generate    generate Go files by processing source
    get         download and install packages and dependencies
    install     compile and install packages and dependencies
    list        list packages
    run         compile and run Go program
    test        test packages
    tool        run specified go tool
    version     print Go version
    vet         run go tool vet on packages

Use "go help [command]" for more information about a command.

Additional help topics:

    c           calling between Go and C
    buildmode   description of build modes
    filetype    file types
    gopath      GOPATH environment variable
    environment environment variables
    importpath  import path syntax
    packages    description of package lists
    testflag    description of testing flags
    testfunc    description of testing functions

Use "go help [topic]" for more information about that topic.
```

go env 用于打印 Go 语言的环境信息。

go run 命令可以编译并运行命令源码文件。

go get 可以根据要求和实际情况从互联网上下载或更新指定的代码包及其依赖包，并对它们进行编译和安装。

go build 命令用于编译我们指定的源码文件或代码包以及它们的依赖包。

go install 用于编译并安装指定的代码包及它们的依赖包。

go clean 命令会删除掉执行其它命令时产生的一些文件和目录。

go doc 命令可以打印附于 Go 语言程序实体上的文档。我们可以通过把程序实体的标识符作为该命令的参数来达到查看其文档的目的。

go test 命令用于对 Go 语言编写的程序进行测试。

go list 命令的作用是列出指定的代码包的信息。

go fix 会把指定代码包的所有 Go 语言源码文件中的旧版本代码修正为新版本的代码。

go vet 是一个用于检查 Go 语言源码中静态错误的简单工具。

go tool pprof 命令来交互式的访问概要文件的内容。

# go mod

```s
go mod [command]

command:
  download    download modules to local cache
  edit        edit go.mod from tools or scripts
  graph       print module requirement graph
  init <包名>  # 初始化项目
  tidy        # 添加缺失的包并移除不使用的包
  vendor      # 将依赖下载到根目录的 vendor 文件夹
  verify      verify dependencies have expected content
  why         explain why packages or modules are needed

```

# go build

```S
go build [-o 输出名] [-i] [编译标记] [包名]

-o <[路径]输出可执行文件名> # 输出名，可以包含路径，默认路径在执行命令的路径
-i # 标志安装作为目标依赖项的包。不推荐使用-i标志。
```

# build flags

build, clean, get, install, list, run, test

```s
-a
    完全编译，不理会-i产生的.a文件(文件会比不带-a的编译出来要大？)
-n
    仅打印输出build需要的命令，不执行build动作（少用）。
-p n
    开多少核cpu来并行编译，默认为本机CPU核数（少用）。GOMAXPROCS
-race
    同时检测数据竞争状态，只支持 linux/amd64, freebsd/amd64, darwin/amd64 和 windows/amd64.
-msan
    启用与内存消毒器的互操作。仅支持linux / amd64，并且只用Clang / LLVM作为主机C编译器（少用）。
-v
    打印出被编译的包名（少用）.
-work
    打印临时工作目录的名称，并在退出时不删除它（少用）。
-x
    同时打印输出执行的命令名（-n）（少用）.
-asmflags 'flag list'
    传递每个go工具asm调用的参数（少用）
-buildmode mode
    编译模式（少用）
    'go help buildmode'
-compiler name
    使用的编译器 == runtime.Compiler
    (gccgo or gc)（少用）.
-gccgoflags 'arg list'
    gccgo 编译/链接器参数（少用）
-gcflags 'arg list'
    垃圾回收参数（少用）.
-installsuffix suffix
    a suffix to use in the name of the package installation directory,
    in order to keep output separate from default builds.
    If using the -race flag, the install suffix is automatically set to race
    or, if set explicitly, has _race appended to it.  Likewise for the -msan
    flag.  Using a -buildmode option that requires non-default compile flags
    has a similar effect.
-ldflags 'flag list'
    '-s -w': 压缩编译后的体积
    -s: 去掉符号表
    -w: 去掉调试信息，不能gdb调试了
-linkshared
    链接到以前使用创建的共享库
    -buildmode=shared.
-pkgdir dir
    从指定位置，而不是通常的位置安装和加载所有软件包。例如，当使用非标准配置构建时，使用-pkgdir将生成的包保留在单独的位置。
-tags 'tag list'
    构建出带tag的版本.
-toolexec 'cmd args'
    a program to use to invoke toolchain programs like vet and asm.
    For example, instead of running asm, the go command will run
    'cmd args /path/to/asm <arguments for asm>'.
```

# go test

`go test`命令是一个按照一定约定和组织的测试代码的驱动程序。

| 类型     | 格式                   | 作用                           |
| -------- | ---------------------- | ------------------------------ |
| 测试函数 | 函数名前缀为 Test      | 测试程序的一些逻辑行为是否正确 |
| 基准函数 | 函数名前缀为 Benchmark | 测试函数的性能                 |
| 示例函数 | 函数名前缀为 Example   | 为文档提供示例文档             |

go test 命令会遍历所有的`*_test.go`文件中符合上述命名规则的函数，然后生成一个临时的 main 包用于调用相应的测试函数，然后构建并运行、报告测试结果，最后清理测试中生成的临时文件。

`go test`的参数解读：

`go test`是 go 语言自带的测试工具，其中包含的是两类，单元测试和性能测试

通过`go help test`可以看到 go test 的使用说明：

格式形如：` go test [-c] [-i] [build flags] [packages] [flags for test binary]`

参数解读：

```S

```

`-c` : 编译 go test 成为可执行的二进制文件，但是不运行测试。

`-i `: 安装测试包依赖的 package，但是不运行测试。

关于`build flags`，调用`go help build`，这些是编译运行过程中需要使用到的参数，一般设置为空

关于`packages`，调用 g`o help packages`，这些是关于包的管理，一般设置为空

关于`flags for test binary`，调用`go help testflag`，这些是 go test 过程中经常使用到的参数

`-test.v `: 是否输出全部的单元测试用例（不管成功或者失败），默认没有加上，所以只输出失败的单元测试用例。

`-test.run pattern`: 只跑哪些单元测试用例

`-test.bench patten`: 只跑那些性能测试用例

`-test.benchmem `: 是否在性能测试的时候输出内存情况

`-test.benchtime t` : 性能测试运行的时间，默认是 1s

`-test.cpuprofile cpu.out `: 是否输出 cpu 性能分析文件

`-test.memprofile mem.out` : 是否输出内存性能分析文件

`-test.blockprofile block.out` : 是否输出内部 goroutine 阻塞的性能分析文件

`-test.memprofilerate n` : 内存性能分析的时候有一个分配了多少的时候才打点记录的问题。这个参数就是设置打点的内存分配间隔，也就是 profile 中一个 sample 代表的内存大小。默认是设置为 512 \* 1024 的。如果你将它设置为 1，则每分配一个内存块就会在 profile 中有个打点，那么生成的 profile 的 sample 就会非常多。如果你设置为 0，那就是不做打点了。

你可以通过设置 memprofilerate=1 和 GOGC=off 来关闭内存回收，并且对每个内存块的分配进行观察。

`-test.blockprofilerate n`: 基本同上，控制的是 goroutine 阻塞时候打点的纳秒数。默认不设置就相当于`-test.blockprofilerate=1`，每一纳秒都打点记录一下

`-test.parallel n `: 性能测试的程序并行 cpu 数，默认等于 GOMAXPROCS。

`-test.timeout t `: 如果测试用例运行时间超过 t，则抛出 panic

`-test.cpu 1,2,4 `: 程序运行在哪些 CPU 上面，使用二进制的 1 所在位代表，和 nginx 的`nginx_worker_cpu_affinity`是一个道理

`-test.short` : 将那些运行时间较长的测试用例运行时间缩短

# go get

```s

usage: go get [-d] [-t] [-u]  [build flags] <packages>

Get 将其命令行参数解析为特定模块版本的包，更新 go.mod 需要这些版本，下载源代码到模块缓存，然后生成并安装命名包。

除公共标志外:

-t # 标志指令开始考虑构建测试所需的模块在命令行上指定的包。
-u # 指示 get 更新提供命令行上命名的软件包依赖项的模块，以便在可用时使用较新的次要版本或修补程序版本。
-u=patch  # 指示 get 更新依赖项，但将默认值更改为选择修补程序版本。
-u -t # 也将更新测试依赖项
-d # 指示 get 不要构建或安装软件包。get 只会更新 go.mod 并下载构建软件包所需的源代码。

使用 get 构建和安装包已弃用。在将来的版本中，默认情况下将启用 -d 标志，并且“go get”将仅用于调整当前模块的依赖项。要使用当前模块中的依赖项安装软件包，请使用“go install”。要安装忽略当前模块的软件包，请在每个参数后使用带有@version后缀（如“@latest”）的“go install”。

```

示例:

```s
go get example.com/pkg # 要为包添加依赖项,或将其升级到最新版本
go get example.com/pkg@v1.2.3 # 将包升级或降级到特定版本
go get example.com/mod@none # 删除对模块的依赖关系并降级需要它的模块
```

# go install

```s
usage: go install [build flags] [packages]

Install compiles and installs the packages named by the import paths.

Executables are installed in the directory named by the GOBIN environment
variable, which defaults to $GOPATH/bin or $HOME/go/bin if the GOPATH
environment variable is not set. Executables in $GOROOT
are installed in $GOROOT/bin or $GOTOOLDIR instead of $GOBIN.

If the arguments have version suffixes (like @latest or @v1.0.0), "go install"
builds packages in module-aware mode, ignoring the go.mod file in the current
directory or any parent directory, if there is one. This is useful for
installing executables without affecting the dependencies of the main module.
To eliminate ambiguity about which module versions are used in the build, the
arguments must satisfy the following constraints:

- Arguments must be package paths or package patterns (with "..." wildcards).
They must not be standard packages (like fmt), meta-patterns (std, cmd,
all), or relative or absolute file paths.

- All arguments must have the same version suffix. Different queries are not
allowed, even if they refer to the same version.

- All arguments must refer to packages in the same module at the same version.

- No module is considered the "main" module. If the module containing
packages named on the command line has a go.mod file, it must not contain
directives (replace and exclude) that would cause it to be interpreted
differently than if it were the main module. The module must not require
a higher version of itself.

- Package path arguments must refer to main packages. Pattern arguments
will only match main packages.

If the arguments don't have version suffixes, "go install" may run in
module-aware mode or GOPATH mode, depending on the GO111MODULE environment
variable and the presence of a go.mod file. See 'go help modules' for details.
If module-aware mode is enabled, "go install" runs in the context of the main
module.

When module-aware mode is disabled, other packages are installed in the
directory $GOPATH/pkg/$GOOS_$GOARCH. When module-aware mode is enabled,
other packages are built and cached but not installed.

The -i flag installs the dependencies of the named packages as well.
The -i flag is deprecated. Compiled packages are cached automatically.

For more about the build flags, see 'go help build'.
For more about specifying packages, see 'go help packages'.

See also: go build, go get, go clean.
```

# go clean

```s
usage: go clean [clean flags] [build flags] [packages]

清理 从包源目录中删除对象文件。
go 命令在临时目录中构建大多数对象，因此 go clean 主要关注其他工具或手动调用 go build 留下的对象文件。

如果给出了包参数或设置了 -i 或 -r 标志，则 clean 将从与导入路径对应的每个源目录中删除以下文件：

        _obj/            old object directory, left from Makefiles
        _test/           old test directory, left from Makefiles
        _testmain.go     old gotest file, left from Makefiles
        test.out         old test log, left from Makefiles
        build.out        old test log, left from Makefiles
        *.[568ao]        object files, left from Makefiles

        DIR(.exe)        from go build
        DIR.test(.exe)   from go test -c
        MAINFILE(.exe)   from go build MAINFILE.go
        *.so             from SWIG

在列表中，DIR 表示目录的最后一个路径元素，MAINFILE 是生成包时未包含的目录中任何 Go 源文件的基名称。

-i 标志会导致干净删除相应的已安装存档或二进制文件（“go install”创建的内容）。

-n 标志使 clean 打印它将执行的删除命令，但不运行它们

-r 标志使 clean 以递归方式应用于由导入路径命名的包的所有依赖项。

-x 标志在执行删除命令时导致干净打印这些命令。

-cache 标志会导致干净删除整个 go 构建缓存。

-testcache 标志会导致 clean 使 go 构建缓存中的所有测试结果过期。

-modcache 标志导致 clean 删除整个模块下载缓存，包括版本化依赖项的解压缩源代码。

```
