# Java version

49 = Java 5
50 = Java 6
51 = Java 7
52 = Java 8
53 = Java 9
54 = Java 10
55 = Java 11
56 = Java 12
57 = Java 13
58 = Java 14

# Java 9

## Jigsaw 模块系统

在 Java 9 以前，打包和依赖都是基于 JAR 包进行的。JRE 中包含了 rt.jar，将近 63M，也就是说要运行一个简单的 Hello World，也需要依赖这么大的 jar 包。在 Java 9 中提出的模块化系统，对这点进行了改善。
关于模块化系统具体可以看看这篇文章。

## JShell REPL

Java 9 提供了交互式解释器。有了 JShell 以后，Java 终于可以像 Python，Node.js 一样在 Shell 中运行一些代码并直接得出结果了。

## 接口中使用私有方法

Java 9 中可以在接口中定义私有方法。示例代码如下:
public interface TestInterface {
String test();

    // 接口默认方法
    default String defaultTest() {
        pmethod();
        return "default";
    }

    private String pmethod() {
        System.out.println("private method in interface");
        return "private";
    }

}

## 集合不可变实例工厂方法

在以前，我们想要创建一个不可变的集合，需要先创建一个可变集合，然后使用 unmodifiableSet 创建不可变集合。代码如下:
Set<String> set = new HashSet<>();
set.add("A");
set.add("B");
set.add("C");

set = Collections.unmodifiableSet(set);
System.out.println(set);
Java 9 中提供了新的 API 用来创建不可变集合。
List<String> list = List.of("A", "B", "C");
Set<String> set = Set.of("A", "B", "C");
Map<String, String> map = Map.of("KA", "VA", "KB", "VB");

## 改进 try-with-resources

Java 9 中不需要在 try 中额外定义一个变量。Java 9 之前需要这样使用 try-with-resources:
InputStream inputStream = new StringBufferInputStream("a");
try (InputStream in = inputStream) {
in.read();
} catch (IOException e) {
e.printStackTrace();
}
在 Java 9 中可以直接使用 inputStream 变量，不需要再额外定义新的变量了。
InputStream inputStream = new StringBufferInputStream("a");
try (inputStream) {
inputStream.read();
} catch (IOException e) {
e.printStackTrace();
}

## 多版本兼容 jar 包

Java 9 中支持在同一个 JAR 中维护不同版本的 Java 类和资源。

## 增强了 Stream，Optional，Process API

## 新增 HTTP2 Client

## 增强 Javadoc，增加了 HTML 5 文档的输出，并且增加了搜索功能

## 增强 @Deprecated

对 Deprecated 新增了 since 和 forRemoval 属性

## 增强了钻石操作符 "<>"，可以在 匿名内部类中使用了。

在 Java 9 之前，内部匿名类需要指定泛型类型，如下:
Handler<? extends Number> intHandler1 = new Handler<Number>(2) {
}
而在 Java 9 中，可以自动做类型推导，如下:
Handler<? extends Number> intHandler1 = new Handler<>(2) {
}

## 多分辨率图像 API:定义多分辨率图像 API，开发者可以很容易的操作和展示不同分辨率的图像了。

## 改进的 CompletableFuture API

CompletableFuture 类的异步机制可以在 ProcessHandle.onExit 方法退出时执行操作。
其他一些改进可参照 docs.oracle.com/javase/9/wh…

# Java 10

## 新增局部类型推断 var

var a = "aa";
System.out.println(a);
复制代码 var 关键字目前只能用于局部变量以及 for 循环变量声明中。

## 删除工具 javah

从 JDK 中移除了 javah 工具，使用 javac -h 代替。

## 统一的垃圾回收接口，改进了 GC 和其他内务管理

## ThreadLocal 握手交互

JDK 10 引入一种在线程上执行回调的新方法，很方便的停止单个线程而不是停止全部线程或者一个都不停。

## 基于 Java 的实验性 JIT 编译器

Java 10 开启了 Java JIT 编译器 Graal，用作 Linux / x64 平台上的实验性 JIT 编译器。

## 提供默认的 CA 根证书

## 将 JDK 生态整合到单个仓库

此 JEP 的主要目标是执行一些内存管理，并将 JDK 生态的众多存储库组合到一个存储库中。

其他一些改进可以参照 www.oracle.com/technetwork…

# Java 11

## Lambda 表达式中使用 var

(var x, var y) -> x.process(y)

## 字符串 API 增强

Java 11 新增了 一系列字符串处理方法，例如:
// 判断字符串是否为空白
" ".isBlank();
" Javastack ".stripTrailing(); // " Javastack"
" Javastack ".stripLeading(); // "Javastack "

## 3. 标准化 HttpClient API

1. java 命令直接编译并运行 java 文件，省去先 javac 编译生成 class 再运行的步骤
2. 增加对 TLS 1.3 的支持
   其他一些改进可以参照 www.oracle.com/technetwork…

# Java 12

## switch 表达式

Java 12 以后，switch 不仅可以作为语句，也可以作为表达式。
private String switchTest(int i) {
return switch (i) {
case 1 -> "1";
default -> "0";
};
}
