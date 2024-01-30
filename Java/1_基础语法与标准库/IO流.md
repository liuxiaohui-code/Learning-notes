# IO

## try(resource) 自动释放资源JDK7

会自动关闭括号里定义的资源,要求,资源对象必须实现 `Closable`接口,然后由编译器自动执行 `close()`方法

```java
try(InputStream input = new FileInputStream()){
}
```


# 控制台输入和输出

## 标准输出流 System.out

```java
System.out.print();
System.out.printf();
System.out.println();
```

## 标准输入流 System.in

1. `System.in`
2. `java.util.Scanner`类

```java
Scanner scanner = new Scanner(System.in); // 创建Scanner对象
System.out.print("Input your name: "); // 打印提示
String name = scanner.nextLine(); // 读取一行输入并获取字符串
System.out.print("Input your age: "); // 打印提示
int age = scanner.nextInt(); // 读取一行输入并获取整数
System.out.printf("Hi, %s, you are %d\n", name, age); // 格式化输出
```

# IO

​IO 是指对数据流的输入和输出，也称为 IO 流，IO 流主要分为两大类，字节流和字符流。IO 流的本质是数据传输，并且流是单向的。

输入和输出是相对于内存来定义的,输入流将数据 read 到内存,输出流将内存中的数据 write 到文件等其他输出设备

数据->输入设备-->输入流-->内存->输出流->输出设备

## InputStream 字节输入流,获取流中的数据

java.io.InputStream ,Java 标准库提供的最基本的输入流,是一个抽象类,面向抽象编程的思想.

实际上，InputStream 也有缓冲区。例如，从 FileInputStream 读取一个字节时，操作系统往往会一次性读取若干字节到缓冲区，并维护一个指针指向未读的缓冲区。然后，每次我们调用 int read()读取下一个字节时，可以直接返回缓冲区的下一个字节，避免每次读一个字节都导致 IO 操作。当缓冲区全部读完后继续调用 read()，则会触发操作系统的下一次读取并再次填满缓冲区。

```java
// 读取输入流的下一个字节，并返回字节表示的int值（0~255）,Java byte -128-127,与返回的值不符合,所以极可能使用byte[]依次读取多个字节
// 读到末尾，返回-1
public abstract int read() throws IOException;

// 缓冲读取多字节,定义一个byte[]数组作为缓冲区,会尽可能多地读取字节到缓冲区，但不会超过缓冲区的大小。如果返回-1，表示没有更多的数据了。
int read(byte[] b) // 读取若干字节并填充到byte[]数组，返回读取的字节数
int read(byte[] b, int off, int len) // 指定byte[]数组的偏移量和最大填充数

// 关闭
close()
```

### 阻塞

在调用 InputStream 的 read()方法读取数据时，我们说 read()方法是阻塞（Blocking）的。

必须等 read()方法返回后才能继续。因为读取 IO 流相比执行普通代码，速度会慢很多，因此，无法确定 read()方法调用到底要花费多长时间。

### 子类

1. `ByteArrayInputStream` 在内存中模拟一个字节流输入 `byte[]->InputStream`,常用于测试构建输入流
2. `FileInputStream` 从文件系统中的文件获取输入字节 `File -> InputStream`
3. `FilterInputStream` 增强流
4. `ObjectInputStream` 序列化对象流
5. `SequenceInputStream` 组合流,表示其他输入流的逻辑串联
6. `AudioInputStream` 音频输入流是具有指定音频格式和长度的输入流。
7. `PipedInputStream` 管道,用于并发线程通信
8. `StringBufferInputStream` Deprecated.

## OutputStream 字节输出流,将数据写入流

OutputStream 是 Java 标准库提供的最基本的输出流。

OutputStream 也是抽象类，它是所有输出流的超类。

```java
// 将一个字节写入输出流,虽然传入的是int参数，但只会写入一个字节，即只写入int最低8位表示字节的部分（相当于b & 0xff）
public abstract void write(int b) throws IOException;
public abstract void write(byte[] b) throws IOException; //一次性写入若干字节
// 关闭
close()
// 将缓冲区的内容真正输出到目的地,因为向磁盘、网络写入数据的时候，出于效率的考虑，操作系统并不是输出一个字节就立刻写入到文件或者发送到网络，而是把输出的字节先放到内存的一个缓冲区里（本质上就是一个byte[]数组），等到缓冲区写满了，再一次性写入文件或者网络。
// 强制把缓冲区内容输出
// 通常情况下，我们不需要调用这个flush()方法，因为缓冲区写满了OutputStream会自动调用它，并且，在调用close()方法关闭OutputStream之前，也会自动调用flush()方法。
flush()
```

### 阻塞

和 InputStream 一样，OutputStream 的 `write()`方法也是阻塞的。

### 子类

1. `ByteArrayOutputStream` 数据被写入字节数组。 缓冲区会在数据写入时自动增长,可以在内存中模拟一个 OutputStream
2. `FileOutputStream` 从文件获取输出流
3. `FilterOutputStream` 包装类,通过很多子类增强功能
4. `PipedOutputStream ` 管道,用于并发多个线程之间传递数据
5. `ObjectOutputStream` 序列化

## 流的关闭

Java 7 引入的新的 `try(resource)`的语法，只需要编写 try 语句，让编译器自动为我们关闭资源。

实际上，编译器并不会特别地为 InputStream 加上自动关闭。编译器只看 `try(resource = …)`中的对象是否实现了 `java.lang.AutoCloseable` 接口，如果实现了，就自动加上 `finally` 语句并调用 `close()`方法。 `InputStream` 和 `OutputStream` 都实现了这个接口，因此，都可以用在 try(resource)中。

```java
byte[] bytes = {1, 2, 3, 4, 5};
try (
        InputStream is = new ByteArrayInputStream(bytes);
        OutputStream os = new ByteArrayOutputStream();
) {
    for (int x = is.read(); x != -1; x = is.read()) {
        System.out.println(x);
        os.write(x);
    }
}
```

## Filter 模式

通过一个“基础”组件再叠加各种“附加”功能组件的模式，称之为 Filter 模式（或者装饰器模式：Decorator）

Java 的 IO 标准库使用 `Filter` 模式为 `InputStream` 和 `OutputStream` 增加功能：

可以把一个 `InputStream` 和任意个 `FilterInputStream` 组合；
可以把一个 `OutputStream` 和任意个 `FilterOutputStream` 组合。

Filter 模式可以在运行期动态增加功能（又称 Decorator 模式）。

使用:

1. 确定数据源,为流定基础,比如数据来自文件 `InputStream file = new FileInputStream("test.gz");`
2. 附加功能,比如增加缓冲提高读取效率`InputStream buffered = new BufferedInputStream(file);`
3. 假设该文件已经用 gzip 压缩了，我们希望直接读取解压缩的内容，就可以再包装一个 GZIPInputStream `InputStream gzip = new GZIPInputStream(buffered);`
4. 只需要持有最外层的 `InputStream` 即可
5. 当最外层的 `InputStream` 关闭时（在 `try(resource)`块的结束处自动关闭），内层的 `InputStream` 的 `close()`方法也会被自动调用，并最终调用到最核心的“基础” `InputStream` ，因此不存在资源泄露。

### 常用类

1. 基类 `FilterOutputStream`
2. `BufferedOutputStream` `BufferedInputStream` 缓冲,增加读写速度
3. `CheckedOutputStream` `CheckedInputStream` 维护正在读取的数据的校验和。 然后可以使用校验和来验证输入数据的完整性
4. `CipherOutputStream` `CipherInputStream` 加解密
5. `DataOutputStream` `DataInputStream` 数据输入流允许应用程序以与机器无关的方式从底层输入流中读取原始 Java 基本数据类型 int byte float...
6. `DeflaterOutputStream` `DeflaterInputStream` 实现流过滤器，以“压缩”压缩格式压缩数据。
7. `DigestOutputStream` `DigestInputStream` 透明流，使用通过流的位更新关联的消息摘要。
8. `InflaterOutputStream` `InflaterInputStream` 流过滤器，用于以“deflate”压缩格式解压缩数据。 它还用作其他解压缩过滤器的基础，例如 `GZIPInputStream` `ZipInputStream` `JarInputStream`
9. `LineNumberInputStream` Deprecated 输入流过滤器，提供跟踪当前行号的附加功能
10. `ProgressMonitorInputStream` jwt 展示监视从某些 InputStream 读取的进度。
11. `PushbackInputStream` 推回缓冲流,可以把读取进来的某些数据重新回退到输入流的缓冲区之中
12. `PrintStream` 方便地打印各种数据值的表示,添加了一组`print()/println()`方法，可以打印各种数据类型，比较方便外，它还有一个额外的优点，就是**不会抛出 IOException**

## Reader 字符输入流

和 InputStream 的区别是，InputStream 是一个字节流，即以 `byte` 为单位读取，而 Reader 是一个字符流，即以 `char` 为单位读取

```java
// 读取一个char, 2个字节2^16 (-1，0~65535）
public int read() throws IOException;
public int read(char[] c) throws IOException;

//要避免乱码问题，我们需要在创建FileReader时指定编码：
Reader reader = new FileReader("src/readme.txt", StandardCharsets.UTF_8);

try (Reader reader = new FileReader("src/readme.txt", StandardCharsets.UTF_8) {
    // TODO
}
```

### 子类

1. `CharArrayReader` 在内存中模拟一个 Reader，它的作用实际上是把一个 char[]数组变成一个 Reader
2. `StringReader` 直接把 String 作为数据源
3. `InputStreamReader` 把任何 InputStream 转换为 Reader `Reader reader = new InputStreamReader(input, "UTF-8");` 子类 `FileReader`文件作为数据源
4. `BufferedReader` 从字符输入流中读取文本，缓冲字符，以便有效地读取字符，数组和行。 子类: `LineNumberReader` 设置行号
5. `FilterReader` 读取已过滤字符流的抽象类,子类: `PushbackReader`允许将字符推回到流中
6. `PipedReader` 管道
7. `URLReader` Deprecated

## Writer 字符输出流

Reader 是带编码转换器的 InputStream，它把 byte 转换为 char，而 Writer 就是带编码转换器的 OutputStream，它把 char 转换为 byte 并输出。

```java
写入一个字符（0~65535）：void write(int c)；
写入字符数组的所有字符：void write(char[] c)；
写入String表示的所有字符：void write(String s)。

//要避免乱码问题，我们需要在创建 FileWriter 时指定编码：
Writer writer = new FileWriter("readme.txt", StandardCharsets.UTF_8);

try (Reader reader = new FileReader("src/readme.txt", StandardCharsets.UTF_8) {
    // TODO
}
```

### 子类

1. `FileWriter` 向文件中写入字符流
2. `CharArrayWriter`在内存中创建一个 Writer，它的作用实际上是构造一个缓冲区，可以写入 char，最后得到写入的 char[]数组
3. `StringWriter` StringWriter 也是一个基于内存的 Writer，它和 CharArrayWriter 类似。实际上，StringWriter 在内部维护了一个 StringBuffer，并对外提供了 Writer 接口。
4. `OutputStreamWriter` 将任意的 OutputStream 转换为 Writer 的转换器
5. `BufferedWriter` 将文本写入字符输出流，缓冲字符，以便有效地写入单个字符，数组和字符串。
6. `FilterWriter` 包装
7. `PipedWriter` 管道
8. `PrintWriter` 字符打印流

# File

java.io.File 文件和目录路径名的抽象表示;

## 基本使用

```java
// 可以绝对路径
File f = new File("/usr/bin/javac");
// 相对路径  可以用.表示当前目录，..表示上级目录。
File f1 = new File("sub\\javac");
// 判断文件是否存在
boolean exists()
// 文件分隔符
System.out.println(File.separator); // 根据当前平台打印"\"或"/"

// 获取文件路径
File file = new File("..");
System.out.println(file.getPath()); // ..  构造函数参数路径
System.out.println(file.getAbsolutePath()); //绝对路径 D:\ide\workspace-java\demo-maven\..
System.out.println(file.getCanonicalPath()); //规范路径 D:\ide\workspace-java  把.和..转换成标准的绝对路径后的路径


// 判断文件还是文件夹
boolean isFile() //文件
boolean isDirectory() //文件夹

// 判断文件的权限和大小
boolean canRead() //是否可读；
boolean canWrite() //是否可写；
boolean canExecute() // 是否可执行；对目录而言，是否可执行表示能否列出它包含的文件和子目录。
long length() //文件字节大小。

//创建文件
boolean createNewFile()
//删除文件
boolean delete()
//创建临时文件
boolean createTempFile()
//在JVM退出时自动删除该文件
boolean deleteOnExit()
// 创建当前File对象表示的目录
boolean mkdir()
//创建当前File对象表示的目录，并在必要时将不存在的父目录也创建出来；
boolean mkdirs()
//删除当前File对象表示的目录，当前目录必须为空才能删除成功。
boolean delete()

//列出目录下的文件和子目录名
list()
listFiles()//可以过滤不想要的文件和目录
```

## Path

Java 标准库还提供了一个 Path 对象，它位于 java.nio.file 包。Path 对象和 File 对象类似，但操作更加简单

如果需要对目录进行复杂的拼接、遍历等操作，使用 Path 对象更方便。

# 操作 zip

## 读取

`ZipInputStream` 是一种 `FilterInputStream` ，它可以直接读取 zip 包的内容;另一个 `JarInputStream` 是从 ZipInputStream 派生，它增加的主要功能是直接读取 jar 文件里面的 `MANIFEST.MF` 文件。因为本质上 jar 包就是 zip 包，只是额外附加了一些固定的描述文件。

- `ZipEntry` 表示一个压缩包内部文件或目录，如果是压缩文件，我们就用 read()方法不断读取，直到返回-1

```java
try (ZipInputStream zip = new ZipInputStream(new FileInputStream(...))) {
    ZipEntry entry = null;
    // 不断获取压缩文件
    while ((entry = zip.getNextEntry()) != null) {
        String name = entry.getName();
        if (!entry.isDirectory()) {
            int n;
            // 读取压缩文件
            while ((n = zip.read()) != -1) {
                ...
            }
        }
    }
}
```

## 写入

ZipOutputStream 是一种 FilterOutputStream，它可以直接写入内容到 zip 包。我们要先创建一个 ZipOutputStream，通常是包装一个 FileOutputStream，然后，每写入一个文件前，先调用 putNextEntry()，然后用 write()写入 byte[]数据，写入完毕后调用 closeEntry()结束这个文件的打包。

1. 创建 `ZipOutputStream`
2. 选定压缩文件 `File[]`
3. 写入 `putNextEntry() -> write() -> closeEntry()`

```java
try (ZipOutputStream zip = new ZipOutputStream(new FileOutputStream(...))) {
    File[] files = ...
    for (File file : files) {
        zip.putNextEntry(new ZipEntry(file.getName()));
        zip.write(getFileDataAsBytes(file));
        zip.closeEntry();
    }
}
```

# 获取 jar 包内部文件

从 classpath 读取文件就可以避免不同环境下文件路径不一致的问题;

在 classpath 中的资源文件，路径总是以／开头，我们先获取当前的 Class 对象，然后调用 getResourceAsStream()就可以直接从 classpath 读取任意的资源文件;

```java
try (InputStream input = getClass().getResourceAsStream("/default.properties")) {
    // getResourceAsStream()需要特别注意的一点是，如果资源文件不存在，它将返回null
     if (input != null) {
        // TODO:
    }
}
```

本项目打包后,文件被打进 jar 包里,此时获取文件的方法

```java
// 获取路径
String s1 = this.getClass().getResource("/library.properties").getPath();
// 获取URL
URL url = this.getClass().getClassLoader().getResource(filepath);
File file = new File(url.getFile());
```




