# Java7

## try(resource) 自动释放资源

会自动关闭括号里定义的资源,要求,资源对象必须实现 `Closable`接口,然后由编译器自动执行 `close()`方法

```java
try(InputStream input = new FileInputStream()){
}
```

## switch String 基本用法

switch 可以比对**枚举**和**字符串**

## 二进制

java 7 开始，可以直接指定不同的进制数字。

二进制指定数字值，只需要使用 `0b` 或者 `OB` 开头。
八进制指定数字值，使用 `0`开头。
十六进制指定数字值，使用 `0x` 开头。

## 数字下划线

Java 7 开始支持在数字定义时候使用下划线分割，增加了数字的可读性。

```java
int a = 10_000_000;
int b = 10_0000;
```

# java 8

## Lambda 和 函数式接口

使用 Lambda 表达式的前提为接口时函数式接口;

函数式接口: 只有一个接口方法的接口

java 为我们提供了四个比较重要的函数式接口:

消费型接口: `Consumer<T> void accept(T t)`有参数，无返回值的抽象方法；
供给型接口: `Supplier <T> T get()` 无参有返回值的抽象方法；
断定型接口: `Predicate<T> boolean test(T t)`:有参，但是返回值类型是固定的 boolean
函数型接口: `Function<T,R> R apply(T t)`有参有返回值的抽象方法；

```java
    @FunctionalInterface
    interface Operation {
        int operation(int a, int b);
    }
    @Test
    void normal(){
        // 匿名类
        Operation operation = new Operation(){
            @Override
            public int operation(int a, int b) {
                return 0;
            }
        };
        System.out.println(operation.operation(1,2));
    }
    @Test
    void lambda(){
        // lambda 表达式
        Operation operation = (int a, int b) -> a + b;
        System.out.println(operation.operation(1,2));
    }
```

## 方法引用

作用: 指代一个实现函数式接口的匿名对象,要求除函数名可以不一致外,参数表和返回值类型要保持一致

通过方法引用，可以使用方法的名字来指向一个方法。使用一对冒号来引 `::` 用方法。

构造方法引用
使用方式:Class::new

静态方法引用
使用方式:Class::staticMethod

对象的实例方法引用
使用方式:instance::method

类的实例方法引用
使用方式:Class::method

```java
    @FunctionalInterface
    interface Operation {
        int operation(int a, int b);
    }
    @FunctionalInterface
    interface Operation2 {
        int operation(OperationFactory factory,int a, int b);
    }
    @FunctionalInterface
    interface Operation3 {
        OperationFactory operation();
    }
    static class OperationFactory{
        int add(int a,int b){
            return a+b;
        }
        //add, subtract, multiply and divide
        static int subtract(int a,int b){
            return a-b;
        }
        int multiply(int a,int b){
            return a*b;
        }
        int divide(int a,int b){
            return a/b;
        }

        @Override
        public String toString() {
            return "OperationFactory{}";
        }
    }
    @Test
    void methodReference(){
        // 对象的实例方法
        Operation operation1 = new OperationFactory()::add;
        System.out.println(operation1.operation(1,2));
        // 类的静态方法
        Operation operation2 = OperationFactory::subtract;
        System.out.println(operation2.operation(1,2));
        // 类的实例方法
        Operation2 operation3 = OperationFactory::multiply;
        System.out.println(operation3.operation(new OperationFactory(),2,2));
        // 类的构造方法
        Operation3 operation4 = OperationFactory::new;
        System.out.println(operation4.operation());
    }
```

## 接口默认方法和静态方法

```java
public interface TestInterface {
    String test();

    // 接口默认方法,类似与抽象类的非抽象方法,可以重写,可以继承
    default String defaultTest() {
        return "default";
    }
    // 不可以重写
    static String staticTest() {
        return "static";
    }
}
```

## 重复注解

Java 8 支持了重复注解。在 Java 8 之前想实现重复注解，需要用一些方法来绕过限制。

## 类型注解

Java 8 之前注解只能用在声明中，在 Java 8 中，注解可以使用在 任何地方。

## 更好的类型推断

## Optional

Java 8 中新增了 Optional 类用来解决空指针异常。Optional 是一个可以保存 null 的容器对象。通过 isPresent() 方法检测值是否存在，通过 get() 方法返回对象。
除此之外，Optional 还提供了很多其他有用的方法，具体可以查看文档。https://link.juejin.cn/?target=https%3A%2F%2Fdocs.oracle.com%2Fjavase%2F8%2Fdocs%2Fapi%2Fjava%2Futil%2FOptional.html

## Stream

Java 8 中新增的 Stream 类提供了一种新的数据处理方式。这种方式将元素集合看做一种流，在管道中传输，经过一系列处理节点，最终输出结果。

## 日期时间 API

Java 8 中新增了日期时间 API 用来加强对日期时间的处理，其中包括了 LocalDate，LocalTime，LocalDateTime，ZonedDateTime 等等
https://link.juejin.cn/?target=https%3A%2F%2Fwww.oracle.com%2Ftechnetwork%2Farticles%2Fjava%2Fjf14-date-time-2125367.html
https://link.juejin.cn/?target=https%3A%2F%2Fwww.cnblogs.com%2Fmuscleape%2Fp%2F9956754.html

## Base64 支持

Java 8 标准库中提供了对 Base 64 编码的支持。java.util.Base64

## 并行数组 ParallelSort

Java 8 中提供了对数组的并行操作，包括 parallelSort 等等，

## 其他新特性

对并发的增强
在 java.util.concurrent.atomic 包中还增加了下面这些类:
DoubleAccumulator
DoubleAdder
LongAccumulator
LongAdder
提供了新的 Nashorn javascript 引擎
提供了 jjs，是一个给予 Nashorn 的命令行工具，可以用来执行 JavaScript 源码
提供了新的类依赖分析工具 jdeps
JVM 的新特性
JVM 内存永久区已经被 metaspace 替换（JEP 122）。JVM 参数 -XX:PermSize 和 –XX:MaxPermSize 被 XX:MetaSpaceSize 和 -XX:MaxMetaspaceSize 代替。

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
