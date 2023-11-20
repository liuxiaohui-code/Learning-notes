# Log4j2

Log4j 2 是 log4j 1.x 和 logback 的改进版，据说采用了一些新技术（无锁异步等等），使得日志的吞吐量、性能比 log4j 1.x 提高 10 倍，并解决了一些死锁的 bug，而且配置更加简单灵活。

Log4j2 支持插件式结构，可以根据需要自行扩展 Log4j2，实现自己的 appender、logger、filter 等。在配置文件中可以引用属性，还可以直接替代或传递到组件，而且支持 json 格式的配置文件。不像其他的日志框架，它在重新配置的时候不会丢失之前的日志文件。

Log4j2 利用 Java5 中的并发特性支持，尽可能地执行最低层次的加锁。解决了在 log4j 1.x 中存留的死锁的问题。Log4j 2 是基于 LMAX Disruptor 库的。在多线程的场景下，和已有的日志框架相比，异步 logger 拥有 10 倍左右的效率提升。

# log4j2 整合日志输出

## java.util.logging.Logger

将 JUL 日志整合到 log4j2 统一输出，需要引入 log4j2 提供的依赖包：

<dependency>
          <groupId>org.apache.logging.log4j</groupId>
          <artifactId>log4j-jul</artifactId>
          <version>2.6.2</version>
      </dependency>
log4j2整合JUL日志的方式与slf4j不同，slf4j只是定义了一个handler，仍旧依赖JUL的配置文件；log4j2则直接继承重写了java.util.logging.LogManager。

使用时，只需要通过系统属性 java.util.logging.manager 绑定重写后的 LogManager（org.apache.logging.log4j.jul.LogManager）即可，感觉比 slf4j 的方式要简单不少。

## org.apache.commons.logging.Log

将 JCL 日志整合到 log4j2 统一输出，需要引入 log4j2 提供的依赖包：

<dependency>
          <groupId>org.apache.logging.log4j</groupId>
          <artifactId>log4j-jcl</artifactId>
          <version>2.6.2</version>
      </dependency>
基于log4j-jcl包整合JCL比较简单，只要把log4j-jcl包扔到classpath就可以了。看起来slf4j的整合方式优雅多了，底层原理是这样的：JCL的LogFactory在初始化的时候，查找LogFactory的具体实现，是分了几种场景的，简单描述如下：

首先根据系统属性 org.apache.commons.logging.LogFactory 查找 LogFactory 实现类；
如果找不到，则以 SPI 方式查找实现类，META-INF/services/org.apache.commons.logging.LogFactory；log4j-jcl 就是以这种方式支撑的；此种方式必须确保整个应用中，包括应用依赖的第三方 jar 包中，org.apache.commons.logging.LogFactory 文件只有一个，如果存在多个的话，哪个先被加载则以哪个为准。万一存在冲突的话，排查起来也挺麻烦的。
还找不到，则读取《commons-logging.properties》配置文件，使用 org.apache.commons.logging.LogFactory 属性指定的 LogFactory 实现类；
最后再找不到，就使用默认的实现 org.apache.commons.logging.impl.LogFactoryImpl。

## org.apache.log4j.Logger

将 log4j 1.x 日志整合到 log4j2 统一输出，需要引入 log4j2 提供的依赖包：

<dependency>
          <groupId>org.apache.logging.log4j</groupId>
          <artifactId>log4j-1.2-api</artifactId>
          <version>2.6.2</version>
      </dependency>
log4j2里整合log4j 1.x日志，也是通过覆写log4j 1.x api的方式来实现的，跟slf4j的实现原理一致。因此也就存在类库冲突的问题，使用log4j-1.2-api的话，必须把classpath下所有log4j 1.x的包清理掉。

## org.slf4j.Logger

将 slf4j 日志整合到 log4j2 统一输出，需要引入 log4j2 提供的依赖包：

<dependency>
          <groupId>org.apache.logging.log4j</groupId>
          <artifactId>log4j-slf4j-impl</artifactId>
          <version>2.6.2</version>
      </dependency>
log4j-slf4j-impl基于log4j2实现了slf4j的接口，其就是slf4j-api和log4j2-core之间的一个桥梁。这里有个问题需要注意下，务必确保classpath下log4j-slf4j-impl和log4j-to-slf4j不要共存，否则会导致事件无止尽地在SLF4J和Log4j2之间路由。
## 单独使用 Log4j2

Log4j2 感觉就是 SLF4J+Logback。log4j-api 等价于 SLF4J，log4j-core 等价于 Logback。

需要引入第三方依赖：

```xml
<dependency>
          <groupId>org.apache.logging.log4j</groupId>
          <artifactId>log4j-api</artifactId>
          <version>2.6.2</version>
      </dependency>
      <dependency>
          <groupId>org.apache.logging.log4j</groupId>
          <artifactId>log4j-core</artifactId>
          <version>2.6.2</version>
      </dependency>

```

#

log4j2-api → log4j2-cor
log4j2-api → log4j-to-slf4j → slf4j
