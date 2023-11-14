# MyBatis-plus

[MyBatis-Plus](https://www.mybatis-plus.com/)

## install

### 添加依赖
``` xml
    <!-- spring-boot 环境 -->
    <dependency>
        <groupId>com.baomidou</groupId>
        <artifactId>mybatis-plus-boot-starter</artifactId>
        <version>Latest Version</version>
    </dependency>
    <!-- spring maven 环境 -->
    <dependency>
        <groupId>com.baomidou</groupId>
        <artifactId>mybatis-plus</artifactId>
        <version>mybatis-plus-latest-version</version>
    </dependency>
    <!--数据库驱动-->
    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
        <scope>runtime</scope>
    </dependency>
``` 
### 配置
spring-boot 项目配置步骤：
1. application.yml
2. 配置 MapperScan 注解
   

``` yaml
# DataSource Config
spring:
  datasource:
    driver-class-name: org.h2.Driver
    schema: classpath:db/schema-h2.sql
    data: classpath:db/data-h2.sql
    url: jdbc:h2:mem:test
    username: root
    password: test
```


``` java
//启动类增加MapperScan注解，扫描mapper
@SpringBootApplication
@MapperScan("com.baomidou.mybatisplus.samples.quickstart.mapper")
psvm(){

}
//com.baomidou.mybatisplus.samples.quickstart.mapper 继承BaseMapper接口
public interface UserMapper extends BaseMapper<User> {

}

//使用
@Autowired
private UserMapper userMapper;
//userMapper即可调用数据库
```

## 注解
``` java
@TableName  //表名注解，类
@TableId //主键注解，属性
@TableField //字段注解(非主键)
@Version //乐观锁注解、标记 @Verison 在字段上
@EnumValue //通枚举类注解(注解在枚举字段上)
@TableLogic //表字段逻辑处理注解（逻辑删除）
@KeySequence //序列主键策略 oracle
@InterceptorIgnore //
@OrderBy //内置 SQL 默认指定排序，优先级低于 wrapper 条件查询
```

## 代码生成器--历史

AutoGenerator 是 MyBatis-Plus 的代码生成器，通过 AutoGenerator 可以快速生成 Entity、Mapper、Mapper XML、Service、Controller 等各个模块的代码，极大的提升了开发效率。

### 添加依赖
MyBatis-Plus 从 3.0.3 之后移除了代码生成器与模板引擎的默认依赖，需要手动添加相关依赖：
``` xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-generator</artifactId>
    <version>3.4.1</version>
</dependency>
<!--添加 模板引擎 依赖，MyBatis-Plus 支持 Velocity（默认）、Freemarker、Beetl，用户可以选择自己熟悉的模板引擎，如果都不满足您的要求，可以采用自定义模板引擎。-->
Velocity（默认）：
<dependency>
    <groupId>org.apache.velocity</groupId>
    <artifactId>velocity-engine-core</artifactId>
    <version>2.3</version>
</dependency>

Freemarker：
<dependency>
    <groupId>org.freemarker</groupId>
    <artifactId>freemarker</artifactId>
    <version>2.3.31</version>
</dependency>

Beetl：
<dependency>
    <groupId>com.ibeetl</groupId>
    <artifactId>beetl</artifactId>
    <version>3.10.0.RELEASE</version>
</dependency>
```
```java
//注意！如果您选择了非默认引擎，需要在 AutoGenerator 中 设置模板引擎。

AutoGenerator generator = new AutoGenerator();

// set freemarker engine
generator.setTemplateEngine(new FreemarkerTemplateEngine());

// set beetl engine
generator.setTemplateEngine(new BeetlTemplateEngine());

// set custom engine (reference class is your custom engine class)
generator.setTemplateEngine(new CustomTemplateEngine());
```
### 编写配置

MyBatis-Plus 的代码生成器提供了大量的自定义参数供用户选择，能够满足绝大部分人的使用需求。
``` java
//配置 GlobalConfig
GlobalConfig globalConfig = new GlobalConfig();
globalConfig.setOutputDir(System.getProperty("user.dir") + "/src/main/java");
globalConfig.setAuthor("jobob");
globalConfig.setOpen(false);
//配置 DataSourceConfig
DataSourceConfig dataSourceConfig = new DataSourceConfig();
dataSourceConfig.setUrl("jdbc:mysql://localhost:3306/ant?useUnicode=true&useSSL=false&characterEncoding=utf8");
dataSourceConfig.setDriverName("com.mysql.jdbc.Driver");
dataSourceConfig.setUsername("root");
dataSourceConfig.setPassword("password");
```

## 代码生成器 3.5.1+

https://www.mybatis-plus.com/guide/generator-new.html#%E7%AD%96%E7%95%A5%E9%85%8D%E7%BD%AE-strategyconfig

## CRUD 接口

dao 层 继承 BaseMapper<T>接口
service 层 继承 IService<T>接口

使用 @Autowired 注解即可

## 条件构造器

com.baomidou.mybatisplus.mapper.Wrapper
com.baomidou.mybatisplus.mapper.Condition

## 数据安全保护
该功能为了保护数据库配置及数据安全，在一定的程度上控制开发人员流动导致敏感信息泄露。

3.3.2 开始支持

配置安全

YML 配置：
``` yaml
// 加密配置 mpw: 开头紧接加密内容（ 非数据库配置专用 YML 中其它配置也是可以使用的 ）
spring:
  datasource:
    url: mpw:qRhvCwF4GOqjessEB3G+a5okP+uXXr96wcucn2Pev6Bf1oEMZ1gVpPPhdDmjQqoM
    password: mpw:Hzy5iliJbwDHhjLs1L0j6w==
    username: mpw:Xb+EgsyuYRXw7U7sBJjBpA==
```

密钥加密：
``` java
// 生成 16 位随机 AES 密钥
String randomKey = AES.generateRandomKey();

// 随机密钥加密
String result = AES.encrypt(data, randomKey);
如何使用：
```
``` s
// Jar 启动参数（ idea 设置 Program arguments , 服务器可以设置为启动环境变量 ）
--mpw.key=d1104d7c3b616f0b
```

## 多数据源