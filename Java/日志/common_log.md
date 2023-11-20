## Apache Commons Logging

之前叫 Jakarta Commons Logging，简称 JCL，是 Apache 提供的一个通用日志 API，可以让应用程序不再依赖于具体的日志实现工具。

commons-logging 包中对其它一些日志工具，包括 Log4J、Avalon LogKit、JUL 等，进行了简单的包装，可以让应用程序在运行时，直接将 JCL API 打点的日志适配到对应的日志实现工具中。

common-logging 通过动态查找的机制，在程序运行时自动找出真正使用的日志库。这一点与 slf4j 不同，slf4j 是在编译时静态绑定真正的 Log 实现库。

commons-logging 包里的包装类和简单实现列举如下：

```java
org.apache.commons.logging.impl.Jdk14Logger，适配 JDK1.4 里的 JUL；
org.apache.commons.logging.impl.Log4JLogger，适配 Log4J；
org.apache.commons.logging.impl.LogKitLogger，适配 avalon-Logkit；
org.apache.commons.logging.impl.SimpleLog，common-logging 自带日志实现类，它实现了 Log 接口，把日志消息都输出到系统错误流 System.err 中；
org.apache.commons.logging.impl.NoOpLog，common-logging 自带日志实现类，它实现了 Log 接口，其输出日志的方法中不进行任何操作；
如果只引入 Apache Commons Logging，也没有通过配置文件《commons-logging.properties》进行适配器绑定，也没有通过系统属性或者 SPI 重新定义 LogFactory 实现，默认使用的就是 jdk 自带的 java.util.logging.Logger 来进行日志输出。
```

JCL 使用配置文件 commons-logging.properties，可以在该文件中指定具体使用哪个日志工具。不配置的话，默认会使用 JUL 来输出日志。配置示例：

## 只使用 Apache Commons Logging

需要引入 commons-logging 包，示例如下：

<dependency>
          <groupId>commons-logging</groupId>
          <artifactId>commons-logging</artifactId>
          <version>1.2</version>
      </dependency>
▐ Apache Commons Logging和log4j结合使用

需要引入 commons-logging 包和 log4j 包，示例如下：

<dependency>
          <groupId>commons-logging</groupId>
          <artifactId>commons-logging</artifactId>
          <version>1.2</version>
      </dependency>
      <dependency>
          <groupId>log4j</groupId>
          <artifactId>log4j</artifactId>
          <version>1.2.17</version>
      </dependency>
该模式下可以使用的打点api：

org.apache.commons.logging.Log，commons-logging 里的 api；
org.apache.log4j.Logger，log4j 里的 api；
无论使用哪种 api 打点，最终日志都会通过 log4j 进行实际的日志记录。推荐用 commons-logging 里的 api，如果直接用 log4j 里的 api，就跟单用 log4j 没区别，就没有再引入 commons-logging 包的必要了。

既然最终是通过 log4j 实现日志记录，那么日志输出的 level、target 等也就是通过 log4j 的配置文件进行控制了。下面是一个 log4j 配置文件《log4j.properties》的简单示例：

#log4j.rootLogger = error,console
log4j.logger.com.suian.logtest = trace,console

#输出源 console 输出到控制台
log4j.appender.console = org.apache.log4j.ConsoleAppender
log4j.appender.console.Target = System.out
log4j.appender.console.layout = org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern = %d{yyyy-MM-dd HH:mm:ss,SSS} [%t] %-5p %c - [log4j]%m%n
既然是推荐使用 commons-logging 里的 api 打点，为了能找到 log4j 的日志实现，必须通过《commons-logging.properties》配置文件显式的确定关联，示例如下：

org.apache.commons.logging.Log=org.apache.commons.logging.impl.Log4JLogger
代码中使用 JCL api 进行日志打点，底层使用 log4j 进行日志输出。日志输出控制依托于 log4j 的配置文件，另外需要在 commons-logging.properties 配置文件中显式指定与 log4j 的绑定关系。
