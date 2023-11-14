# hello world

## 1. 输出 c 语言字面量 hello world

```go
//  1. 通过import "C" 语句启用CGO特性,同时包含C语言的<stdio.h>头文件
//#include <stdio.h>
import "C"

func Hello1() {
  // 2. 通过CGO包的C.CString函数将Go语言字符串转为C语言字符串
  // 3.调用CGO包的C.puts函数向标准输出窗口打印转换后的C字符串
	C.puts(C.CString("Hello, World\n"))

  /**
  没有释放使用C.CString创建的C语言字符串会导致内存泄漏。但是对于这个小程序来说，这样是没有问题的，因为程序退出后操作系统会自动回收程序的所有资源。
  */
}
```

## 2. 执行 c 片段

```go

/*
#include <stdio.h>

static void SayHello(const char* s) {
    puts(s);
}
*/
import "C"

func SayHello() {
	C.SayHello(C.CString("Hello, World\n"))
}

```

## 3. 执行外部 c 文件

```c
// hello.c
#include <stdio.h>

void SayHello(const char* s) {
    puts(s);
}
```

```go
// hello.go
package main

//void SayHello(const char* s);
import "C"

func main() {
    C.SayHello(C.CString("Hello, World\n"))
}

/*
注意，如果之前运行的命令是go run hello.go或go build hello.go的话，此处须使用go run "your/package"或go build "your/package"才可以。若本就在包路径下的话，也可以直接运行go run .或go build。
*/
```

## 4. c 模块化

```c
// hello.h
void SayHello2(const char* s);
// hello.c
#include "hello.h"
#include <stdio.h>

void SayHello3(const char* s) {
    puts(s);
}
```

```go
/*
#include <hello.h>
*/
import "C"
/*
  引入自定义的 .h 文件,使用 多行注释 ,不要使用单行注释
  .h 文件要和调用文件在同一个包下
  .C 文件也需要在同一包下
*/
func Hello() {
	C.SayHello3(C.CString("Hello, World\n"))
}

```

## 5. c++ 模块化

```cpp
// hello.h
void SayHello2(const char* s);
// hello.cpp
#include <iostream>
/*
不过为了保证C++语言实现的SayHello函数满足C语言头文件hello.h定义的函数规范，我们需要通过extern "C"语句指示该函数的链接符号遵循C语言的规则。
*/
extern "C" {
    #include "hello.h"
}

void SayHello2(const char* s) {
    std::cout << s;
}
```

```go
/*
#include <hello.h>
*/
import "C"
/*
  引入自定义的 .h 文件,使用 多行注释 ,不要使用单行注释
  .h 文件要和调用文件在同一个包下
  .cpp 文件也需要在同一包下
*/
func Hello() {
	C.SayHello2(C.CString("Hello, World\n"))
}

```

## 6. go 实现 c.h

1. 约定 .h 文件
2. 使用 go 实现 .h
3. call.go 调用.h

```c
// hello.h
// 为了适配CGO导出的C语言函数，我们禁止了在函数的声明语句中的const修饰符。
void SayHello4(/*const*/ char* s);
```

```go
// hello.go
import "C"

import "fmt"

// 我们通过CGO的//export SayHello指令将Go语言实现的函数SayHello导出为C语言函数。
// 需要注意的是，这里其实有两个版本的SayHello函数：一个Go语言环境的；另一个是C语言环境的。cgo生成的C语言版本SayHello函数最终会通过桥接代码调用Go语言版本的SayHello函数。
//export SayHello4
func SayHello4(s *C.char) {
	fmt.Print(C.GoString(s))
}

```

```go
//call.go
/*
#include <hello.h>
*/
import "C"

func Hello() {
	C.SayHello4(C.CString("Hello, World\n"))
}

```

## 7. 一个文件实现.h-.go-call.go

```go
package main

//void SayHello(char* s);
import "C"

import (
    "fmt"
)

func main() {
    C.SayHello(C.CString("Hello, World\n"))
}

//export SayHello
func SayHello(s *C.char) {
    fmt.Print(C.GoString(s))
}
```

另外,在 Go1.10 中 CGO 新增加了一个`_GoString_`预定义的 C 语言类型，用来表示 Go 语言字符串。改进后的代码如下:

```go
package gocgo2

//#cgo CFLAGS: -g -Wall   // 处理报错
/*
void SayHello6(_GoString_ s);
*/
import "C"

import (
	"fmt"
)

func Hello() {
	C.SayHello5(C.CString("Hello, World\n"))
	C.SayHello6("Hello, World\n")
}


//export SayHello6
func SayHello6(s string) {
	fmt.Print(s)
}

```

# cgo 基础

## 环境配置

1. 要使用 CGO 特性，需要安装 C/C++构建工具链
   1. 在 macOS 和 Linux 下是要安装 `GCC`
   2. 在 windows 下是需要安装 `MinGW`工具
2. 同时需要保证环境变量 `CGO_ENABLED` 被设置为 1，这表示 CGO 是被启用的状态。
   1. 在本地构建时 `CGO_ENABLED` 默认是启用的，当交叉构建时 CGO 默认是禁止的。
   2. 比如要交叉构建 ARM 环境运行的 Go 程序，需要手工设置好 C/C++交叉构建的工具链，同时开启 `CGO_ENABLED` 环境变量。
3. 然后通过 `import "C"`语句启用 CGO 特性。

## import "C" 语句

如果在 Go 代码中出现了 import "C"语句则表示使用了 CGO 特性，紧跟在这行语句前面的注释是一种特殊语法，里面包含的是正常的 C 语言代码。当确保 CGO 启用的情况下，还可以在当前目录中包含 C/C++对应的源文件。

```go
package main

/*
#include <stdio.h>

void printint(int v) {
    printf("printint: %d\n", v);
}
*/
import "C"

func main() {
    v := 42
    // 向C函数传递参数也很简单，就直接转化成对应C语言类型传递就可以。
    // C.int(v)用于将一个Go中的int类型值强制类型转换转化为C语言中的int类型值，然后调用C语言定义的printint函数进行打印
    C.printint(C.int(v))
}
```

**需要注意的是，import "C"导入语句需要单独一行，不能与其他包一同 import。**

## `#cgo`

在`import "C"`语句前的注释中可以通过`#cgo`语句设置编译阶段和链接阶段的相关参数。

1. 编译阶段的参数主要用于定义相关宏和指定头文件检索路径。`#cgo CFLAGS: -DPNG_DEBUG=1 -I./include`
   1. `-D`部分定义了宏 PNG_DEBUG，值为 1；
   2. `-I`定义了头文件包含的检索目录,可以为相对目录;
2. 链接阶段的参数主要是指定库文件检索路径和要链接的库文件。`#cgo LDFLAGS: -L/usr/local/lib -lpng`
   1. `-L`指定了链接时库文件检索目录，需要绝对路径;在库文件的检索目录中可以通过`${SRCDIR}`变量表示当前包目录的绝对路径
   2. `-l`指定了链接时需要链接 png 库

`#cgo`语句主要影响`CFLAGS`、`CPPFLAGS`、`CXXFLAGS`、`FFLAGS`和`LDFLAGS`几个编译器环境变量。

- `CFLAGS` 编译:对应 C 语言特有的编译选项
- `CPPFLAGS` 编译:对应 C 和 C++共有的编译选项
- `CXXFLAGS` 编译:对应是 C++特有的编译选项
- `FFLAGS`
- `LDFLAGS` 链接:

`#cgo`指令还支持条件选择，当满足某个操作系统或某个 CPU 架构类型时后面的编译或链接选项生效。

- `#cgo windows CFLAGS: -DX86=1` 在 windows 平台下，编译前会预定义 X86 宏为 1；
- `#cgo !windows LDFLAGS: -lm` 在非 widnows 平台下，在链接阶段会要求链接 math 数学库。

如果在不同的系统下 cgo 对应着不同的 c 代码，我们可以先使用#cgo 指令定义不同的 C 语言的宏，然后通过宏来区分不同的代码：

```go
package main

/*
#cgo windows CFLAGS: -DCGO_OS_WINDOWS=1
#cgo darwin CFLAGS: -DCGO_OS_DARWIN=1
#cgo linux CFLAGS: -DCGO_OS_LINUX=1

#if defined(CGO_OS_WINDOWS)
    const char* os = "windows";
#elif defined(CGO_OS_DARWIN)
    static const char* os = "darwin";
#elif defined(CGO_OS_LINUX)
    static const char* os = "linux";
#else
#    error(unknown os)
#endif
*/
import "C"

func main() {
    print(C.GoString(C.os))
}
```

## build tag 条件编译

`// +build debug` 放在文件开头,该文件文件只有在设置`debug`构建标志时才会被构建;`go build -tags="debug"`

```go
// +build debug

package main

var buildMode = "debug"
```

`go build -tags="windows debug"`,我们可以通过-tags 命令行参数同时指定多个 build 标志，它们之间用空格分隔。当有多个 build tag 时，我们将多个标志通过逻辑操作的规则来组合使用。比如以下的构建标志表示只有在”linux/386“或”darwin 平台下非 cgo 环境“才进行构建。`// +build linux,386 darwin,!cgo`;其中 linux,386 中 linux 和 386 用逗号链接表示 AND 的意思；而 linux,386 和 darwin,!cgo 之间通过空白分割来表示 OR 的意思。

# 类型转换

## 数值类型

| C 语言类型             | CGO 类型    | Go 语言类型 |
| ---------------------- | ----------- | ----------- |
| char                   | C.char      | byte        |
| singed char            | C.schar     | int8        |
| unsigned char          | C.uchar     | uint8       |
| short                  | C.short     | int16       |
| unsigned short         | C.ushort    | uint16      |
| int                    | C.int       | int32       |
| unsigned int           | C.uint      | uint32      |
| long                   | C.long      | int32       |
| unsigned long          | C.ulong     | uint32      |
| long long int          | C.longlong  | int64       |
| unsigned long long int | C.ulonglong | uint64      |
| float                  | C.float     | float32     |
| double                 | C.double    | float64     |
| size_t                 | C.size_t    | uint        |

需要注意的是，虽然在 C 语言中`int`、`short`等类型没有明确定义内存大小，但是在 CGO 中它们的内存大小是确定的。**在 CGO 中，C 语言的 int 和 long 类型都是对应 4 个字节的内存大小，size_t 类型可以当作 Go 语言 uint 无符号整数类型对待。**

CGO 中，虽然 C 语言的 int 固定为 4 字节的大小，但是 Go 语言自己的 int 和 uint 却在 32 位和 64 位系统下分别对应 4 个字节和 8 个字节大小。如果需要在 C 语言中访问 Go 语言的 int 类型，可以通过 GoInt 类型访问，GoInt 类型在 CGO 工具生成的 `_cgo_export.h` 头文件中定义。其实在 `_cgo_export.h` 头文件中，每个基本的 Go 数值类型都定义了对应的 C 语言类型，它们一般都是以单词 Go 为前缀。
下面是 64 位环境下，`_cgo_export.h` 头文件生成的 Go 数值类型的定义，其中 GoInt 和 GoUint 类型分别对应 GoInt64 和 GoUint64：

```c
typedef signed char GoInt8;
typedef unsigned char GoUint8;
typedef short GoInt16;
typedef unsigned short GoUint16;
typedef int GoInt32;
typedef unsigned int GoUint32;
typedef long long GoInt64;
typedef unsigned long long GoUint64;
typedef GoInt64 GoInt;
typedef GoUint64 GoUint;
typedef float GoFloat32;
typedef double GoFloat64;
```

除了 GoInt 和 GoUint 之外，我们并不推荐直接访问 GoInt32、GoInt64 等类型。更好的做法是通过 C 语言的 C99 标准引入的`<stdint.h>`头文件。为了提高 C 语言的可移植性，在`<stdint.h>`文件中，不但每个数值类型都提供了明确内存大小，而且和 Go 语言的类型命名更加一致。

| C 语言类型 | CGO 类型   | Go 语言类型 |
| ---------- | ---------- | ----------- |
| int8_t     | C.int8_t   | int8        |
| uint8_t    | C.uint8_t  | uint8       |
| int16_t    | C.int16_t  | int16       |
| uint16_t   | C.uint16_t | uint16      |
| int32_t    | C.int32_t  | int32       |
| uint32_t   | C.uint32_t | uint32      |
| int64_t    | C.int64_t  | int64       |
| uint64_t   | C.uint64_t | uint64      |

前文说过，如果 C 语言的类型是由多个关键字组成，则无法通过虚拟的“C”包直接访问(比如 C 语言的 unsigned short 不能直接通过 C.unsigned short 访问)。但是，在`<stdint.h>`中通过使用 C 语言的 typedef 关键字将 unsigned short 重新定义为 uint16_t 这样一个单词的类型后，我们就可以通过 C.uint16_t 访问原来的 unsigned short 类型了。对于比较复杂的 C 语言类型，推荐使用 typedef 关键字提供一个规则的类型命名，这样更利于在 CGO 中访问。

## go 字符串和切片

在 CGO 生成的`_cgo_export.h`头文件中还会为 Go 语言的字符串、切片、字典、接口和管道等特有的数据类型生成对应的 C 语言类型：

```c
typedef struct { const char *p; GoInt n; } GoString;
typedef void *GoMap;
typedef void *GoChan;
typedef struct { void *t; void *v; } GoInterface;
typedef struct { void *data; GoInt len; GoInt cap; } GoSlice;
```

不过需要注意的是，其中只有字符串和切片在 CGO 中有一定的使用价值，因为 CGO 为他们的某些 GO 语言版本的操作函数生成了 C 语言版本，因此二者可以在 Go 调用 C 语言函数时马上使用;而 CGO 并未针对其他的类型提供相关的辅助函数，且 Go 语言特有的内存模型导致我们无法保持这些由 Go 语言管理的内存指针，所以它们 C 语言环境并无使用的价值。

不过需要注意的是，如果使用了 `GoString` 类型则会对`_cgo_export.h` 头文件产生依赖，而这个头文件是动态输出的。Go1.10 针对 Go 字符串增加了一个`_GoString_`预定义类型，可以降低在 cgo 代码中可能对`_cgo_export.h`头文件产生的循环依赖的风险。

因为*GoString*是预定义类型，我们无法通过此类型直接访问字符串的长度和指针等信息。Go1.10 同时也增加了以下两个函数用于获取字符串结构中的长度和指针信息：

```c
size_t _GoStringLen(_GoString_ s);
const char *_GoStringPtr(_GoString_ s);
```

更严谨的做法是为 C 语言函数接口定义严格的头文件，然后基于稳定的头文件实现代码。

## 结构体、联合、枚举类型

C 语言的结构体、联合、枚举类型不能作为匿名成员被嵌入到 Go 语言的结构体中。

### 结构体

在 Go 语言中，我们可以通过 C.struct_xxx 来访问 C 语言中定义的 struct xxx 结构体类型。结构体的内存布局按照 C 语言的通用对齐规则，在 32 位 Go 语言环境 C 语言结构体也按照 32 位对齐规则，在 64 位 Go 语言环境按照 64 位的对齐规则。对于指定了特殊对齐规则的结构体，无法在 CGO 中访问。

```Go
/*
struct A {
    int i;
    float f;
};
*/
import "C"
import "fmt"

func main() {
    var a C.struct_A
    fmt.Println(a.i)
    fmt.Println(a.f)
}
```

如果结构体的成员名字中碰巧是 Go 语言的关键字，可以通过在成员名开头添加下划线来访问：

```go
/*
struct A {
    int type; // type 是 Go 语言的关键字
};
*/
import "C"
import "fmt"

func main() {
    var a C.struct_A
    fmt.Println(a._type) // _type 对应 type
}
```

但是如果有 2 个成员：一个是以 Go 语言关键字命名，另一个刚好是以下划线和 Go 语言关键字命名，那么以 Go 语言关键字命名的成员将无法访问（被屏蔽）：

```Go
/*
struct A {
    int   type;  // type 是 Go 语言的关键字
    float _type; // 将屏蔽CGO对 type 成员的访问
};
*/
import "C"
import "fmt"

func main() {
    var a C.struct_A
    fmt.Println(a._type) // _type 对应 _type
}
```

C 语言结构体中位字段对应的成员无法在 Go 语言中访问，如果需要操作位字段成员，需要通过在 C 语言中定义辅助函数来完成。对应零长数组的成员，无法在 Go 语言中直接访问数组的元素，但其中零长的数组成员所在位置的偏移量依然可以通过 `unsafe.Offsetof(a.arr)`来访问。

```go
/*
struct A {
    int   size: 10; // 位字段无法访问
    float arr[];    // 零长的数组也无法访问
};
*/
import "C"
import "fmt"

func main() {
    var a C.struct_A
    fmt.Println(a.size) // 错误: 位字段无法访问
    fmt.Println(a.arr)  // 错误: 零长的数组也无法访问
}
```

在 C 语言中，我们无法直接访问 Go 语言定义的结构体类型。

### 联合

对于联合类型，我们可以通过 C.union_xxx 来访问 C 语言中定义的 union xxx 类型。但是 Go 语言中并不支持 C 语言联合类型，它们会被转为对应大小的字节数组。

```go
/*
#include <stdint.h>

union B1 {
    int i;
    float f;
};

union B2 {
    int8_t i8;
    int64_t i64;
};
*/
import "C"
import "fmt"

func main() {
    var b1 C.union_B1;
    fmt.Printf("%T\n", b1) // [4]uint8

    var b2 C.union_B2;
    fmt.Printf("%T\n", b2) // [8]uint8
}
```

如果需要操作 C 语言的联合类型变量，一般有三种方法：第一种是在 C 语言中定义辅助函数；第二种是通过 Go 语言的`"encoding/binary"`手工解码成员(需要注意大端小端问题)；第三种是使用 unsafe 包强制转型为对应类型(这是性能最好的方式)。下面展示通过 unsafe 包访问联合类型成员的方式：

```go
/*
#include <stdint.h>

union B {
    int i;
    float f;
};
*/
import "C"
import "fmt"

func main() {
    var b C.union_B;
    fmt.Println("b.i:", *(*C.int)(unsafe.Pointer(&b)))
    fmt.Println("b.f:", *(*C.float)(unsafe.Pointer(&b)))
}
```

虽然 unsafe 包访问最简单、性能也最好，但是对于有嵌套联合类型的情况处理会导致问题复杂化。对于复杂的联合类型，推荐通过在 C 语言中定义辅助函数的方式处理。

### 枚举

对于枚举类型，我们可以通过 C.enum_xxx 来访问 C 语言中定义的 enum xxx 结构体类型。

```go
/*
enum C {
    ONE,
    TWO,
};
*/
import "C"
import "fmt"

func main() {
    var c C.enum_C = C.TWO
    fmt.Println(c)
    fmt.Println(C.ONE)
    fmt.Println(C.TWO)
}
```

在 C 语言中，枚举类型底层对应 int 类型，支持负数类型的值。我们可以通过 C.ONE、C.TWO 等直接访问定义的枚举值。

## 数组\字符串\切片

在 C 语言中，数组名其实对应于一个指针，指向特定类型特定长度的一段内存，但是这个指针不能被修改；当把数组名传递给一个函数时，实际上传递的是数组第一个元素的地址。为了讨论方便，我们将一段特定长度的内存统称为数组。C 语言的字符串是一个 char 类型的数组，字符串的长度需要根据表示结尾的 NULL 字符的位置确定。C 语言中没有切片类型。

在 Go 语言中，数组是一种值类型，而且数组的长度是数组类型的一个部分。Go 语言字符串对应一段长度确定的只读 byte 类型的内存。Go 语言的切片则是一个简化版的动态数组。

Go 语言和 C 语言的数组、字符串和切片之间的相互转换可以简化为 Go 语言的切片和 C 语言中指向一定长度内存的指针之间的转换。

CGO 的 C 虚拟包提供了以下一组函数，用于 Go 语言和 C 语言之间数组和字符串的双向转换：

```go
// Go string to C string
// C.CString针对输入的Go字符串，克隆一个C语言格式的字符串；返回的字符串由C语言的malloc函数分配，不使用时需要通过C语言的free函数释放。
func C.CString(string) *C.char

// Go []byte slice to C array
// 用于从输入的Go语言字节切片克隆一个C语言版本的字节数组，同样返回的数组需要在合适的时候释放
func C.CBytes([]byte) unsafe.Pointer

// C string to Go string
// C.GoString用于将从NULL结尾的C语言字符串克隆一个Go语言字符串
func C.GoString(*C.char) string

// C data with explicit length to Go string
// C.GoStringN是另一个字符数组克隆函数
func C.GoStringN(*C.char, C.int) string

// C data with explicit length to Go []byte
// C.GoBytes用于从C语言数组，克隆一个Go语言字节切片
func C.GoBytes(unsafe.Pointer, C.int) []byte
```

该组辅助函数都是以克隆的方式运行。当 Go 语言字符串和切片向 C 语言转换时，克隆的内存由 C 语言的 malloc 函数分配，最终可以通过 free 函数释放。当 C 语言字符串或数组向 Go 语言转换时，克隆的内存由 Go 语言分配管理。通过该组转换函数，转换前和转换后的内存依然在各自的语言环境中，它们并没有跨越 Go 语言和 C 语言。克隆方式实现转换的优点是接口和内存管理都很简单，缺点是克隆需要分配新的内存和复制操作都会导致额外的开销。
在 reflect 包中有字符串和切片的定义：

```go
type StringHeader struct {
    Data uintptr
    Len  int
}

type SliceHeader struct {
    Data uintptr
    Len  int
    Cap  int
}
```

如果不希望单独分配内存，可以在 Go 语言中直接访问 C 语言的内存空间：

```go

/*
static char arr[10];
static char *s = "Hello";
*/
import "C"
import "fmt"

func main() {
    // 通过 reflect.SliceHeader 转换
    var arr0 []byte
    var arr0Hdr = (*reflect.SliceHeader)(unsafe.Pointer(&arr0))
    arr0Hdr.Data = uintptr(unsafe.Pointer(&C.arr[0]))
    arr0Hdr.Len = 10
    arr0Hdr.Cap = 10

    // 通过切片语法转换
    arr1 := (*[31]byte)(unsafe.Pointer(&C.arr[0]))[:10:10]

    var s0 string
    var s0Hdr = (*reflect.StringHeader)(unsafe.Pointer(&s0))
    s0Hdr.Data = uintptr(unsafe.Pointer(C.s))
    s0Hdr.Len = int(C.strlen(C.s))

    sLen := int(C.strlen(C.s))
    s1 := string((*[31]byte)(unsafe.Pointer(&C.s[0]))[:sLen:sLen])
}
```

因为 Go 语言的字符串是只读的，用户需要自己保证 Go 字符串在使用期间，底层对应的 C 字符串内容不会发生变化、内存不会被提前释放掉。

在 CGO 中，会为字符串和切片生成和上面结构对应的 C 语言版本的结构体：

```go
typedef struct { const char *p; GoInt n; } GoString;
typedef struct { void *data; GoInt len; GoInt cap; } GoSlice;
```

在 C 语言中可以通过 GoString 和 GoSlice 来访问 Go 语言的字符串和切片。如果是 Go 语言中数组类型，可以将数组转为切片后再行转换。如果字符串或切片对应的底层内存空间由 Go 语言的运行时管理，那么在 C 语言中不能长时间保存 Go 内存对象。

## 指针相互转换

cgo 存在的一个目的就是打破 Go 语言的禁止，恢复 C 语言应有的指针的自由转换和指针运算。以下代码演示了如何将 X 类型的指针转化为 Y 类型的指针：

```go
var p *X
var q *Y

q = (*Y)(unsafe.Pointer(p)) // *X => *Y
p = (*X)(unsafe.Pointer(q)) // *Y => *X

//为了实现X类型指针到Y类型指针的转换，我们需要借助unsafe.Pointer作为中间桥接类型实现不同类型指针之间的转换。unsafe.Pointer指针类型类似C语言中的void*类型的指针。
```

## 数值和指针的转换

不同类型指针间的转换看似复杂，但是在 cgo 中已经算是比较简单的了。在 C 语言中经常遇到用普通数值表示指针的场景，也就是说如何实现数值和指针的转换也是 cgo 需要面对的一个问题。
为了严格控制指针的使用，Go 语言禁止将数值类型直接转为指针类型！不过，Go 语言针对 unsafe.Pointr 指针类型特别定义了一个 uintptr 类型。我们可以 uintptr 为中介，实现数值类型到 unsafe.Pointr 指针类型到转换。再结合前面提到的方法，就可以实现数值和指针的转换了。

```Go
func TestNumberToPtr(t *testing.T) {
	var x int = 123456
	a, _ := strconv.ParseUint(fmt.Sprintf("%p", &x), 0, 64)
	b := uintptr(a)
	ptr := (*int)(unsafe.Pointer(b))
	t.Log(*ptr)
}
```

## 切片间的转换

在 C 语言中数组也一种指针，因此两个不同类型数组之间的转换和指针间转换基本类似。但是在 Go 语言中，数组或数组对应的切片都不再是指针类型，因此我们也就无法直接实现不同类型的切片之间的转换。

不过 Go 语言的 reflect 包提供了切片类型的底层结构，再结合前面讨论到不同类型之间的指针转换技术就可以实现[]X 和[]Y 类型的切片转换：

```go

var p []X
var q []Y

pHdr := (*reflect.SliceHeader)(unsafe.Pointer(&p))
qHdr := (*reflect.SliceHeader)(unsafe.Pointer(&q))

pHdr.Data = qHdr.Data
pHdr.Len = qHdr.Len * unsafe.Sizeof(q[0]) / unsafe.Sizeof(p[0])
pHdr.Cap = qHdr.Cap * unsafe.Sizeof(q[0]) / unsafe.Sizeof(p[0])
```

不同切片类型之间转换的思路是先构造一个空的目标切片，然后用原有的切片底层数据填充目标切片。如果 X 和 Y 类型的大小不同，需要重新设置 Len 和 Cap 属性。需要注意的是，如果 X 或 Y 是空类型，上述代码中可能导致除 0 错误，实际代码需要根据情况酌情处理。

# 函数调用

函数是 C 语言编程的核心，通过 CGO 技术我们不仅仅可以在 Go 语言中调用 C 语言函数，也可以将 Go 语言函数导出为 C 语言函数。

## Go 调用 C 函数

```go
/*
static int div(int a, int b) {
    return a/b;
}
*/
import "C"
import "fmt"

func main() {
    v := C.div(6, 3)
    fmt.Println(v)
}
```

## C 调用 Go 导出函数

```go
import "C"

//export add
func add(a, b C.int) C.int {
    return a+b
}
```
