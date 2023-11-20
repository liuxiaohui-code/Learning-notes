# Java

## 特性与优势

- 简单性
- 面向对象
- 可移植性
- 高性能
- 分布式
- 动态性
- 多线程
- 安全性
- 健壮性

## 三大版本

write once ,run anywhere --- JVM

- JavaSE 标准版 （桌面、控制台）
- JavaME :嵌入式开发
- JavaEE : 企业开发（web 、服务器）

## JDK JRE JVM

```
  ┌─    ┌──────────────────────────────────┐
  │     │     Compiler, debugger, etc.     │
  │     └──────────────────────────────────┘
 JDK ┌─ ┌──────────────────────────────────┐
  │  │  │                                  │
  │ JRE │      JVM + Runtime Library       │
  │  │  │                                  │
  └─ └─ └──────────────────────────────────┘
        ┌───────┐┌───────┐┌───────┐┌───────┐
        │Windows││ Linux ││ macOS ││others │
        └───────┘└───────┘└───────┘└───────┘
```

- JDK : JAVA development kit java 开发者工具
- JRE : JAVA runtime environment java 运行时环境
- JVM : JAVA virtual machine java 虚拟机

JSR 规范：Java Specification Request,SUN 公司
JCP 组织：Java Community Process

JDK 包含 JRE,JRE 包含 JVM

## 开发环境搭建

1. 安装
   1. 下载 jdk8---常用
   2. 记住安装路径
   3. 系统变量 JAVA_HOME: java 安装路径
   4. 系统变量 path: %JAVA_HOME%/bin
   5. 系统变量 path: %JAVA_HOME%/jre/bin
   6. `java -version`
2. 卸载
   1. 删除 java 安装目录
   2. 删除 JAVA_HOME
   3. 删除 path 环境变量
   4. `java -version`

## 工具

细心的童鞋还可以在 JAVA_HOME 的 bin 目录下找到很多可执行文件：

`java` ：这个可执行程序其实就是 JVM，运行 Java 程序，就是启动 JVM，然后让 JVM 执行指定的编译后的代码；
`javac` ：这是 Java 的编译器，它用于把 Java 源码文件（以.java 后缀结尾）编译为 Java 字节码文件（以.class 后缀结尾）；
`jar` ：用于把一组.class 文件打包成一个.jar 文件，便于发布；
`javadoc` ：用于从 Java 源码中自动提取注释并生成文档；
`jdb` ：Java 调试器，用于开发阶段的运行调试。

## Hello world

```java
// Hello.java
public class Hello{
  public static void main(String[] args){
    System.out.println("Hello world !");
  }
}
// javac Hello.java  # 编译，生成.class文件
// java Hello #运行字节码文件


// Java 11 可以直接运行一个Java文件

// 入口函数
// main()方法一直写到了今天：
public static void main(String args[]){}
以上的各个参数的含义如下：
· public：表示公共的内容，可以被所有操作所调用
· static：表示方法是静态的，可以由类名称直接调用。java StaticDemo09
· void：表示没有任何的返回值操作
· main：系统规定好的方法名称。如果main写错了或没有，会报错：NoSuchMethodError: main
· String[] args：字符串数组，接收命令行参数的
  args[0] 为程序名
```

注意：

- **大小写敏感**：Java 是大小写敏感的，这就意味着标识符 Hello 与 hello 是不同的。
- **类名**：对于所有的类来说，类名的首字母应该大写。如果类名由若干单词组成，那么每个单词的首字母应该大写，例如 **MyFirstJavaClass** 。
- **方法名**：所有的方法名都应该以小写字母开头。如果方法名含有若干单词，则后面的每个单词首字母大写。
- **源文件名**：源文件名必须和类名相同。当保存文件的时候，你应该使用类名作为文件名保存（切记 Java 是大小写敏感的），文件名的后缀为 **.java**。（如果文件名和类名不相同则会导致编译错误）。
- **主方法入口**：所有的 Java 程序由 **public static void main(String[] args)** 方法开始执行。
- 注释：`//单行 /*多行*/`

## java 运行机制

- 编译型
- 解释型

_.java -> java 编译器： _.class 字节码文件 -> 类加载器 -> 字节码校验器 -> 解释器 -> 操作系统平台

## java 命令

```sh
# 编译,得到.class字节码文件
javac [-encoding UTF-8] <程序名.java>
# 运行
java <程序名.class> [参数1 参数2 ...]

javac MyFirstClass.java
java MyFirstClass
# java 11 以上
java MyFirstClass.java
```

## javadoc

`@author` 标识一个类的作者，一般用于类注释 @author description
`@deprecated` 指名一个过期的类或成员，表明该类或方法不建议使用 @deprecated description
`@version` 指定类的版本，一般用于类注释 @version info
`@since` 说明从哪个版本起开始有了这个函数 @since release
`@throws` 和 @exception 标签一样. The @throws tag has the same meaning as the @exception tag.
`@return` 说明返回值类型，一般用于方法注释，不能出现再构造方法中 @return explanation
`@exception` 可能抛出异常的说明，一般用于方法注释 @exception exception-name explanation
`{@docRoot}` 指明当前文档根目录的路径 Directory Path
`{@inheritDoc}` 从直接父类继承的注释 Inherits a comment from the immediate surperclass.
`{@link}` 插入一个到另一个主题的链接 {@link name text}
`{@linkplain}` 插入一个到另一个主题的链接，但是该链接显示纯文本字体 Inserts an in-line link to another topic.
`{@value}` 显示常量的值，该常量必须是 static 属性。 Displays the value of a constant, which must be a static field.
`@param` 说明一个方法的参数，一般用于方法注释 @param parameter-name explanation
`@see` 指定一个到另一个主题的链接 @see anchor
`@serial` 说明一个序列化属性 @serial description
`@serialData` 说明通过 writeObject() 和 writeExternal() 方法写的数据 @serialData description
`@serialField` 说明一个 ObjectStreamField 组件 @serialField name type description

```sh

javadoc 参数 java 文件
javadoc [options] [packagenames] [sourcefiles]

javadoc --help

# 导出 java 文档
javadoc -encoding UTF-8 -charest UTF-8 *.java
```
## jar

```sh
https://www.cnblogs.com/mq0036/p/8566427.html#a11

$ java -jar target/myapplication-0.0.1-SNAPSHOT.jar
$ java -Xdebug -Xrunjdwp:server=y,transport=dt_socket,address=8000,suspend=n \
       -jar target/myapplication-0.0.1-SNAPSHOT.jar
```

