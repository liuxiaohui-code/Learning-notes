# hello world

Spring Boot 是 Pivotal 团队在 Spring 的基础上提供的一套全新的开源框架，其目的是为了简化 Spring 应用的搭建和开发过程。Spring Boot 去除了大量的 XML 配置文件，简化了复杂的依赖管理。

Spring Boot 具有 Spring 一切优秀特性，Spring 能做的事，Spring Boot 都可以做，而且使用更加简单，功能更加丰富，性能更加稳定而健壮。随着近些年来微服务技术的流行，Spring Boot 也成了时下炙手可热的技术。

Spring Boot 集成了大量常用的第三方库配置，Spring Boot 应用中这些第三方库几乎可以是零配置的开箱即用（out-of-the-box），大部分的 Spring Boot 应用都只需要非常少量的配置代码（基于 Java 的配置），开发者能够更加专注于业务逻辑。

## 依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example.springboot</groupId>
    <artifactId>spring-boot-demo</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
    </properties>

    <parent>
        <!--SpringBoot父项目依赖管理,Spring Boot 的版本仲裁中心，可以对项目内的部分常用依赖进行统一管理。使用它之后，常用的包依赖可以省去version标签-->
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.7.0</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
</project>
```

## 启动

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@EnableAutoConfiguration
public class MyApplication {

    @RequestMapping("/")
    String home() {
        return "Hello World!";
    }

    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }

}
```

# Spring Boot starter

传统的 Spring 项目想要运行，不仅需要导入各种依赖，还要对各种 XML 配置文件进行配置，十分繁琐，但 Spring Boot 项目在创建完成后，即使不编写任何代码，不进行任何配置也能够直接运行，这都要归功于 Spring Boot 的 starter 机制。

Spring Boot 将日常企业应用研发中的各种场景都抽取出来，做成一个个的 starter（启动器），starter 中整合了该场景下各种可能用到的依赖，用户只需要在 Maven 中引入 starter 依赖，SpringBoot 就能自动扫描到要加载的信息并启动相应的默认配置。starter 提供了大量的自动配置，让用户摆脱了处理各种依赖和配置的困扰。所有这些 starter 都遵循着约定成俗的默认配置，并允许用户调整这些配置，即遵循“约定大于配置”的原则。

> 并不是所有的 starter 都是由 Spring Boot 官方提供的，也有部分 starter 是第三方技术厂商提供的，例如 druid-spring-boot-starter 和 mybatis-spring-boot-starter 等等。当然也存在个别第三方技术，Spring Boot 官方没提供 starter，第三方技术厂商也没有提供 starter。

## spring-boot-starter-parent

Spring Boot 的版本仲裁中心，可以对项目内的部分常用依赖进行统一管理;Spring Boot 项目可以通过继承 spring-boot-starter-parent 来获得一些合理的默认配置，它主要提供了以下特性：

-   默认 JDK 版本（Java 8）
-   默认字符集（UTF-8）
-   依赖管理功能
-   资源过滤
-   默认插件配置
-   识别 application.properties 和 application.yml 类型的配置文件

其有一个父级依赖 `spring-boot-dependencies`,spring-boot-dependencies 通过 dependencyManagement 、pluginManagement 和 properties 等元素对一些常用技术框架的依赖或插件进行了统一版本管理，例如 Activemq、Spring、Tomcat 等。

## 启动应用程序

```java
@SpringBootApplication // 标识启动类,也可以用 @EnableAutoConfiguration
public class FinReportApplication {
    public static void main(String[] args) {
      // 1. 静态方法
      SpringApplication.run(FinReportApplication.class, args);
      // 2. 自定义启动
      SpringApplication application = new SpringApplication(MyApplication.class);
      application.setBannerMode(Banner.Mode.OFF);
      // 可以设置环境变量的前缀等信息
      application.run(args);
      // 3. 自定义启动,流的方式
      new SpringApplicationBuilder()
      .sources(Parent.class)
      .child(Application.class)
      .bannerMode(Banner.Mode.OFF)
      .run(args);
    }
}
```

## @SpringBootApplication

`@SpringBootApplication` 标识启动类,指示声明一个或多个@Bean 方法并触发自动配置和组件扫描的配置类。这是一个方便的注释，相当于声明@Configuration、@EnableAutoConfiguration 和@ComponentScan。

参数: exclude = { DataSourceAutoConfiguration.class } 可以排除一些类自动注入

### @ComponentScan 组件扫描

扫描 `@Component`, `@Service`, `@Repository`, `@Controller`, 和其他组件进行注册.

只有一个构造函数,组件默认使用其来注册;如果有多个构造函数,可以用`@Autowired` 放在构造函数上标识使用那个构造函数初始化.

```java
import java.io.PrintStream;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class MyAccountService implements AccountService {

    private final RiskAssessor riskAssessor;

    private final PrintStream out;

    @Autowired
    public MyAccountService(RiskAssessor riskAssessor) {
        this.riskAssessor = riskAssessor;
        this.out = System.out;
    }

    public MyAccountService(RiskAssessor riskAssessor, PrintStream out) {
        this.riskAssessor = riskAssessor;
        this.out = out;
    }

    // ...

}
```

### @EnableAutoConfiguration

### @SpringBootConfiguration

@SpringBootConfiguration 是 springboot @Configuration 的替代方案

Spring Boot 抛弃了传统 xml 配置文件，通过配置类（标注 @Configuration 的类，相当于一个 xml 配置文件）以 JavaBean 形式进行相关配置。

## 事件监听

```Java
// 1.应用启动时，还未启动，此时监听器与初始化程序已注册
private final ApplicationStartingEvent applicationStartingEvent;
// 2.当上下文中要使用的环境已知但在创建上下文之前
private final ApplicationEnvironmentPreparedEvent applicationEnvironmentPreparedEvent;
// 3.在准备ApplicationContext并且调用了ApplicationContextInitializers之后，但在加载任何bean定义之前，
private final ApplicationContextInitializedEvent applicationContextInitializedEvent;
// 4.bean 加载后，刷新开始前
private final ApplicationPreparedEvent applicationPreparedEvent;
// 5.已经刷新，但是应用和命令行运行器未被调用
private final ApplicationStartedEvent applicationStartedEvent;
// 6.LivenessState.CORRECT（该应用程序被认为是实时的。；
// 8.ReadinessState.ACCEPTING_TRAFFIC 应用程序已准备好服务请求。
private final AvailabilityChangeEvent availabilityChangeEvent;
// 7.应用和命令行运行器被调用之后
private final ApplicationReadyEvent applicationReadyEvent;
// 9.启动异常时发送
private final ApplicationFailedEvent applicationFailedEvent;
// 以上为监听springboot上下文的事件，下面为4和5之间被发送
// web服务器就绪，
private final WebServerInitializedEvent webServerInitializedEvent;
// ApplicationContext 被刷新
private final ContextRefreshedEvent contextRefreshedEvent;
```

## 命令行参数

-   @AutoWired 自动装配 ApplicationArguments 实例,通过`containsOption(flag)`方法确定是否存在参数,
-   springboot 注册 CommandLinePropertySource,可以使用@Value 注解直接使用命令行参数

## ApplicationRunner or CommandLineRunner

在事件 ApplicationStartedEvent 之后调用,ApplicationRunner 与 CommandLineRunner 具有相同的意义.

```java
@Component
//@Order(2) 如果有多个类需要启动后执行 order注解中的值为启动的顺序
public class StartAllJobInit implements CommandLineRunner {
    @Override
    public void run(String... args) throws Exception {}
}
```

`run` 方法参数是 `ApplicationArguments` ,解析封装过后的 `args` 参数， 通过该对象既可以拿到原始命令行参数，也可以拿到解析后的参数， 其中 `@Order` 中的值指定了执行顺序，值小的先执行。默认值是 `Integer.MAX_VALUE`

```java
@Component
@Order(1)
public class MyApplicationRunner implements ApplicationRunner {
    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println("====MyApplicationRunner");
        System.out.println("order值： 1");
        System.out.println("原始参数："+Arrays.asList(args.getSourceArgs()));
        Set<String> keys = args.getOptionNames();
        for (String key : keys) {
            System.out.println("解析后的key: ["+key+"]  value: "+args.getOptionValues(key));
        }
        System.out.println("无OptionName的参数： "+args.getNonOptionArgs());
        System.out.println("=======");
    }
```

## 退出

```Java
import org.springframework.boot.ExitCodeGenerator;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;

@Configuration
public class MyApplication {
    @Bean
    public ExitCodeGenerator exitCodeGenerator() {
      //This exit code can then be passed to System.exit() to return it as a status code
        return () -> 42;
    }
}


```

# 配置

## @EnableAutoConfiguration

根据使用的 starter,推测需要的注解,可以代替启动类注解@SpringBootApplication

## 外部化配置

### 优先级

配置属性顺序: (优先级从小到大)

1. 默认属性（通过设置 SpringApplication.setDefaultProperties 指定）
2. @PropertySource annotations on your @Configuration classes.
3. 配置文件 application.properties
4. RandomValuePropertySource `random.*`
5. 操作系统环境变量
6. System.getProperties()
7. JNDI attributes from `java:comp/env/spring.application.json`
8. ServletContext init parameters.
9. ServletConfig init parameters.
10. SPRING_APPLICATION_JSON 中的属性（内嵌在环境变量或系统属性中的 JSON）,例如 my.name=test: `SPRING_APPLICATION_JSON='{"my":{"name":"test"}}'`,`-Dspring.application.json='{"my":{"name":"test"}}'`,`--spring.application.json='{"my":{"name":"test"}}'`
11. Command line arguments. 以`--`开头,例如`--server.port=9000`,可以禁用:`SpringApplication.setAddCommandLineProperties(false)`
12. @SpringBootTest
13. @TestPropertySource

### 配置文件优先级

默认加载的配置文件生效顺序: (优先级从小到大,同一级 properties 文件优先级更高,高优先级会覆盖低优先级),如果不想用 application 的名字,可以使用`--spring.config.name=my`的命令行参数修改

1. classpath:application.yaml,application.properties
2. classpath:application-{profile}.yaml,application-{profile}.properties
3. classpath:config/application.yaml,application.properties
4. classpath:config/application-{profile}.yaml,application-{profile}.properties
5. ./application.yaml , ./application.properties
6. ./application-{profile}.yaml , ./application-{profile}.properties
7. ./config/~
8. ./config/config/~

### 命令行加载配置文件路径

1. `java -jar {JAR} --spring.config.location={外部配置文件全路径}` 需要注意的是，使用该参数指定配置文件后，会使项目默认配置文件（application.properties 或 application.yml ）失效，Spring Boot 将只加载指定的外部配置文件。
2. `java -jar {JAR} --spring.config.additional-location={外部配置文件全路径}` 但与 --spring.config.location 不同，--spring.config.additional-location 不会使项目默认的配置文件失效，使用该命令行参数添加的外部配置文件会与项目默认的配置文件共同生效，形成互补配置，且其优先级是最高的，比所有默认配置文件的优先级都高。
3. 如果路径时文件夹,应以`/`结束
4. 路径使用`optional:`前缀,文件不存在时不会报错,如`optional:file:./config/`,或者设置`spring.config.on-not-found=ignore`忽略此错误
5. 通配符匹配`/config/*/`,匹配`/config/mysql/applicaiton.yaml`与`/config/redis/applicaiton.yaml`,通配符只能有一个,且以`*/`或`*/<filename>`结尾,通配符只能在外部文件使用,不能在 classpath 路径使用
6. 多个配置文件路径 `$ java -jar myproject.jar --spring.config.location=optional:classpath:/default.properties,optional:classpath:/override.properties`
7. `--spring.profiles.active=prod,live` 使用最后获胜原则,都会加载,但是 application-live.yaml 会覆盖 application-prod.yaml.如果不设置--spring.profiles.active,那么会考虑 application-default.yaml.

## 配置文件语法

-   文件后缀: yaml,yml,properties
-   文件内可以使用占位符 `${app.name}`来表示环境变量或系统变量 app.name 的值,也可以使用`{app.name:deafult}`设置缺省值;另外,`${demo.item-price}`可以匹配`demo.item-price`和`demo.itemPrice`和环境变量`DEMO_ITEMPRICE`,但是`${demo.itemPrice}`只能匹配`demo.item-price`
-   单文件多配置,yaml 文件使用`---`分割,properties 使用`#---`或`!---`分割
-   属性名:字母,数字,.,-;除此之外的符号需要使用`[]`包含才能显示
-   建议使用`-`连接
-

## spring 配置

[全部配置](https://docs.spring.io/spring-boot/docs/2.7.6/reference/html/application-properties.html#appendix.application-properties.core)

### bean 懒加载

```yaml
spring:
    main:
        lazy-initialization: true # bean 在第一次调用时初始化，而不随着启动初始化
```

### banner 日志横幅

```yaml
spring:
    banner:
        location: classpath:banner.txt # 默认为classpath下的banner.txt文件,也可以修改覆盖
        charset: utf-8
        image:
            location: banner.gif|banner.jpg|banner.png # 显示图片
    main:
        banner-mode: console # off |console |log # 控制是否打印banner,或用什么打印
```

banner.txt 可以使用以下变量打印信息:
`${application.version}` MANIFEST.MF 文件显示的版本信息
`${application.formatted-version} `MANIFEST.MF 文件显示的格式化版本信息
`${application.title} ` MANIFEST.MF 显示的标题
`${spring-boot.version}` springboot 版本
`${spring-boot.formatted-version} ` springboot 版本
`${Ansi.NAME}` (or `${AnsiColor.NAME}`, `${AnsiBackground.NAME}`, `${AnsiStyle.NAME}`)

代码实现 banner:

1. 实现接口: `org.springframework.boot.Banner` 中`printBanner()`自定义文本显示
2. 调用静态方法设置: `SpringApplication.setBanner(…​)`

banner 注册的 bean 名: springBootBanner,只能在 springboot 启动时才能打印 springboot 版本信息 `java org.springframework.boot.loader.JarLauncher`

### application 应用基本信息

```yaml
spring:
    application:
        name: 应用名
    app:
        name: "MyApp"
        description: "${app.name} is a Spring Boot application written by ${username:Unknown}"
```

### config 配置激活与子配置文件

```yaml
spring:
  config:
    import: "optional:file:./dev.properties" # 插入子配置文件,子配置文件要比触发其的父文件优先级高,也会导入dev-{}.properties
    import: "file:/etc/config/myconfig[.yaml]" # 以yaml的方式导入/etc/config/myconfig文件
    import: "optional:configtree:/etc/config/" # 导入文件夹/etc/config/下的所有配置文件,
    import: "optional:configtree:/etc/config/*/" #可以使用一个通配符*

    activate: # 激活配置
      on-profile: dev # 仅当 spring.profiles.active=prod 时激活
      on-cloud-platform: "kubernetes" # 云环境为kubernetes时激活使用
```

### messages

```yaml
spring:
    messages:
        basename: messages # 指定message的basename，多个以逗号分隔，如果不加包名的话，默认从classpath路径开始，默认: messages
        cache-seconds: -1 # 设定加载的资源文件缓存失效时间，-1的话为永不过期，默认为-1
        encoding: UTF-8 # 设定Message bundles的编码，默认: UTF-8
```

### resources

```yaml
spring.resources.add-mappings
是否开启默认的资源处理，默认为true

spring.resources.cache-period
设定资源的缓存时效，以秒为单位.

spring.resources.chain.cache
是否开启缓存，默认为: true

spring.resources.chain.enabled
是否开启资源 handling chain，默认为false

spring.resources.chain.html-application-cache
是否开启h5应用的cache manifest重写，默认为: false

spring.resources.chain.strategy.content.enabled
是否开启内容版本策略，默认为false

spring.resources.chain.strategy.content.paths
指定要应用的版本的路径，多个以逗号分隔，默认为:[/**]

spring.resources.chain.strategy.fixed.enabled
是否开启固定的版本策略，默认为false

spring.resources.chain.strategy.fixed.paths
指定要应用版本策略的路径，多个以逗号分隔

spring.resources.chain.strategy.fixed.version
指定版本策略使用的版本号

spring.resources.static-locations
指定静态资源路径，默认为classpath:[/META-INF/resources/,/resources/, /static/, /public/]以及context:/
```

### multipart

```yaml
multipart.enabled
是否开启文件上传支持，默认为true

multipart.file-size-threshold
设定文件写入磁盘的阈值，单位为MB或KB，默认为0

multipart.location
指定文件上传路径.

multipart.max-file-size
指定文件大小最大值，默认1MB

multipart.max-request-size
指定每次请求的最大值，默认为10MB
```

### http

```yaml
spring.hateoas.apply-to-primary-object-mapper
设定是否对object mapper也支持HATEOAS，默认为: true

spring.http.converters.preferred-json-mapper
是否优先使用JSON mapper来转换.

spring.http.encoding.charset
指定http请求和相应的Charset，默认: UTF-8

spring.http.encoding.enabled
是否开启http的编码支持，默认为true

spring.http.encoding.force
是否强制对http请求和响应进行编码，默认为true
```

### json

spring boot 提供了三种 json 解析方式: gson,jackson(默认),json-b;

spring-boot-starter-json,当 jackson 在类路径,会自动装配 `ObjectMapper`

```yaml
spring:
    jackson:
        date-format: "yyyy-MM-dd HH:mm:ss" #指定日期格式，比如yyyy-MM-dd HH:mm:ss，或者具体的格式化类的全限定名
        deserialization: true # 是否开启Jackson的反序列化
        generator: true # 是否开启json的generators.
        joda-date-time-format: "yyyy-MM-dd HH:mm:ss" #指定Joda date/time的格式，比如). 如果没有配置的话，dateformat会作为backup
        locale: # 指定json使用的Locale.
        mapper: true # 是否开启Jackson通用的特性.
        parser: true # 是否开启jackson的parser特性.
        property-naming-strategy: #指定PropertyNamingStrategy (CAMEL_CASE_TO_LOWER_CASE_WITH_UNDERSCORES)或者指定PropertyNamingStrategy子类的全限定类名.
        serialization: true # 是否开启jackson的序列化.
        serialization-inclusion: #指定序列化时属性的inclusion方式，具体查看JsonInclude.Include枚举.
        time-zone: #指定日期格式化时区，比如America/Los_Angeles或者GMT+10.
```

#### @JsonComponet

1. @JsonComponent 类会被自动注入
2. @JsonComponent 可以直接在 JsonSerializer,JsonDeserializer,KeyDeserializer 实现类上使用，也可以包含多个连续类
3. springboot 也提供了 JsonObjectSerializer 和 JsonObjectDeserializer 接口，使用类似 JsonSerializer
4. @JsonMixin

```java
import java.io.IOException;

import com.fasterxml.jackson.core.JsonGenerator;
import com.fasterxml.jackson.core.JsonParser;
import com.fasterxml.jackson.core.ObjectCodec;
import com.fasterxml.jackson.databind.DeserializationContext;
import com.fasterxml.jackson.databind.JsonDeserializer;
import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.JsonSerializer;
import com.fasterxml.jackson.databind.SerializerProvider;

import org.springframework.boot.jackson.JsonComponent;

@JsonComponent
public class MyJsonComponent {
    public static class Serializer extends JsonSerializer<MyObject> {
        @Override
        public void serialize(MyObject value, JsonGenerator jgen, SerializerProvider serializers) throws IOException {
            jgen.writeStartObject();
            jgen.writeStringField("name", value.getName());
            jgen.writeNumberField("age", value.getAge());
            jgen.writeEndObject();
        }
    }
    public static class Deserializer extends JsonDeserializer<MyObject> {
        @Override
        public MyObject deserialize(JsonParser jsonParser, DeserializationContext ctxt) throws IOException {
            ObjectCodec codec = jsonParser.getCodec();
            JsonNode tree = codec.readTree(jsonParser);
            String name = tree.get("name").textValue();
            int age = tree.get("age").intValue();
            return new MyObject(name, age);
        }
    }
}
```

### profile 环境激活

1. 可以配置配置项 spring.profiles
2. 可以在 application 启动前设置`SpringApplication.setAdditionalProfiles(…​)`激活
3. 可以通过实现接口`ConfigurableEnvironment`
4. 可以通过`@Profile`注解根据`spring.profiles.active`的值决定是否加载类为 springbean

```yaml
spring:
    profiles:
        default: "none" # active 为空时生效
        active: "prod,live" # 后者优先，常用命令行参数配置，不能在含有后缀的配置文件中配置
        inclues:
            - "common" # 优先级最高
            - "local"
        group:
            production: # 相当于命令行参数 --spring.profiles.active=production,但可以启动production、proddb、prodmp三种配置文件
                - "proddb"
                - "prodmq"
```

```java
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;

@Configuration(proxyBeanMethods = false)
@Profile("production") // 可以用在 @Component, @Configuration or @ConfigurationProperties 类上
// `spring.profiles.active=production` 激活
public class ProductionConfiguration {
    // ...
}
```

### lifecycle

```yaml
spring:
    lifecycle:
        timeout-per-shutdown-phase: "20s" # server 优雅关闭的超时时间
```

## server 配置

```yaml
server:
    port: 8888 # 端口
    address: 0.0.0.0 # 请求地址
    shutdown: "graceful" # 优雅关闭
    tomcat:
        uri-encoding: utf-8 # 资源字符集
    compression:
        enabled: true # //是否开启压缩，默认为false.
        excluded-user-agents: text/html,text/xml,text/plain,text/css #指定不压缩的user-agent，多个以逗号分隔，默认值为:text/html,text/xml,text/plain,text/css
        mime-types: #指定要压缩的MIME type，多个以逗号分隔.
        min-response-size: 2048 #执行压缩的阈值，默认为2048
    context-parameters:
        - [param name] #设置servlet context 参数
    context-path: #设定应用的context-path.（如设置context-path=server.context-path，所以访问的路径前缀都要有/springboot。）
    display-name: #设定应用的展示名称，默认: application
    jsp-servlet:
        class-name: #设定编译JSP用的servlet，默认: org.apache.jasper.servlet.JspServlet)
        init-parameters: [param name] #设置JSP servlet 初始化参数.
        registered: #设定JSP servlet是否注册到内嵌的servlet容器，默认true
    servlet-path: #设定dispatcher servlet的监听路径，默认为: /
```

## logging 日志配置

默认:

1. 默认使用 logback 实现 slf4fj
2. 默认写入控制台,不写入文件

```yaml
debug: true # 默认打印info,warn,error;开启debug日志
spring:
    output:
        ansi:
            enabled: ALWAYS # DETECT(默认), ALWAYS(支持彩色日志%clr),NEVER;
            # 颜色使用格式 %clr(%d{yyyy-MM-dd HH:mm:ss.SSS}){yellow}
            # 支持的颜色: blue cyan faint green magenta red yellow
logging:
    config: ./logback-spring.xml # 自定义日志配置文件,建议使用-spring后缀,否则log将在spring启动前注册,spring无法管理log
    level:
        root: "warn" # 缺省日志级别 TRACE, DEBUG, INFO, WARN, ERROR, FATAL, or OFF
        org.springframework.web: "debug" # 对于某个包单独级别,设置环境变量同样有效LOGGING_LEVEL_ORG_SPRINGFRAMEWORK_WEB=DEBUG
        org.hibernate: "error"
        tomcat: "trace" # group 级别
    group:
        tomcat: "org.apache.catalina,org.apache.coyote,org.apache.tomcat"
        # spring boot 默认定义组
        web: org.springframework.core.codec, org.springframework.http, org.springframework.web, org.springframework.boot.actuate.endpoint.web, org.springframework.boot.web.servlet.ServletContextInitializerBeans
        sql: org.springframework.jdbc.core, org.hibernate.SQL, org.jooq.tools.LoggerListener
    register-shutdown-hook: true # jvm 关闭时释放log资源
    pattern:
        console: "%d{yyyy-MM-dd hh:MM:ss} %highlight(%-5level) %thread  %caller{1} %clr(%msg){green} %n"
        file:
        level:
    charset:
        console: utf-8
        file:
    file: # 配置file的name或path其一后,控制台日志同时会写入日志文件,文件达到10mb会旋转
        name: my.log # 在根目录生成,可以附带文件路径
        path: /var/log # 文件夹名,默认文件为 spring.log
    logback: # 如果使用logback
        rollingpolicy: # 文件滚动策略
            file-name-pattern:
            clean-history-on-start:
            max-file-size:
            total-size-cap:
            max-history: 7
```

## 属性加密

EnvironmentPostProcessor 接口

## 配置文件使用随机数

`RandomValuePropertySource`

```yaml
my:
    secret: "${random.value}"
    number: "${random.int}"
    bignumber: "${random.long}"
    uuid: "${random.uuid}"
    number-less-than-ten: "${random.int(10)}" # 最大为10
    number-in-range: "${random.int[1024,65536]}" # 1024-65535
```

## 配置文件使用时间

1. `java.time.Duration` 时间间隔
    1. 配置 long ,默认毫秒,除非用`@DurationUnit`定义单位
    2. 标准 ISO-8601 字符串 https://docs.oracle.com/javase/8/docs/api/java/time/Duration.html#parse-java.lang.CharSequence-
    3. 一种可读的格式 `10s` ns us ms s m h d
2. `java.time.Period` 日期间隔
    1. int,默认为天,`@PeriodUnit`定义单位
    2. ISO-8601 字符串 https://docs.oracle.com/javase/8/docs/api/java/time/Period.html#parse-java.lang.CharSequence-
    3. 可读格式 `1y3d` y 年 m 月 w 周 d 日

## 配置文件使用 DataSize

spring 有一个 DataSize 类型用来表明字节大小,

1. long 使用@DataSizeUnit(DataUnit.MEGABYTES)定义单位,默认为 B
2. 10MB , B KB MB GB TB

## task 任务执行与调度

```yaml
spring:
    task:
        execution:
            pool:
                max-size: 16 # 线程数量，默认8
                queue-capacity: 100 # 任务容量
                keep-alive: "10s" # 空闲时间,空闲10s则回收线程,默认60s
        scheduling:
            thread-name-prefix: "scheduling-"
            pool:
                size: 2
```

## 属性绑定(配置文件,环境变量...)

所谓“配置绑定”就是把配置文件中的值与 JavaBean 中对应的属性进行绑定。

### @ConfigurationProperties 配置序列化绑定

@ConfigurationProperties：告诉 SpringBoot 将本类中的所有属性和配置文件中相关的配置进行绑定；
特点：

-   标注在 JavaBean 的类名上
-   prefix = "person"：配置文件中哪个下面的所有属性进行一一映射
-   用于批量绑定配置文件中的配置；
-   支持松散绑定（松散语法），例如实体类 Person 中有一个属性为 firstName，那么配置文件中的属性名支持以下写法：
    -   person.firstName
    -   person.first-name
    -   person.first_name
    -   PERSON_FIRST_NAME
-   不支持 SpEL 表达式
-   支持所有类型数据的封装，例如 Map、List、Set、以及对象等
    -   map 类型,必须有 getter,不必须 setter
    -   list 或 set 类型,建议有 setter,有初始化的话不能用 final 修饰
-   若专门编写了一个 JavaBean 来和配置文件进行映射，则建议使用 @ConfigurationProperties 注解。
-   不支持绑定静态属性

注册为 spring bean:

1. 使用 `@EnableConfigurationProperties`注解将`@ConfigurationProperties`注册为 spring bean,如`@EnableConfigurationProperties({MyProperties.class})`,该注解用在任意`@Configation`类上
2. 使用`@ConfigurationPropertiesScan({ "com.example.app", "com.example.another" })`扫描包下的所有`@ConfigurationProperties`注册为 spring bean.该注解在启动类上使用
3. 直接在 Spring bean 上使用`@ConfigurationProperties`

```java
import java.net.InetAddress;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

import org.springframework.boot.context.properties.ConfigurationProperties;

@ConfigurationProperties("my.service")
@Data
public class MyProperties {
  // 绑定 my.service.enabled
  private boolean enabled;
  // my.service.remote-address
  private InetAddress remoteAddress;
  // my.service.security,如果希望自动初始化,需要添加setter
  private final Security security = new Security();

  public static class Security {
    //my.service.security.username
    private String username;
    //my.service.security.password
    private String password;
    //my.service.security.roles
    private List<String> roles = new ArrayList<>(Collections.singleton("USER"));
  }
}

```

#### @ConstructorBinding 使用构造函数绑定

1. `@ConstructorBinding` 在 Java16 以及更高版本可以省略
2. `@DefaultValue` 可以用在构造函数的参数上,用于指定缺省值
3. 使用前提:
    1. 开启: `@EnableConfigurationProperties`
    2. 不能用于常规的 bean 上,如@Component @Bean @Import
4. 如果有多个构造函数,可以直接将`@ConstructorBinding`放在需要绑定的构造函数上
5. 不建议使用类型 java.util.Optional,如果空值会返回 null,而不是 empty Optional

```Java
import java.net.InetAddress;
import java.util.List;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.boot.context.properties.ConstructorBinding;
import org.springframework.boot.context.properties.bind.DefaultValue;

@ConstructorBinding
@ConfigurationProperties("my.service")
public class MyProperties {
    private final boolean enabled;
    private final InetAddress remoteAddress;
    private final Security security;
    public MyProperties(boolean enabled, InetAddress remoteAddress, Security security) {
        this.enabled = enabled;
        this.remoteAddress = remoteAddress;
        this.security = security;
    }
    public boolean isEnabled() {
        return this.enabled;
    }
    public InetAddress getRemoteAddress() {
        return this.remoteAddress;
    }
    public Security getSecurity() {
        return this.security;
    }
    public static class Security {
        private final String username;
        private final String password;
        private final List<String> roles;
        public Security(String username, String password, @DefaultValue("USER") List<String> roles) {
            this.username = username;
            this.password = password;
            this.roles = roles;
        }
        public String getUsername() {
            return this.username;
        }
        public String getPassword() {
            return this.password;
        }
        public List<String> getRoles() {
            return this.roles;
        }
    }
}

```

#### @Validation 校验

```java
import java.net.InetAddress;

import javax.validation.Valid;
import javax.validation.constraints.NotEmpty;
import javax.validation.constraints.NotNull;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.validation.annotation.Validated;

@Component
@ConfigurationProperties("my.service")
@Validated // 校验字段
public class MyProperties {
    @NotNull // 不为空
    private InetAddress remoteAddress;
    @Valid // 字段嵌套时必须,
    private final Security security = new Security();
    public InetAddress getRemoteAddress() {
        return this.remoteAddress;
    }
    public void setRemoteAddress(InetAddress remoteAddress) {
        this.remoteAddress = remoteAddress;
    }
    public Security getSecurity() {
        return this.security;
    }
    // 必须为静态类
    public static class Security {
        @NotEmpty
        private String username;
        public String getUsername() {
            return this.username;
        }
        public void setUsername(String username) {
            this.username = username;
        }
    }
}


```

### @Value 单属性绑定

当我们只需要读取配置文件中的某一个配置时，可以通过 @Value 注解获取。
特点：

-   标注在 JavaBean 的属性上
-   只能一个一个的指定需要绑定的配置
-   不支持松散绑定
-   支持 SpEL 表达式
-   只支持基本数据类型的封装，例如字符串、布尔值、整数等类型
-   若只是获取配置文件中的某项值，则推荐使用 @Value 注解
-   使用这个类时，只能通过依赖注入的方式，用 new 的方式是不会自动注入这些配置的。

```java
import org.springframework.beans.factory.annotation.Value;

@Component   //适用前提是IOC容器
public class XX{
    @Value("${配置项:默认值}")
    private int x;
}
```

### @PropertySource

如果将所有的配置都集中到 application.properties 或 application.yml 中，那么这个配置文件会十分的臃肿且难以维护，因此我们通常会将与 Spring Boot 无关的配置（例如自定义配置）提取出来，写在一个单独的配置文件中，并在对应的 JavaBean 上使用 @PropertySource 注解指向该配置文件。

```java
@Component
@PropertySource(value = "classpath:person.properties")//指向对应的配置文件
@ConfigurationProperties(prefix = "person")
```

### Environment 对象获取配置项

```java
import org.springframework.core.env.Environment;

    ...
    @Autowired
    private Environment environment;
    //使用Environment对象获取配置文件的值，最好使用带默认值的方法：getProperty(“配置项key”,“默认值”)，避免null值
    //使用Environment对象还可以获取到一些系统的启动信息
    ...
    @PostConstruct
    public void testEnvironment() {
        System.out.println("-------------------Environment测试开始-------------------");
        System.out.println("Environment测试获取的系统组：" + environment.getProperty("envir.system.group"));
        //如果配置文件未设置该key的值，则使用默认值
        System.out.println("Environment测试获取的默认值设置：" + environment.getProperty("envir.system.init", "未设置初始化参数"));
        System.out.println("-------------------Environment测试结束-------------------");
    }
```

### 环境变量格式

1. 环境变量用`_`代替`.`
2. 删除`-`
3. 大写

example:

```s
SPRING_MAIN_LOGSTARTUPINFO # spring.main.log-startup-info
MY_SERVICE_0_OTHER # my.service[0].other
```

也可以使用 @Value 获取

### 原生方式获取

```java
package com.alian.properties.service;

import org.springframework.stereotype.Service;

import javax.annotation.PostConstruct;
import java.io.IOException;
import java.io.InputStreamReader;
import java.nio.charset.StandardCharsets;
import java.util.Objects;
import java.util.Properties;

@Service
public class LoadPropertiesService {

    @PostConstruct
    public void testLoadProperties() {
        System.out.println("-------------------LoadProperties测试开始: 文件load.properties-------------------");
        Properties props = new Properties();
        try {
            InputStreamReader inputStreamReader = new InputStreamReader(Objects.requireNonNull(this.getClass().getClassLoader().getResourceAsStream("load.properties")), StandardCharsets.UTF_8);
            props.load(inputStreamReader);
        } catch (IOException e1) {
            e1.printStackTrace();
        }
        System.out.println("LoadProperties测试获取的功能名称：" + props.getProperty("function.name"));
        System.out.println("LoadProperties测试获取的功能描述：" + props.getProperty("function.desp"));
        System.out.println("-------------------LoadProperties测试开始-------------------");
    }
}

```

### yaml 文件加载

1. `YamlPropertiesFactoryBean` yaml 转 properties
2. `YamlMapFactoryBean` yaml 转 Map
3. `YamlPropertySourceLoader` yaml 转 PropertySource

# spring-boot-starter-web

它能够为提供 Web 开发场景所需要的几乎所有依赖，因此在使用 Spring Boot 开发 Web 项目时，只需要引入该 Starter 即可，而不需要额外导入 Web 服务器和其他的 Web 依赖。

Spring Boot 为 Spring MVC 提供了自动配置，并在 Spring MVC 默认功能的基础上添加了以下特性：

-   引入了 ContentNegotiatingViewResolver 和 BeanNameViewResolver（视图解析器）
-   对包括 WebJars 在内的静态资源的支持
-   自动注册 Converter、GenericConverter 和 Formatter （转换器和格式化器）
-   对 HttpMessageConverters 的支持（Spring MVC 中用于转换 HTTP 请求和响应的消息转换器）
-   自动注册 MessageCodesResolver（用于定义错误代码生成规则）
-   支持对静态首页（index.html）的访问
-   自动使用 ConfigurableWebBindingInitializer

Spring Boot 对 Spring MVC 的自动配置可以满足我们的大部分需求，但是我们也可以通过自定义配置类（标注 `@Configuration` 的类）并实现 `WebMvcConfigurer` 接口来定制 Spring MVC 配置，例如拦截器、格式化程序、视图控制器等等。

SpringBoot 1.5 及以前是通过继承 WebMvcConfigurerAdapter 抽象类来定制 Spring MVC 配置的，但在 SpringBoot 2.0 后，WebMvcConfigurerAdapter 抽象类就被弃用了，改为实现 WebMvcConfigurer 接口来定制 Spring MvVC 配置。

## 例子

### 路径与实现一体

```java
import java.util.List;

import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/users")
public class MyRestController {

    private final UserRepository userRepository;

    private final CustomerRepository customerRepository;

    public MyRestController(UserRepository userRepository, CustomerRepository customerRepository) {
        this.userRepository = userRepository;
        this.customerRepository = customerRepository;
    }

    @GetMapping("/{userId}")
    public User getUser(@PathVariable Long userId) {
        return this.userRepository.findById(userId).get();
    }

    @GetMapping("/{userId}/customers")
    public List<Customer> getUserCustomers(@PathVariable Long userId) {
        return this.userRepository.findById(userId).map(this.customerRepository::findByUser).get();
    }

    @DeleteMapping("/{userId}")
    public void deleteUser(@PathVariable Long userId) {
        this.userRepository.deleteById(userId);
    }
}
```

### 路径与实现分离

```Java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.MediaType;
import org.springframework.web.servlet.function.RequestPredicate;
import org.springframework.web.servlet.function.RouterFunction;
import org.springframework.web.servlet.function.ServerResponse;

import static org.springframework.web.servlet.function.RequestPredicates.accept;
import static org.springframework.web.servlet.function.RouterFunctions.route;

@Configuration(proxyBeanMethods = false)
public class MyRoutingConfiguration {
    private static final RequestPredicate ACCEPT_JSON = accept(MediaType.APPLICATION_JSON);
    @Bean
    public RouterFunction<ServerResponse> routerFunction(MyUserHandler userHandler) {
        return route()
                .GET("/{user}", ACCEPT_JSON, userHandler::getUser)
                .GET("/{user}/customers", ACCEPT_JSON, userHandler::getUserCustomers)
                .DELETE("/{user}", ACCEPT_JSON, userHandler::deleteUser)
                .build();
    }

}

import org.springframework.stereotype.Component;
import org.springframework.web.servlet.function.ServerRequest;
import org.springframework.web.servlet.function.ServerResponse;

@Component
public class MyUserHandler {
    public ServerResponse getUser(ServerRequest request) {
        ...
        return ServerResponse.ok().build();
    }
    public ServerResponse getUserCustomers(ServerRequest request) {
        ...
        return ServerResponse.ok().build();
    }
    public ServerResponse deleteUser(ServerRequest request) {
        ...
        return ServerResponse.ok().build();
    }
}
```

## 编译 jar 或 war

### jar

```xml
<packaging>jar</packaging>
<build>
    <finalName>api</finalName>
    <resources>
        <resource>
            <directory>src/main/resources</directory>
            <filtering>true</filtering>
        </resource>
    </resources>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <fork>true</fork>
                <mainClass>com.lynn.yiyi.Application</mainClass>
            </configuration>
            <executions>
                <execution>
                    <goals>
                        <goal>repackage</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
        <plugin>
            <artifactId>maven-resources-plugin</artifactId>
            <version>2.5</version>
            <configuration>
                <encoding>UTF-8</encoding>
                <useDefaultDelimiters>true</useDefaultDelimiters>
            </configuration>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <version>2.18.1</version>
            <configuration>
                <skipTests>true</skipTests>
            </configuration>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>2.3.2</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
            </configuration>
        </plugin>
    </plugins>
</build>

```

```java

```

### war

```xml
<packaging>war</packaging>

<build>
    <finalName>index</finalName>
    <resources>
        <resource>
            <directory>src/main/resources</directory>
            <filtering>true</filtering>
        </resource>
    </resources>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
        <plugin>
            <artifactId>maven-resources-plugin</artifactId>
            <version>2.5</version>
            <configuration>
                <encoding>UTF-8</encoding>
            </configuration>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <version>2.18.1</version>
            <configuration>
                <skipTests>true</skipTests>
            </configuration>
        </plugin>
    </plugins>
</build>
```

```java
@RestController
@SpringBootApplication
public class HelloController extends SpringBootServletInitializer{

@RequestMapping("hello")
String hello() {
return "Hello World!";
}

public static void main(String[] args) {
SpringApplication.run(HelloController.class, args);
}
@Override
protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
return application.sources(Application.class);
}

}
```

## 常用注解

1. `@RestController` 标明一个控制器组件
2. `@RequestMapping` 标明一个控制器根路径或一个方法的访问路径与方式
3. `@GetMapping|DeleteMapping|PostMapping|PutMappting` 对应方法的 api,@RequestMapping 注解的子注解
4. `@PathVariable` 获取路径参数
5. `@RequestBody` 获取请求体参数
6. `@RequestParam` 获取 query 参数

## 自定义请求与返回体转换器 json|xml

1. 默认使用 utf-8

```java
import org.springframework.boot.autoconfigure.http.HttpMessageConverters;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.converter.HttpMessageConverter;

@Configuration(proxyBeanMethods = false)
public class MyHttpMessageConvertersConfiguration {

    @Bean
    public HttpMessageConverters customConverters() {
        HttpMessageConverter<?> additional = new AdditionalHttpMessageConverter();
        HttpMessageConverter<?> another = new AnotherHttpMessageConverter();
        return new HttpMessageConverters(additional, another);
    }

}
```

## 静态资源

springboot 默认静态资源目录:

1. 类路径 classpath: `/static` (or `/public` or `/resources` or `/META-INF/resources`)
2. 上下文根目录的以上文件夹
3. 使用`ResourceHttpRequestHandler`类,可以通过实现`WebMvcConfigurer`接口覆盖方法`addResourceHandlers`修改 addResourceLocations
4. 使用`spring.web.resources.static-locations=new_dir_path`自定义,需要加前缀 classpath 或 file
5. 欢迎页为固定的 index.html,图标为 favicon.ico
6. 默认错误页`/error`,自定义错误页面,可以在静态目录下添加 error 文件夹,添加以错误码命名的 html 文件,如: 404.html

```yaml
spring:
    mvc:
        async:
            request-timeout: 10000 #设定async请求的超时时间，以毫秒为单位，
        date-format: "yyyy-MM-dd" # 设定日期的格式，比如dd/MM/yyyy:
        static-path-pattern: "/resources/**" # 默认,静态资源会映射 /**,静态资源前缀
        favicon:
            enabled: #是否支持favicon.ico，默认为: true
    web:
        resources:
            static-locations: file:./static # 自定义静态资源文件夹,必须加前缀
            chain:
                strategy:
                    content:
                        enabled: true
                        paths: "/**"
                    fixed:
                        enabled: true
                        paths: "/js/lib/"
                        version: "v12"
```

使用 WebJars,及打包为 jar 包的静态资源,使用`/webjars/**`此路径访问,SpringBoot 默认将/webjars/\*\*映射到 classpath:/META-INF/resources/webjars/

## 拦截器

在 Spring Boot 项目中，使用拦截器功能通常需要以下 3 步：

1. 定义拦截器: 实现接口 `HandlerInterceptor`
2. 注册拦截器:创建一个实现了 `WebMvcConfigurer` 接口的配置类（使用了 `@Configuration` 注解的类），重写 `addInterceptors()` 方法，并在该方法中调用 `registry.addInterceptor()` 方法将自定义的拦截器注册到容器中。
3. 指定拦截规则（如果是拦截所有，静态资源也会被拦截）: 修改配置类中 `addInterceptors()` 方法的代码，继续指定拦截器的拦截规则，

```java
registry.addInterceptor(new LoginInterceptor())
    .addPathPatterns("/**") //拦截所有请求，包括静态资源文件
    .excludePathPatterns("/", "/login", "/index.html", "/user/login", "/css/**", "/images/**", "/js/**", "/fonts/**"); //放行登录页，登陆操作，静态资源
```

## 注册 Web 原生组件（Servlet、Filter、Listener）

由于 Spring Boot 默认以 Jar 包方式部署的，默认没有 web.xml，因此无法再像以前一样通过 web.xml 配置来使用 Servlet 、Filter、Listener，但 Spring Boot 提供了 2 种方式来注册这些 Web 原生组件。

### 通过组件扫描注册

通过组件扫描注册
Servlet 3.0 提供了以下 3 个注解：
`@WebServlet`：用于声明一个 Servlet；
`@WebFilter`：用于声明一个 Filter；
`@WebListener`：用于声明一个 Listener。

这些注解可直接标注在对应组件上，它们与在 web.xml 中的配置意义相同。每个注解都具有与 web.xml 对应的属性，可直接配置，省去了配置 web.xml 的繁琐。

想要在 SpringBoot 中注册这些原生 Web 组件，可以使用 `@ServletComponentScan` 注解实现，该注解可以扫描标记 `@WebServlet、@WebFilter 和 @WebListener` 三个注解的组件类，并将它们注册到容器中。
注意：`@ServletComponentScan` 注解只能标记在启动类或配置类上。

### 使用 RegistrationBean 注册

我们还可以在**配置类**中使用 RegistrationBean 来注册原生 Web 组件，不过这种方式相较于注解方式要繁琐一些。使用这种方式注册的原生 Web 组件，不再需要使用 @WebServlet 、@WebListener 和 @WebListener 等注解。

`RegistrationBean` 是个抽象类，负责将组件注册到 Servlet 容器中，Spring 提供了三个它的实现类，分别用来注册 Servlet、Filter 和 Listener。
`ServletRegistrationBean` ：Servlet 的注册类
`FilterRegistrationBean` ：Filter 的注册类
`ServletListenerRegistrationBean` ：Listener 的注册类

我们可以在配置类中，使用 `@Bean` 注解将 ServletRegistrationBean、FilterRegistrationBean 和 ServletListenerRegistrationBean 添加 Spring 容器中，并通过它们将我们自定义的 Servlet、Filter 和 Listener 组件注册到容器中使用。

# spring-boot-starter-test

包含:

1. JUnit
2. Spring Test & SpringBootTest
3. AssertJ
4. Hamcrest
5. Mockito
6. JSONassert
7. JsonPath

`@SpringBootTest` 放在单元测试类上,可以使测试使用 springboot 上下文注册的 component; JUnit 4 需要追加注解`@RunWith(SpringRunner.class)`,

# 日志配置

## 默认配置

Spring Boot 默认使用 SLF4J+Logback 记录日志，并提供了默认配置，即使我们不进行任何额外配，也可以使用 SLF4J+Logback 进行日志输出。常见的日志配置包括日志级别、日志的输入出格式等内容。

## 默认日志配置

我们可以根据自身的需求，通过全局配置文件（application.properties/yml）修改 Spring Boot 日志级别和显示格式等默认配置。

```yaml
logging:
    level:
        root: info
    config: classpath:logback.xml # 使用 logback 配置文件
    file:
        path: ./logs # 使用相对路径或绝对路径的方式设置日志输出的位置
    pattern:
        console: %d{yyyy-MM-dd hh:mm:ss} [%thread] %-5level %logger{50} - %msg%n # 控制台日志格式
        file: %d{yyyy-MM-dd} === [%thread] === %-5level === %logger{50} === - %msg%n # 日志文件日志格式
spring:
    main:
        log-startup-info: false # 关闭springboot启动日志
```

## 自定义日志配置

在 Spring Boot 的配置文件 application.porperties/yml 中，可以对日志的一些默认配置进行修改，但这种方式只能修改个别的日志配置，想要修改更多的配置或者使用更高级的功能，则需要通过日志实现框架自己的配置文件进行配置。

Spring 官方提供了各个日志实现框架所需的配置文件，用户只要将指定的配置文件放置到项目的类路径下即可。

日志框架 配置文件
`Logback` `logback-spring.xml、logback-spring.groovy、logback.xml、logback.groovy`
`Log4j2` `log4j2-spring.xml、log4j2.xml`
`JUL` (Java Util Logging) `logging.properties`

从上表可以看出，日志框架的配置文件基本上被分为 2 类：

-   普通日志配置文件，即不带 srping 标识的配置文件，例如 logback.xml；
-   带有 spring 表示的日志配置文件，例如 logback-spring.xml。

### 普通日志配置文件

我们将 logback.xml、log4j2.xml 等不带 spring 标识的普通日志配置文件，放在项目的类路径下后，这些配置文件会跳过 Spring Boot，直接被日志框架加载。通过这些配置文件，我们就可以达到自定义日志配置的目的。

将 logback.xml 加入到 Spring Boot 项目的类路径下（resources 目录下），该配置文件配置内容如下。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--
scan：当此属性设置为true时，配置文件如果发生改变，将会被重新加载，默认值为true。
scanPeriod：设置监测配置文件是否有修改的时间间隔，如果没有给出时间单位，默认单位是毫秒当scan为true时，此属性生效。默认的时间间隔为1分钟。
debug：当此属性设置为true时，将打印出logback内部日志信息，实时查看logback运行状态。默认值为false。
-->
<configuration scan="false" scanPeriod="60 seconds" debug="false">
    <!-- 定义日志的根目录 -->
    <property name="LOG_HOME" value="/app/log"/>
    <!-- 定义日志文件名称 -->
    <property name="appName" value="bianchengbang-spring-boot-logging"></property>
    <!-- ch.qos.logback.core.ConsoleAppender 表示控制台输出 -->
    <appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
        <!--
        日志输出格式：
   %d表示日期时间，
   %thread表示线程名，
   %-5level：级别从左显示5个字符宽度
   %logger{50} 表示logger名字最长50个字符，否则按照句点分割。
   %msg：日志消息，
   %n是换行符
        -->
        <layout class="ch.qos.logback.classic.PatternLayout">
            <pattern>%d{yyyy-MM-dd HH:mm:ss} [%thread]**************** %-5level %logger{50} - %msg%n</pattern>
        </layout>
    </appender>
    <!-- 滚动记录文件，先将日志记录到指定文件，当符合某个条件时，将日志记录到其他文件 -->
    <appender name="appLogAppender" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 指定日志文件的名称 -->
        <file>${LOG_HOME}/${appName}.log</file>
        <!--
        当发生滚动时，决定 RollingFileAppender 的行为，涉及文件移动和重命名
        TimeBasedRollingPolicy： 最常用的滚动策略，它根据时间来制定滚动策略，既负责滚动也负责出发滚动。
        -->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--
            滚动时产生的文件的存放位置及文件名称 %d{yyyy-MM-dd}：按天进行日志滚动
            %i：当文件大小超过maxFileSize时，按照i进行文件滚动
            -->
            <fileNamePattern>${LOG_HOME}/${appName}-%d{yyyy-MM-dd}-%i.log</fileNamePattern>
            <!--
            可选节点，控制保留的归档文件的最大数量，超出数量就删除旧文件。假设设置每天滚动，
            且maxHistory是365，则只保存最近365天的文件，删除之前的旧文件。注意，删除旧文件是，
            那些为了归档而创建的目录也会被删除。
            -->
            <MaxHistory>365</MaxHistory>
            <!--
            当日志文件超过maxFileSize指定的大小是，根据上面提到的%i进行日志文件滚动 注意此处配置SizeBasedTriggeringPolicy是无法实现按文件大小进行滚动的，必须配置timeBasedFileNamingAndTriggeringPolicy
            -->
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>100MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
        </rollingPolicy>
        <!-- 日志输出格式： -->
        <layout class="ch.qos.logback.classic.PatternLayout">
            <pattern>%d{yyyy-MM-dd HH:mm:ss} [ %thread ] ------------------ [ %-5level ] [ %logger{50} : %line ] -
                %msg%n
            </pattern>
        </layout>
    </appender>
    <!--
  logger主要用于存放日志对象，也可以定义日志类型、级别
  name：表示匹配的logger类型前缀，也就是包的前半部分
  level：要记录的日志级别，包括 TRACE < DEBUG < INFO < WARN < ERROR
  additivity：作用在于children-logger是否使用 rootLogger配置的appender进行输出，
  false：表示只用当前logger的appender-ref，true：
  表示当前logger的appender-ref和rootLogger的appender-ref都有效
    -->
    <!-- hibernate logger -->
    <logger name="net.biancheng.www" level="debug"/>
    <!-- Spring framework logger -->
    <logger name="org.springframework" level="debug" additivity="false"></logger>
    <!--
    root与logger是父子关系，没有特别定义则默认为root，任何一个类只会和一个logger对应，
    要么是定义的logger，要么是root，判断的关键在于找到这个logger，然后判断这个logger的appender和level。
    -->
    <root level="info">
        <appender-ref ref="stdout"/>
        <appender-ref ref="appLogAppender"/>
    </root>
</configuration>
```

### 带有 spring 标识的日志配置文件

Spring Boot 推荐用户使用 logback-spring.xml、log4j2-spring.xml 等这种带有 spring 标识的配置文件。这种配置文件被放在项目类路径后，不会直接被日志框架加载，而是由 Spring Boot 对它们进行解析，这样就可以使用 Spring Boot 的高级功能 Profile，实现在不同的环境中使用不同的日志配置。

1. 将 logback.xml 文件名修改为 logback-spring.xml，并将配置文件中日志输出格式的配置修改为使用 Profile 功能的配置。

2. 配置内容修改前，日志输出格式配置如下。

```xml
<configuration scan="false" scanPeriod="60 seconds" debug="false">
    ......
    <appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
        <layout class="ch.qos.logback.classic.PatternLayout">
            <pattern>%d{yyyy-MM-dd HH:mm:ss} [%thread]**************** %-5level %logger{50} - %msg%n</pattern>
        </layout>
    </appender>
    ......
</configuration>
```

3. 修改 logback-spring.xml 的配置内容，通过 Profile 功能实现在不同的环境中使用不同的日志输出格式，配置如下。

```xml
<configuration scan="false" scanPeriod="60 seconds" debug="false">
    ......
    <appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
       <layout class="ch.qos.logback.classic.PatternLayout">
            <!--开发环境 日志输出格式-->
            <springProfile name="dev">
                <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} ----> [%thread] ---> %-5level %logger{50} - %msg%n</pattern>
            </springProfile>
            <!--非开发环境 日志输出格式-->
            <springProfile name="!dev">
                <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} ==== [%thread] ==== %-5level %logger{50} - %msg%n</pattern>
            </springProfile>
        </layout>
    </appender>
    ......
</configuration>
```

# JDBC

Spring Boot 默认使用 HikariCP 作为其数据源，对数据库的访问。

## 一般使用

1. 导入 JDBC 场景启动器：spring-boot-starter-data-jdbc，

```xml
<!--导入JDBC的场景启动器-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jdbc</artifactId>
</dependency>
```

2. 导入数据库驱动

JDBC 的场景启动器中并没有导入数据库驱动，我们需要根据自身的需求引入所需的数据库驱动。

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>
```

> Spring Boot 默认为数据库驱动程序做了版本仲裁，所以我们在导入数据库驱动时，可以不再声明版本。需要注意的是，数据库驱动的版本必须与数据库的版本相对应。

3. 配置数据源

```yaml
#数据源连接信息
spring:
    datasource:
        username: root
        password: root
        url: jdbc:mysql://127.0.0.1:3306/bianchengbang_jdbc
        driver-class-name: com.mysql.cj.jdbc.Driver
```

4. 测试
   Spring Boot 提供了一个名为 JdbcTemplate 的轻量级数据访问工具，它是对 JDBC 的封装。Spring Boot 对 JdbcTemplate 提供了默认自动配置，我们可以直接使用 @Autowired 或构造函数将它注入到 bean 中使用。

## 整合 MyBatis

MyBatis 是一个半自动化的 ORM 框架，所谓半自动化是指 MyBatis 只支持将数据库查出的数据映射到 POJO 实体类上，而实体到数据库的映射则需要我们自己编写 SQL 语句实现，相较于 Hibernate 这种完全自动化的框架，Mybatis 更加灵活，我们可以根据自身的需求编写 sql 语句来实现复杂的数据库操作。

随着 Spring Boot 越来越流行，越来越多的被厂商及开发者所认可，MyBatis 也开发了一套基于 Spring Boot 模式的 starter：mybatis-spring-boot-starter。

**引入依赖**
Spring Boot 整合 MyBatis 的第一步，就是在项目的 pom.xml 中引入 mybatis-spring-boot-starter 的依赖，示例代码如下。

```xml
<!--引入 mybatis-spring-boot-starter 的依赖-->
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.2.0</version>
</dependency>
```

**配置 MyBatis**
在 Spring Boot 的配置文件（application.properties/yml）中对 MyBatis 进行配置

```yaml
mybatis:
    # 指定 mapper.xml 的位置
    mapper-locations: classpath:mybatis/mapper/*.xml
    #扫描实体类的位置,在此处指明扫描实体类的包，在 mapper.xml 中就可以不写实体类的全路径名
    type-aliases-package: net.biancheng.www.bean
    configuration:
        #默认开启驼峰命名法，可以不用设置该属性
        map-underscore-to-camel-case: true
```

# 注解

## bean 生命周期 @PostConstruct 和 @PreDestroy

```java
@Component
public class xxx{
    @PostConstruct // 在构造函数之后执行
    void afterConstroct(){}
    @PreDestroy // 在类 destory() 之前执行
    void beforeDestory(){}
}
```

## bean 条件注入

```java
package org.springframework.boot.autoconfigure.condition;

@ConditionalOnProperty(name="key",havingValue="value") // 放在bean上,当配置文件存在 key=value 时,创建bean
```

# 启动初始化

## 实现 org.springframework.beans.factory.InitializingBean 接口并重写 afterPropertiesSet()方法

InitializingBean 接口只包含一个方法 afterPropertiesSet()，凡是继承了 InitializingBean 接口的类，在初始化时都会调用这方法;
使用方法分为三个步骤：1、 被 spring 管理 2、 实现 InitializingBean 接口 3、重写 afterPropertiesSet 方法

```java
@Component
public class InitializationBeanTest implements InitializingBean{

    @Override
    public void afterPropertiesSet() throws Exception {
        //初始化操作
    }
}
```

## 使用 ContextRefreshedEvent 事件(上下文件刷新事件)

`ContextRefreshedEvent` 是 `Spring` 的 `ApplicationContextEvent` 一个实现，此事件会在 `Spring` 容器初始化完成后以及刷新时触发。

```java
@Component // 注意 这个也是必须有的注解 三种都需要 使spring扫描到这个类并交给它管理
public class InitRedisCache implements ApplicationListener<ContextRefreshedEvent> {
    static final Logger logger = LoggerFactory.getLogger(InitRedisCache.class);

    @Autowired
    private SysConfigService sysConfigService;

    @Autowired
    private SysDictService sysDictService;

    @Override
    public void onApplicationEvent(ContextRefreshedEvent contextRefreshedEvent) {
        logger.info("-------加载配置信息 start-------");
        sysConfigService.loadConfigIntoRedis();
        logger.info("-------加载配置信息 end-------");

        logger.info("-------加载字典信息 start-------");
        sysDictService.loadDictIntoRedis();
        logger.info("-------加载字典信息 end-------");
    }
}
```

# webscoket

WebSocket 是基于 TCP 协议的一种网络协议，它实现了浏览器与服务器全双工通信，支持客户端和服务端之间相互发送信息。在有 WebSocket 之前，如果服务端数据发生了改变，客户端想知道的话，只能采用定时轮询的方式去服务端获取，这种方式很大程度上增大了服务器端的压力，有了 WebSocket 之后，如果服务端数据发生改变，可以立即通知客户端，客户端就不用轮询去换取，降低了服务器的压力。目前主流的浏览器都已经支持 WebSocket 协议了。
　　 WebSocket 使用 ws 和 wss 作资源标志符，它们两个类似于 http 和 https，wss 是使用 TSL 的 ws。主要有 4 个事件：
　　 onopen 　　 创建连接时触发
　　 onclose 　　 连接断开时触发
　　 onmessage 接收到信息时触发
　　 onerror 　　 通讯异常时触发

## 依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-websocket</artifactId>
</dependency>
```

## 使用

1. 配置 `ServerEndpointExporter`

要使用 WebSocket，我们需要在启动类上使用`@EnableWebSocket`注解开启对 WebSocket 的支持功能

```java
@Configuration
public class WebSocketConfig {
    /**
     * 注入ServerEndpointExporter，
     * 这个bean会自动注册使用了@ServerEndpoint注解声明的Websocket endpoint
     */
    @Bean
    public ServerEndpointExporter serverEndpointExporter() {
        return new ServerEndpointExporter();
    }
}

```

# 定时任务

## 基于注解 @Scheduled

基于注解`@Scheduled`默认为单线程，开启多个任务时，任务的执行时机会受上一个任务执行时间的影响。

```java
org.springframework.scheduling.annotation.Scheduled

@Configuration      //1.主要用于标记配置类，兼备Component的效果。
@EnableScheduling   // 2.开启定时任务
public class SaticScheduleTask {
    //3.添加定时任务
    @Scheduled(cron = "0/5 * * * * ?")
    //或直接指定时间间隔，例如：5秒
    //@Scheduled(fixedRate=5000)
    private void configureTasks() {
        System.err.println("执行静态定时任务时间: " + LocalDateTime.now());
    }
}

Cron表达式参数分别表示：

秒（0~59） 例如0/5表示每5秒
分（0~59）
时（0~23）
日（0~31）的某天，需计算
月（0~11）
周几（ 可填1-7 或 SUN/MON/TUE/WED/THU/FRI/SAT）

每隔5秒执行一次：*/5 * * * * ?
每隔1分钟执行一次：0 */1 * * * ?
每天23点执行一次：0 0 23 * * ?
每天凌晨1点执行一次：0 0 1 * * ?
每月1号凌晨1点执行一次：0 0 1 1 * ?
每月最后一天23点执行一次：0 0 23 L * ?
每周星期天凌晨1点实行一次：0 0 1 ? * L
在26分、29分、33分执行一次：0 26,29,33 * * * ?
每天的0点、13点、18点、21点都执行一次：0 0 0,13,18,21 * * ?
```

## 动态:基于接口 SchedulingConfigurer

```java
@Configuration      //1.主要用于标记配置类，兼备Component的效果。
@EnableScheduling   // 2.开启定时任务
public class DynamicScheduleTask implements SchedulingConfigurer {

    @Mapper
    public interface CronMapper {
        @Select("select cron from cron limit 1")
        public String getCron();
    }

    @Autowired      //注入mapper
    @SuppressWarnings("all")
    CronMapper cronMapper;

    /**
     * 执行定时任务.
     */
    @Override
    public void configureTasks(ScheduledTaskRegistrar taskRegistrar) {
        taskRegistrar.addTriggerTask(
                //1.添加任务内容(Runnable)
                () -> System.out.println("执行动态定时任务: " + LocalDateTime.now().toLocalTime()),
                //2.设置执行周期(Trigger)
                triggerContext -> {
                    //2.1 从数据库获取执行周期
                    String cron = cronMapper.getCron();
                    //2.2 合法性校验.
                    if (StringUtils.isEmpty(cron)) {
                        // Omitted Code ..
                    }
                    //2.3 返回执行周期(Date)
                    return new CronTrigger(cron).nextExecutionTime(triggerContext);
                }
        );
    }

}
```

## 多线程定时任务

```java
//@Component注解用于对那些比较中立的类进行注释；
//相对与在持久层、业务层和控制层分别采用 @Repository、@Service 和 @Controller 对分层中的类进行注释
@Component
@EnableScheduling   // 1.开启定时任务
@EnableAsync        // 2.开启多线程
public class MultithreadScheduleTask {

        @Async
        @Scheduled(fixedDelay = 1000)  //间隔1秒
        public void first() throws InterruptedException {
            System.out.println("第一个定时任务开始 : " + LocalDateTime.now().toLocalTime() + "\r\n线程 : " + Thread.currentThread().getName());
            System.out.println();
            Thread.sleep(1000 * 10);
        }

        @Async
        @Scheduled(fixedDelay = 2000)
        public void second() {
            System.out.println("第二个定时任务开始 : " + LocalDateTime.now().toLocalTime() + "\r\n线程 : " + Thread.currentThread().getName());
            System.out.println();
        }
    }
```

# IO

## 缓存

1. `@Cacheable` 缓存后,立即返回缓存内容而不调用方法;没有配置缓存程序时,spring 会配置一个默认的,但不推荐在生产上使用
2. 不提供真实的缓存,有`org.springframework.cache.Cache`和`org.springframework.cache.CacheManager`接口的实现类提供
    1. Generic 如果上下文创建了不止一个 cache,则会使用通用缓存,并且创建一个 CacheManager
    2. JCache (JSR-107) (EhCache 3, Hazelcast, Infinispan, and others)
    3. EhCache 2.x
    4. Hazelcast
    5. Infinispan
    6. Couchbase
    7. Redis
    8. Caffeine
    9. Cache2k
    10. Simple
3. [文档](https://docs.spring.io/spring-boot/docs/2.7.6/reference/html/io.html#io.caching.provider)

```java
import org.springframework.cache.annotation.Cacheable;
import org.springframework.stereotype.Component;

@Component
public class MyMathService {

    @Cacheable("piDecimals")
    public int computePiDecimal(int precision) {
        ...
    }

}
```
