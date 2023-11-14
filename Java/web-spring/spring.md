# hello world

## pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.itranswarp.learnjava</groupId>
    <artifactId>spring-ioc-appcontext</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
        <java.version>11</java.version>
        <spring.version>5.2.3.RELEASE</spring.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${spring.version}</version>
        </dependency>
    </dependencies>
</project>
```

## 配置文件

resources/Bean.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
            http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
            http://www.springframework.org/schema/context
            http://www.springframework.org/schema/context/spring-context-4.2.xsd
            http://www.springframework.org/schema/aop
            http://www.springframework.org/schema/aop/spring-aop-4.3.xsd">


    <bean id="helloWorld" class="org.example.demo1.HelloWorld">
        <property name="message" value="Hello World!" />
    </bean>
</beans>
```

## App.java

```java
public class HelloWorld {
    private String message;
    public void setMessage(String message) {
        this.message = message;
    }
    public void getMessage() {
        System.out.println("message : " + message);
    }
}
public class App {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml");
        HelloWorld obj = context.getBean("helloWorld", HelloWorld.class);
        obj.getMessage();
    }
}

```

# 前言介绍

## 概念

- 性能好 IOC 控制反转
- 易于测试 Junit
- 可重用,AOP 面向切面编程
- 轻量级框架,基础版本只有 2MB 左右
- 适用于任何开发 java 的程序,但是 java web 需要安装扩展
- 目标: 使 J2EE 更容易使用,基于 POJO 模型

## spring 家族

`Spring Framework`
spring 的基础,为依赖关系注入、事务管理、Web 应用、数据访问、消息传递等提供核心支持。

`Spring Boot`
以快速的方式构建 Spring 应用程序，并让您尽快启动并运行。

`Spring Data`
提供一致的数据访问方法 - 关系型、非关系型、map-reduce 等。

`Spring Cloud`
为分布式系统中的常见模式提供一组工具。适用于构建和部署微服务。

`Spring Cloud Data Flow`
为现代运行时上的可组合数据微服务应用程序提供编排服务。

`Spring Security`
通过全面且可扩展的身份验证和授权支持保护您的应用程序。

`Spring for GraphQL`
Spring for GraphQL 为基于 GraphQL Java 构建的 Spring 应用程序提供支持。

`Spring Session`
提供用于管理用户会话信息的 API 和实现。

`Spring Integration`
通过轻量级消息传递和声明性适配器支持众所周知的企业集成模式。

`Spring HATEOAS`
简化创建遵循 HATEOAS 原则的 REST 表示。

`Spring REST Docs`
通过将手写文档与使用 Spring MVC 测试或 REST Assured 生成的自动生成的代码段相结合，您可以记录 RESTful 服务。

`Spring Batch`
简化并优化处理大批量批量操作的工作。

`Spring AMQP`
将 Spring 的核心概念应用于基于 AMQP 的消息传递解决方案的开发。

`Spring CredHub`
为存储、检索和删除在 Cloud Foundry 平台中运行的 CredHub 服务器中的凭据提供客户端支持。

`Spring Flo`
提供一个 JavaScript 库，该库为管道和简单图形提供基本的可嵌入 HTML5 可视化生成器。

`Spring for Apache Kafka`
为 Apache Kafka 提供了熟悉的 Spring Abstractions。

`Spring LDAP`
通过使用 Spring 熟悉的基于模板的方法，简化使用 LDAP 的应用程序的开发。

`Spring Shell`
通过基于 CLI 的资源发现和交互，使编写和测试 RESTful 应用程序变得更加容易。

`Spring Statemachine`
为应用程序开发人员提供一个框架，将状态机概念与 Spring 应用程序一起使用。

`Spring Vault`
为 HashiCorp Vault 提供熟悉的 spring 抽象

`Spring Web Flow`
支持构建具有受控导航功能的 Web 应用程序，例如办理登机手续或申请贷款。

`Spring Web Services`
促进合同优先 SOAP Web 服务的开发。

## Spring Framework

特性:

- 非侵入式: 对应用程序本身的结构影响非常小,对领域结构可以做到零污染;对于功能性组件只需要几个简单注解进行标记,完全不会破坏原有机构,反而能简化.
- 控制反转: IOC 翻转资源获取方向,把自己创建资源\向环境索取资源变为: 环境将资源准备好,我们享受资源注入
- AOP Aspects 面向切面编程: 在不修改源代码的基础上增强代码功能
- 容器: IOC 是一个容器,因为它包含并且管理组件对象的生命周期,组件享受到了容器化管理,替程序员屏蔽了组件创建工程中的大量细节,极大的降低了使用门槛,大幅提高了开发效率.
- 组件化: spring 实现了使用简单的组件配置组合成复杂的应用,在 spring 中可以使用 xml java 注解组合这些对象,使我们可以基于一个个功能明确\边界清晰的组件有条不紊的搭建超大型复杂应用系统.
- 声明式: 很多以前需要编写代码才能实现的功能,现在只需要声明需求即可由框架代为实现
- 一站式: spring 在 IOC 与 AOP 基础上可以整合各种第三方框架和类库,而且,spring 旗下的项目已经覆盖了广泛领域,很多方面的功能需求可以在 spring framework 的基础上全部使用 spring 来实现

Spring 框架基本涵盖了企业级应用开发的各个方面，它包含了 20 多个不同的模块。
spring-aop spring-context-indexer spring-instrument spring-orm spring-web
spring-aspects spring-context-support spring-jcl spring-oxm spring-webflux
spring-beans spring-core spring-jdbc spring-r2dbc spring-webmvc
spring-context spring-expression spring-jms spring-test spring-websocket
spring-messaging spring-tx

![Spring架构图](../photo/163550G63-0.png)

1. **Data Access/Integration（数据访问／集成）**
   数据访问／集成层包括 JDBC、ORM、OXM、JMS 和 Transactions 模块，具体介绍如下。
   JDBC 模块：提供了一个 JBDC 的样例模板，使用这些模板能消除传统冗长的 JDBC 编码还有必须的事务控制，而且能享受到 Spring 管理事务的好处。
   ORM 模块：提供与流行的“对象-关系”映射框架无缝集成的 API，包括 JPA、JDO、Hibernate 和 MyBatis 等。而且还可以使用 Spring 事务管理，无需额外控制事务。
   OXM 模块：提供了一个支持 Object /XML 映射的抽象层实现，如 JAXB、Castor、XMLBeans、JiBX 和 XStream。将 Java 对象映射成 XML 数据，或者将 XML 数据映射成 Java 对象。
   JMS 模块：指 Java 消息服务，提供一套 “消息生产者、消息消费者”模板用于更加简单的使用 JMS，JMS 用于用于在两个应用程序之间，或分布式系统中发送消息，进行异步通信。
   Transactions 事务模块：支持编程和声明式事务管理。

2. **Web 模块**
   Spring 的 Web 层包括 Web、Servlet、WebSocket 和 Portlet 组件，具体介绍如下。
   Web 模块：提供了基本的 Web 开发集成特性，例如多文件上传功能、使用的 Servlet 监听器的 IOC 容器初始化以及 Web 应用上下文。
   Servlet 模块：提供了一个 Spring MVC Web 框架实现。Spring MVC 框架提供了基于注解的请求资源注入、更简单的数据绑定、数据验证等及一套非常易用的 JSP 标签，完全无缝与 Spring 其他技术协作。
   WebSocket 模块：提供了简单的接口，用户只要实现响应的接口就可以快速的搭建 WebSocket Server，从而实现双向通讯。
   Portlet 模块：提供了在 Portlet 环境中使用 MVC 实现，类似 Web-Servlet 模块的功能。

3. **Core Container（Spring 的核心容器）**
   Spring 的核心容器是其他模块建立的基础，由 Beans 模块、Core 核心模块、Context 上下文模块和 SpEL 表达式语言模块组成，没有这些核心容器，也不可能有 AOP、Web 等上层的功能。具体介绍如下。
   Beans 模块：提供了框架的基础部分，包括控制反转和依赖注入。
   Core 核心模块：封装了 Spring 框架的底层部分，包括资源访问、类型转换及一些常用工具类。
   Context 上下文模块：建立在 Core 和 Beans 模块的基础之上，集成 Beans 模块功能并添加资源绑定、数据验证、国际化、Java EE 支持、容器生命周期、事件传播等。ApplicationContext 接口是上下文模块的焦点。
   SpEL 模块：提供了强大的表达式语言支持，支持访问和修改属性值，方法调用，支持访问及修改数组、容器和索引器，命名变量，支持算数和逻辑运算，支持从 Spring 容器获取 Bean，它也支持列表投影、选择和一般的列表聚合等。

4. **AOP、Aspects、Instrumentation 和 Messaging**
   在 Core Container 之上是 AOP、Aspects 等模块，具体介绍如下：
   AOP 模块：提供了面向切面编程实现，提供比如日志记录、权限控制、性能统计等通用功能和业务逻辑分离的技术，并且能动态的把这些功能添加到需要的代码中，这样各司其职，降低业务逻辑和通用功能的耦合。
   Aspects 模块：提供与 AspectJ 的集成，是一个功能强大且成熟的面向切面编程（AOP）框架。
   Instrumentation 模块：提供了类工具的支持和类加载器的实现，可以在特定的应用服务器中使用。
   messaging 模块：Spring 4.0 以后新增了消息（Spring-messaging）模块，该模块提供了对消息传递体系结构和协议的支持。

5. **Test 模块**
   Test 模块：Spring 支持 Junit 和 TestNG 测试框架，而且还额外提供了一些基于 Spring 的测试功能，比如在测试 Web 框架时，模拟 Http 请求的功能。

# 控制反转 IoC,管理 bean

## IoC

IoC: 控制反转核心思想就是**由 Spring 负责对象的创建**。

IoC 底层通过工厂模式、Java 的反射机制、XML 解析等技术，将代码的耦合度降低到最低限度，其主要步骤如下。

1. 在配置文件（例如 Bean.xml）中，对各个对象以及它们之间的依赖关系进行配置；
2. 我们可以把 IoC 容器当做一个工厂，这个工厂的产品就是 Spring Bean；
3. 容器启动时会加载并解析这些配置文件，得到对象的基本信息以及它们之间的依赖关系；
4. IoC 利用 Java 的反射机制，根据类名生成相应的对象（即 Spring Bean），并根据依赖关系将这个对象注入到依赖它的对象中。

由于对象的基本信息、对象之间的依赖关系都是在配置文件中定义的，并没有在代码中紧密耦合，因此即使对象发生改变，我们也只需要在配置文件中进行修改即可，而无须对 Java 代码进行修改，这就是 Spring IoC 实现解耦的原理。

IOC 依赖:

```xml
<dependencies>
    <!-- 基于Maven依赖传递性，导入spring-context依赖即可导入当前所需所有jar包 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.3.1</version>
    </dependency>
    <!-- junit测试 -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>
</dependencies>

```

### 依赖注入 DI

在对象创建过程中，Spring 会自动根据依赖关系，将它依赖的对象注入到当前对象中，这就是所谓的“依赖注入”。
依赖注入本质上是 Spring Bean 属性注入的一种，只不过这个属性是一个对象属性而已。

DI ,是 IOC 的实现

### IOC 容器实现

IoC 思想基于 IoC 容器实现的，**IoC 容器底层其实就是一个 Bean 工厂**。
Spring 框架为我们提供了两种不同类型 IoC 容器，它们分别是 `BeanFactory` 和 `ApplicationContext` .

`BeanFactory`: IOC 的基本实现,面向 SPEING 本身,不提供给开发者
`ApplicationContext`: `BeanFactory` 的子接口,面向 spring 的使用者
`ClassPathXmlApplicationContext`: 通过读取类路径下的 xml 格式的配置文件创建 IOC 容器对象 (更常用)
`FileSystemXmlApplicationContext`: 通过读取文件系统路径下的 xml 格式的配置文件创建 IOC 容器对象
`ConfigurableApplicationContext`: `ApplicationContext` 的子接口,包含了一些扩展方法,让 ApplicationContext 拒用启动 关闭 和刷新上下文的能力
`WebApplicationContext`: 专门为 web 应用准备,基于 web 环境创建 IOC 容器对象,并将对象引入存入 `ServletContext`域中.

## Bean

**由 Spring IoC 容器管理的对象称为 Bean，Bean 根据 Spring 配置文件中的信息创建。**

Spring 配置文件支持两种格式，即 XML 文件格式和 Properties 文件格式。

- Properties 配置文件主要以 key-value 键值对的形式存在，只能赋值，不能进行其他操作，适用于简单的属性配置。
- XML 配置文件采用树形结构，结构清晰，相较于 Properties 文件更加灵活。但是 XML 配置比较繁琐，适用于大型的复杂的项目。

### 生命周期

1. bean 对象创建（调用无参构造器）
2. 给 bean 对象设置属性
3. bean 对象初始化之前操作（由 bean 的后置处理器负责）
4. bean 对象初始化（需在配置 bean 时指定初始化方法） `init-method`
5. bean 对象初始化之后操作（由 bean 的后置处理器负责）
6. bean 对象就绪可以使用
7. bean 对象销毁（需在配置 bean 时指定销毁方法）`destroy-method`
8. IOC 容器关闭

### bean 后置处理器

bean 的后置处理器会在生命周期的初始化前后添加额外的操作，需要实现`BeanPostProcessor`接口，且配置到 IOC 容器中，需要注意的是，bean 后置处理器不是单独针对某一个 bean 生效，而是针对 IOC 容器中所有 bean 都会执行

```java
package com.atguigu.spring.process;
import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanPostProcessor;
public class MyBeanProcessor implements BeanPostProcessor {
    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName)throws BeansException {
        System.out.println("☆☆☆" + beanName + " = " + bean);
        return bean;
    }
    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName)throws BeansException {
        System.out.println("★★★" + beanName + " = " + bean);
        return bean;
    }
}

```

```xml
<!-- bean的后置处理器要放入IOC容器才能生效 -->
<bean id="myBeanProcessor" class="com.atguigu.spring.process.MyBeanProcessor"/>
```

### 使用注解插足生命周期

我们还可以通过 JSR-250 的 `@PostConstruct` 和 `@PreDestroy` 注解，指定 Bean 的生命周期回调方法。

| 注解             | 描述                                                                                      |
| ---------------- | ----------------------------------------------------------------------------------------- |
| `@PostConstruct` | 指定初始化回调方法，这个方法会在 Spring Bean 被初始化后被调用，执行一些自定义的回调操作。 |
| `@PreDestroy`    | 指定销毁回调方法，这个方法会在 Spring Bean 被销毁前被调用，执行一些自定义的回调操作。     |

```xml
<bean id="annotationLifeCycleBean" class="net.biancheng.c.AnnotationLifeCycleBean">
<!--net.biancheng.c.AnnotationLifeCycleBean 内部方法上使用注解自定义生命周期方法-->
</bean>
```

### Bean 继承

在 Spring 中，Bean 和 Bean 之间也存在继承关系。我们将被继承的 Bean 称为父 Bean，将继承父 Bean 配置信息的 Bean 称为子 Bean。

Spring Bean 的定义中可以包含很多配置信息，例如构造方法参数、属性值。子 Bean 既可以继承父 Bean 的配置数据，也可以根据需要重写或添加属于自己的配置信息。

在 Spring XML 配置中，我们通过子 Bean 的 parent 属性来指定需要继承的父 Bean，配置格式如下。

```xml
<!--父Bean-->
<bean id="parentBean" class="xxx.xxxx.xxx.ParentBean" >
    <property name="xxx" value="xxx"></property>
    <property name="xxx" value="xxx"></property>
</bean>
<!--子Bean-->
<bean id="childBean" class="xxx.xxx.xxx.ChildBean" parent="parentBean"></bean>

```

### 定义模板

在父 Bean 的定义中，有一个十分重要的属性，那就是 `abstract` 属性。如果一个父 Bean 的 abstract 属性值为 true，则表明这个 Bean 是抽象的。
**抽象的父 Bean 只能作为模板被子 Bean 继承，它不能实例化，也不能被其他 Bean 引用，更不能在代码中根据 id 调用 getBean() 方法获取，否则就会返回错误。**
在父 Bean 的定义中，既可以指定 class 属性，也可以不指定 class 属性。**如果父 Bean 定义没有明确地指定 class 属性，那么这个父 Bean 的 abstract 属性就必须为 true**

## 基于 xml 实现 IOC

### xml 配置文件

```xml
<beans>
    <!--list集合类型的bean-->
    <util:list id="students">
        <ref bean="studentOne"></ref>
        <ref bean="studentTwo"></ref>
        <ref bean="studentThree"></ref>
    </util:list>
    <!--map集合类型的bean-->
    <util:map id="teacherMap">
        <entry>
            <key>
                <value>10010</value>
            </key>
            <ref bean="teacherOne"></ref>
        </entry>
        <entry>
            <key>
                <value>10086</value>
            </key>
            <ref bean="teacherTwo"></ref>
        </entry>
    </util:map>

    <!-- 引入外部属性文件 jdbc.properties -->
    <context:property-placeholder location="classpath:jdbc.properties"/>
    <bean id="druidDataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="url" value="${jdbc.url}"/>
        <property name="driverClassName" value="${jdbc.driver}"/>
        <property name="username" value="${jdbc.user}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>
    <!--
        `scope`
            singleton （单例）默认
            prototype   (原型) 每次使用都重新创建
            在WebApplicationContext环境下
                request 在一个请求范围内有效
                session 在一个会话范围内有效
            global Session
    -->
    <bean
        id=""  唯一标识符
        name="" Bean 的名称,多个名称,,隔开
        class=""  类的全限定名
        lazy-init="true" 懒加载,scope=singleton 时有效
        scope="singleton" 作用范围
        init-method="initMethod" 生命周期初始化,在属性注入后执行(类成员方法)
        destroy-method="destroyMethod" 生命周期对象销毁>

        <constructor-arg 缺省,使用无参构造
            index="0"  参数的序号,从 0 开始
            type=""  参数的类型
            name="" 参数名
            value="" 参数赋值>
        </constructor-arg>
        <!--
            property: 用于调用 Bean 实例中的 setter 方法对属性进行赋值，从而完成属性的注入。
                name="": 属性用于指定 Bean 实例中相应的属性名
                value="": 初始化赋值,字面量
                ref="id" 使用已声明bean

            基本类型: name-value
            复杂类型: name
                1. ref="id" 引用外部bean id
                2. 内部 bean
                3. 级联: ref 并 .
            数组: []

        -->
        <property name="" value=""></property>
        <!-- null 空值 -->
        <property name="" value="null"></property>
        <property name="" >
            <null/>
        </property>
        <!-- xml 特殊字符 <> -->
        <property name="expression" value="a &lt; b"/>
        <property name="expression">
        <!-- 解决方案二：使用CDATA节 -->
        <!-- CDATA中的C代表Character，是文本、字符的含义，CDATA就表示纯文本数据 -->
        <!-- XML解析器看到CDATA节就知道这里是纯文本，就不会当作XML标签或属性来解析 -->
        <!-- 所以CDATA节中写什么符号都随意 -->
        <value><![CDATA[a < b]]></value>
        </property>

        <!-- ref属性：引用IOC容器中某个bean的id，将所对应的bean为属性赋值 -->
        <property name="clazz" ref="clazzOne"></property>
        <property name="clazz">
            <!-- 在一个bean中再声明一个bean就是内部bean -->
            <!-- 内部bean只能用于给属性赋值，不能在外部通过IOC容器获取，因此可以省略id属性 -->
            <bean id="clazzInner" class="com.atguigu.spring.bean.Clazz">
                <property name="clazzId" value="2222"></property>
                <property name="clazzName" value="远大前程班"></property>
            </bean>
        </property>
        <!-- 一定先引用某个bean为属性赋值，才可以使用级联方式更新属性 -->
        <property name="clazz" ref="clazzOne"></property>
        <property name="clazz.clazzId" value="3333"></property>
        <property name="clazz.clazzName" value="最强王者班"></property>
        <!-- 为数组赋值 []string -->
        <property name="hobbies">
            <array>
                <value>抽烟</value>
                <value>喝酒</value>
                <value>烫头</value>
            </array>
        </property>
        <!-- 为List赋值 list<Student> -->
        <property name="students">
            <list>
                <ref bean="studentOne"></ref>
                <ref bean="studentTwo"></ref>
                <ref bean="studentThree"></ref>
            </list>
        </property>
        <!-- 外部引用 -->
        <property name="students" ref="students"></property>
        <!-- 为Map<String,Teacher> 赋值-->
        <property name="teacherMap">
            <map>
                <entry>
                    <key>
                        <value>10010</value>
                    </key>
                    <ref bean="teacherOne"></ref>
                </entry>
                <entry>
                    <key>
                        <value>10086</value>
                    </key>
                    <ref bean="teacherTwo"></ref>
                </entry>
            </map>
        </property>
        <!-- 外部引用 -->
        <property name="teacherMap" ref="teacherMap"></property>
    </bean>
</beans>
```

### 获取 bean 对象

```java
    // 加载xml配置文件,获取上下文
    ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
    // 通过 id 获取 bean 对象
    HelloWorld helloworld = (HelloWorld) ac.getBean("helloworld");
    // 通过类名获取
    // 当根据类型获取bean时，要求IOC容器中指定类型的bean有且只能有一个 NoUniqueBeanDefinitionException
    HelloWorld bean = ac.getBean(HelloWorld.class);
    // 通过 id 和 类型
    HelloWorld bean = ac.getBean("helloworld", HelloWorld.class);

```

### p 命名空间注入

p 命名空间 是对`<bean>` 元素中嵌套的 `<property>` 元素 setter 方式属性注入的一种快捷实现方式
首先我们需要在配置文件的 `<beans>` 元素中导入以下 XML 约束。`xmlns:p="http://www.springframework.org/schema/p"`
在导入 XML 约束后，我们就能通过以下形式实现属性注入。`<bean id="Bean 唯一标志符" class="包名+类名" p:普通属性="普通属性值" p:对象属性-ref="对象的引用">`
使用 p 命名空间注入依赖时，必须注意以下 3 点：
Java 类中必须有 setter 方法；
Java 类中必须有无参构造器（类中不包含任何带参构造函数的情况，无参构造函数默认存在）；
在使用 p 命名空间实现属性注入前，XML 配置的 `<beans>` 元素内必须先导入 p 命名空间的 XML 约束。

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-3.0.xsd"
       xmlns:p="http://www.springframework.org/schema/p">
    <bean
        id="studentSix"
        class="com.atguigu.spring.bean.Student"
        p:id="1006"
        p:name="小明"
        p:clazz-ref="clazzOne"
        p:teacherMap-ref="teacherMap">
    </bean>
</beans>
```

### c 命名空间注入

c 命名空间 `<bean>` 元素中嵌套的 `<constructor>` 元素 是构造函数属性注入的一种快捷实现方式
首先我们需要在配置文件的 `<beans>` 元素中导入以下 XML 约束。`xmlns:c="http://www.springframework.org/schema/c"`
在导入 XML 约束后，我们就能通过以下形式实现属性注入。`<bean id="Bean 唯一标志符" class="包名+类名" c:普通属性="普通属性值" c:对象属性-ref="对象的引用">`
使用 c 命名空间注入依赖时，必须注意以下 2 点：
Java 类中必须包含对应的带参构造器；
在使用 c 命名空间实现属性注入前，XML 配置的 `<beans>` 元素内必须先导入 c 命名空间的 XML 约束。

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-3.0.xsd"
       xmlns:c="http://www.springframework.org/schema/c">
    <bean
        id="studentSix"
        class="com.atguigu.spring.bean.Student"
        c:id="1006"
        c:name="小明"
        c:clazz-ref="clazzOne"
        c:teacherMap-ref="teacherMap">
    </bean>
</beans>
```

### FactoryBean

FactoryBean 是 Spring 提供的一种整合第三方框架的常用机制。和普通的 bean 不同，配置一个 FactoryBean 类型的 bean，在获取 bean 的时候得到的并不是 class 属性中配置的这个类的对象，而是 getObject()方法的返回值。通过这种机制，Spring 可以帮我们把复杂组件创建的详细过程和繁琐细节都屏蔽起来，只把最简洁的使用界面展示给我们。
将来我们整合 Mybatis 时，Spring 就是通过 FactoryBean 机制来帮我们创建 SqlSessionFactory 对象的。

```Java
import org.springframework.beans.factory;

public class UserFactoryBean implements FactoryBean<User> {
    @Override
    public User getObject() throws Exception {
        return new User();
    }
    @Override
    public Class<?> getObjectType() {
        return User.class;
    }
}

```

```xml
<bean id="user" class="com.atguigu.bean.UserFactoryBean"></bean>
```

```java
@Test
public void testUserFactoryBean(){
    //获取IOC容器
    ApplicationContext ac = new ClassPathXmlApplicationContext("springfactorybean.xml");
    User user = (User) ac.getBean("user");
    System.out.println(user);
}

```

### 基于 xml 的自动装配

自动装配：根据指定的策略，在 IOC 容器中匹配某一个 bean，自动为指定的 bean 中所依赖的类类型或接口类型属性赋值

```java
public class UserController {
    private UserService userService;
    public void setUserService(UserService userService) {
        this.userService = userService;
    }
    public void saveUser(){
        userService.saveUser();
    }
}
public interface UserService {
    void saveUser();
}
public class UserServiceImpl implements UserService {
    private UserDao userDao;
    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }
    @Override
    public void saveUser() {
        userDao.saveUser();
    }
}
public interface UserDao {
    void saveUser();
}
public class UserDaoImpl implements UserDao {
    @Override
    public void saveUser() {
        System.out.println("保存成功");
    }
}
```

使用 bean 标签的`autowire`属性设置自动装配效果

`byType` ：根据类型匹配 IOC 容器中的某个兼容类型的 bean，为属性自动赋值,若在 IOC 中，没有任何一个兼容类型的 bean 能够为属性赋值，则该属性不装配，即值为默认值 null,若在 IOC 中，有多个兼容类型的 bean 能够为属性赋值，则抛出异常`NoUniqueBeanDefinitionException`
`byName` ：将自动装配的属性的属性名，作为 bean 的 id 在 IOC 容器中匹配相对应的 bean 进行赋值

```xml
<!-- byType -->
<bean id="userController" class="com.atguigu.autowire.xml.controller.UserController" autowire="byType"></bean>
<bean id="userService" class="com.atguigu.autowire.xml.service.impl.UserServiceImpl" autowire="byType"></bean>
<bean id="userDao" class="com.atguigu.autowire.xml.dao.impl.UserDaoImpl"></bean>
<!-- byName -->
<bean id="userController" class="com.atguigu.autowire.xml.controller.UserController" autowire="byName"></bean>
<bean id="userService" class="com.atguigu.autowire.xml.service.impl.UserServiceImpl" autowire="byName"></bean>
<bean id="userServiceImpl" class="com.atguigu.autowire.xml.service.impl.UserServiceImpl" autowire="byName"></bean>
<bean id="userDao" class="com.atguigu.autowire.xml.dao.impl.UserDaoImpl"></bean>
<bean id="userDaoImpl" class="com.atguigu.autowire.xml.dao.impl.UserDaoImpl"></bean>
```

```java
@Test
public void testAutoWireByXML(){
    ApplicationContext ac = new ClassPathXmlApplicationContext("autowirexml.xml");
    UserController userController = ac.getBean(UserController.class);
    userController.saveUser();
}

```

## 基于注解实现 IOC

### 依赖

```xml
<dependencies>
    <!-- 基于Maven依赖传递性，导入spring-context依赖即可导入当前所需所有jar包 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.3.1</version>
    </dependency>
    <!-- junit测试 -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

### 注解

```java
// 放在实现类上面
@Component ：将类标识为普通组件,相当于定义了一个bean,默认名为小驼峰
@Controller ：将类标识为控制层组件
@Service ：将类标识为业务层组件
@Repository ：将类标识为持久层组件dao

通过查看源码我们得知，@Controller、@Service、@Repository 这三个注解只是在 @Component 注解 的基础上起了三个新的名字。对于Spring使用IOC容器管理这些组件来说没有区别。
//注意：虽然它们本质上一样，但是为了代码的可读性，为了程序结构严谨我们肯定不能随便胡乱标记。

默认情况:类名首字母小写就是bean的id。例如：UserController类对应的bean的id就是userController。
自定义bean的id:可通过标识组件的注解的value属性设置自定义的bean的id
@Service("userService")//默认为userServiceImpl
public class UserServiceImpl implements UserService {}
```

### 扫描组件

配置文件: `applicationContext.xml`

```xml
<!-- 情况一：最基本的扫描方式 -->
<context:component-scan base-package="com.atguigu"></context:component-scan>
<!-- 情况二：指定要排除的组件 -->
<context:component-scan base-package="com.atguigu">
    <!-- context:exclude-filter标签：指定排除规则 -->
    <!--
    type：设置排除或包含的依据
    type="annotation"，根据注解排除，expression中设置要排除的注解的全类名
    type="assignable"，根据类型排除，expression中设置要排除的类型的全类名
    -->
    <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    <!--<context:exclude-filter type="assignable" expression="com.atguigu.controller.UserController"/>-->
</context:component-scan>
<!-- 情况三：仅扫描指定组件 -->
<context:component-scan base-package="com.atguigu" use-default-filters="false">
    <!-- context:include-filter标签：指定在原有扫描规则的基础上追加的规则 -->
    <!-- use-default-filters属性：取值false表示关闭默认扫描规则 -->
    <!-- 此时必须设置use-default-filters="false"，因为默认规则即扫描指定包下所有类 -->
    <!--
    type：设置排除或包含的依据
    type="annotation"，根据注解排除，expression中设置要排除的注解的全类名
    type="assignable"，根据类型排除，expression中设置要排除的类型的全类名
    -->
    <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    <!--<context:include-filter type="assignable"
    expression="com.atguigu.controller.UserController"/>-->
</context:component-scan>
```

### 基于注解的自动装配

`@Autowired` 可以应用到 Bean 的属性变量、setter 方法、非 setter 方法及构造函数等，默认按照 Bean 的类型进行装配。 @Autowired 注解默认按照 Bean 的类型进行装配，默认情况下它要求依赖对象必须存在，如果允许 null 值，可以设置它的 required 属性为 false。如果我们想使用按照名称（byName）来装配，可以结合 @Qualifier 注解一起使用
`@Resource` 作用与 Autowired 相同，区别在于 @Autowired 默认按照 Bean 类型装配，而 @Resource 默认按照 Bean 的名称进行装配。@Resource 中有两个重要属性：name 和 type。Spring 将 name 属性解析为 Bean 的实例名称，type 属性解析为 Bean 的实例类型。如果指定 name 属性，则按实例名称进行装配；如果指定 type 属性，则按 Bean 类型进行装配；如果都不指定，则先按 Bean 实例名称装配，如果不能匹配，则再按照 Bean 类型进行装配；如果都无法匹配，则抛出 NoSuchBeanDefinitionException 异常。
`@Qualifier` 与 @Autowired 注解配合使用，会将默认的按 Bean 类型装配修改为按 Bean 的实例名称装配，Bean 的实例名称由 @Qualifier 注解的参数指定。

```java
//在成员变量上直接标记@Autowired注解即可完成自动装配，
// @Autowired 只能在bean中生效

@Autowired // 属性
private UserService userService;

// 沟槽方法
X(@Autowired UserService userService){....}

@Autowired // set方法
setPerson(Person person){}

@Autowired//接口多实现注入,Spring会自动把所有类型为Validator的Bean装配为一个List注入进来
List<Validator> validators;
//因为Spring是通过扫描classpath获取到所有的Bean，而List是有序的，要指定List中Bean的顺序，可以加上@Order注解

// 可选注入
@Autowired(required = false) //这个参数告诉Spring容器，如果找到一个类型为ZoneId的Bean，就注入，如果找不到，就忽略。这种方式非常适合有定义就使用定义，没有就使用默认值的情况。
ZoneId zoneId = ZoneId.systemDefault();
```

### 创建第三方 bean

如果一个 Bean 不在我们自己的 package 管理之类，例如 ZoneId，如何创建它？

答案是我们自己在@Configuration 类中编写一个 Java 方法创建并返回它，注意给方法标记一个@Bean 注解：

Spring 对标记为@Bean 的方法只调用一次，因此返回的 Bean 仍然是单例。

```java
@Configuration
@ComponentScan
public class AppConfig {
    // 创建一个Bean:
    @Bean
    ZoneId createZoneId() {
        return ZoneId.of("Z");
    }
}
```

### 启动程序

```java
@Configuration //标注了@Configuration，表示它是一个配置类，因为我们创建ApplicationContext时,使用的实现类是AnnotationConfigApplicationContext，必须传入一个标注了@Configuration的类名。
@ComponentScan //自动搜索当前类所在的包以及子包，把所有标注为@Component的Bean自动创建出来，并根据@Autowired进行装配
public class AppConfig {
    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
        UserService userService = context.getBean(UserService.class);
        User user = userService.login("bob@example.com", "password");
        System.out.println(user.getName());
    }
}
```

### 生命周期

Spring 只根据 Annotation 查找无参数方法，对方法名不作要求。

1. 调用构造方法创建 MailService 实例
2. 根据`@Autowired `进行注入
3. 调用标记有`@PostConstruct` 的 init()方法进行初始化
4. 而销毁时，容器会首先调用标记有`@PreDestroy` 的 shutdown()方法。

```java
<dependency>
    <groupId>javax.annotation</groupId>
    <artifactId>javax.annotation-api</artifactId>
    <version>1.3.2</version>
</dependency>

@Component
public class MailService {
    @Autowired(required = false)
    ZoneId zoneId = ZoneId.systemDefault();
    @PostConstruct
    public void init() {
        System.out.println("Init mail service with zoneId = " + this.zoneId);
    }
    @PreDestroy
    public void shutdown() {
        System.out.println("Shutdown mail service");
    }
}
```

### 常见异常

1. `NoUniqueBeanDefinitionException` 意思是
   1. 出现了重复的 Bean 定义;`@Bean @Qualifier("新名字")`;
   2. 存在多个同类型的 Bean 时，注入 ZoneId 又会报错,需要指定 Bean 的名称;
      1. `@Autowired(required = false) @Qualifier("z") // 指定注入名称为"z"的ZoneId`;
      2. `@Primary // 指定为主要Bean`在注入时，如果没有指出 Bean 的名字，Spring 会注入标记有@Primary 的 Bean。

### 使用配置文件

1. `@Value("path")` 注入文件地址
2. `@PropertySource("app.properties")` 自动读取配置文件,需要配置类
3. `@Value("${app.zone[:Z]}")` 表示读取 key 为 app.zone 的 value，但如果 key 不存在，就使用默认值 Z; 用在@PropertySource 生效之后;可以用在属性和方法参数
4. `@Value("#{smtpConfig.host}")`,#{}表示从 JavaBean 读取属性。从名称为 smtpConfig 的 Bean 读取 host 属性，即调用 getHost()方法。

使用一个独立的 JavaBean 持有所有属性，然后在其他 Bean 中以#{bean.property}注入的好处是，多个 Bean 都可以引用同一个 Bean 的某个属性。

```java
@Component
public class AppService {
    @Value("classpath:/logo.txt") // 使用classpath
    private Resource resource;

    private String logo;

    @Value("file:/path/to/logo.txt") // 使用绝对路径
    private Resource resource;

    @PostConstruct
    public void init() throws IOException {
        try (var reader = new BufferedReader(
                new InputStreamReader(resource.getInputStream(), StandardCharsets.UTF_8))) {
            this.logo = reader.lines().collect(Collectors.joining("\n"));
        }
    }
}
```

### 条件装配

Spring 为应用程序准备了 Profile 这一概念，用来表示不同的环境。

例如，我们分别定义开发、测试和生产这 3 个环境：` native``test``production `

创建某个 Bean 时，Spring 容器可以根据注解`@Profile` 来决定是否创建。

在运行程序时，加上 JVM 参数`-Dspring.profiles.active=test` 就可以指定以 `test` 环境启动。

```java
@Configuration
@ComponentScan
public class AppConfig {
    @Bean
    @Profile("!test")
    ZoneId createZoneId() {
        return ZoneId.systemDefault();
    }
    @Bean
    @Profile("test")
    ZoneId createZoneIdForTest() {
        return ZoneId.of("America/New_York");
    }

    @Bean
    @Profile({ "test", "master" }) // 同时满足test和master
    ZoneId createZoneId() {
        ...
    }
}

```

除了根据@Profile 条件来决定是否创建某个 Bean 外，Spring 还可以根据`@Conditional` 决定是否创建某个 Bean。

```java
@Component
@Conditional(OnSmtpEnvCondition.class) // 如果满足OnSmtpEnvCondition的条件，才会创建SmtpMailService这个Bean
public class SmtpMailService implements MailService {
    ...
}

public class OnSmtpEnvCondition implements Condition {
    public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
      //OnSmtpEnvCondition的条件是存在环境变量smtp，值为true
      return "true".equalsIgnoreCase(System.getenv("smtp"));
    }
}
```

Spring 只提供了@Conditional 注解，具体判断逻辑还需要我们自己实现。Spring Boot 提供了更多使用起来更简单的条件注解，

```java
// spring-boot 环境
@Component
@ConditionalOnProperty(name="app.smtp", havingValue="true") //如果配置文件中存在app.smtp=true，则创建MailService
public class MailService {
    ...
}
@Component
@ConditionalOnClass(name = "javax.mail.Transport") //如果当前classpath中存在类javax.mail.Transport，则创建MailService
public class MailService {
    ...
}
```

# AOP 面向切面编程

AOP 的全称是“Aspect Oriented Programming”，译为“面向切面编程”，和 OOP（面向对象编程）类似，它也是一种编程思想。

与 OOP 中纵向的父子继承关系不同，AOP 是通过**横向的抽取机制**实现的。 **它将应用中的一些非业务的通用功能抽取出来单独维护，并通过声明的方式（例如配置文件、注解等）定义这些功能要以何种方式作用在那个应用中，而不是在业务模块的代码中直接调用。**

这虽然设计公共函数有几分类似，但传统的公共函数除了在代码直接硬调用之外并没有其他手段。AOP 则为这一问题**提供了一套灵活多样的实现方法**（例如 Proxy 代理、拦截器、字节码翻译技术等），**可以在无须修改任何业务代码的基础上完成对这些通用功能的调用和修改**。

AOP 编程和 OOP 编程的目标是一致的，都是**为了减少程序中的重复性代码，让开发人员有更多的精力专注于业务逻辑的开发，**只不过两者的实现方式大不相同。

AOP 不是用来替换 OOP 的，而是 OOP 的一种延伸，用来解决 OOP 编程中遇到的问题。

在 Java 平台上，对于 AOP 的织入，有 3 种方式：

**编译期**：在编译时，由编译器把切面调用编译进字节码，这种方式需要定义新的关键字并扩展编译器，AspectJ 就扩展了 Java 编译器，使用关键字 aspect 来实现织入；
**类加载器**：在目标类被装载到 JVM 时，通过一个特殊的类加载器，对目标类的字节码重新“增强”；
**运行期**：目标对象和切面都是普通 Java 类，通过 JVM 的动态代理功能或者第三方库实现运行期动态织入。 最简单的方式是第三种，Spring 的 AOP 实现就是基于 JVM 的动态代理。由于 JVM 的动态代理要求必须实现接口，如果一个普通类没有业务接口，就需要通过 CGLIB 或者 Javassist 这些第三方库实现。

## 术语

**Joinpoint（连接点）** AOP 的核心概念，指的是程序执行期间明确定义的一个点，例如方法的调用、类初始化、对象实例化等。在 Spring 中，连接点则指可以被动态代理拦截目标类的方法。
**Advice（通知）** 指拦截到 Joinpoint 之后要执行的代码，即对切入点增强的内容。有 5 种类型：

- `before（前置通知）` 通知方法在目标方法调用之前执行
- `after（后置通知）` 通知方法在目标方法返回或异常后调用
- `after-returning（返回后通知）` 通知方法会在目标方法返回后调用
- `after-throwing（抛出异常通知）` 通知方法会在目标方法抛出异常后调用
- `around（环绕通知）` 通知方法会将目标方法封装起来

**Aspect（切面）** 切面是切入点（Pointcut）和通知（Advice）的结合。封装通知方法的类
**Target（目标）** 指被代理的目标对象，通常也被称为被通知（advised）对象。
**Proxy（代理）** 指生成的代理对象。
**Pointcut（切入点）** 又称切点，指要对哪些 Joinpoint 进行拦截，即被拦截的连接点。定位连接点的方式。
**Weaving（织入）** 指把增强代码应用到目标对象上，生成代理对象的过程。

**动态代理（InvocationHandler）**：JDK 原生的实现方式，需要被代理的目标类必须实现接口。因为这个技术要求代理对象和目标对象实现同样的接口（兄弟两个拜把子模式）。
**cglib**：通过继承被代理的目标类（认干爹模式）实现代理，所以不需要目标类实现接口。
**AspectJ**：本质上是静态代理，将代理逻辑“织入”被代理的目标类编译得到的字节码文件，所以最终效果是动态的。weaver 就是织入器。Spring 只是借用了 AspectJ 中的注解。

## 基于注解的 AOP 编程

### 添加依赖

```xml
<!-- spring-aspects会帮我们传递过来aspectjweaver -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aspects</artifactId>
    <version>5.3.1</version>
</dependency>
```

### 创建被代理对象

```java
public interface Calculator {
    int add(int i, int j);
    int sub(int i, int j);
    int mul(int i, int j);
    int div(int i, int j);
}
@Component
public class CalculatorPureImpl implements Calculator {
    @Override
    public int add(int i, int j) {
        int result = i + j;
        System.out.println("方法内部 result = " + result);
        return result;
    }
    @Override
    public int sub(int i, int j) {
        int result = i - j;
        System.out.println("方法内部 result = " + result);
        return result;
    }
    @Override
    public int mul(int i, int j) {
        int result = i * j;
        System.out.println("方法内部 result = " + result);
        return result;
    }
    @Override
    public int div(int i, int j) {
        int result = i / j;
        System.out.println("方法内部 result = " + result);
        return result;
    }
}

```

### 创建切面类并配置

1. `@Aspect` 表示这个类是一个切面类
2. `@Order(0)` 表示这个切面执行顺序
3. 通知,或者说监听
   1. 前置通知：使用`@Before`注解标识，在被代理的目标方法前执行
   2. 返回通知：使用`@AfterReturning`注解标识，在被代理的目标方法成功结束后执行（寿终正寝）
   3. 异常通知：使用`@AfterThrowing`注解标识，在被代理的目标方法异常结束后执行（死于非命）
   4. 后置通知：使用`@After`注解标识，在被代理的目标方法最终结束后执行（盖棺定论）
   5. 环绕通知：使用`@Around`注解标识，使用 try...catch...finally 结构围绕整个被代理的目标方法，包括上面四种通知对应的所有位置

```java
// @Aspect表示这个类是一个切面类
@Aspect
// @Component注解保证这个切面类能够放入IOC容器
@Component
@Order(0) //多个切面使用order确定顺序
public class LogAspect {
    @Before("execution(public int com.atguigu.aop.annotation.CalculatorImpl.*(..))")
    public void beforeMethod(JoinPoint joinPoint){
        String methodName = joinPoint.getSignature().getName();
        String args = Arrays.toString(joinPoint.getArgs());
        System.out.println("Logger-->前置通知，方法名："+methodName+"，参数："+args);
    }
    @After("execution(*com.atguigu.aop.annotation.CalculatorImpl.*(..))")
    public void afterMethod(JoinPoint joinPoint){
        String methodName = joinPoint.getSignature().getName();
        System.out.println("Logger-->后置通知，方法名："+methodName);
    }
    @AfterReturning(value = "execution(*com.atguigu.aop.annotation.CalculatorImpl.*(..))", returning = "result")
    public void afterReturningMethod(JoinPoint joinPoint, Object result){
        String methodName = joinPoint.getSignature().getName();
        System.out.println("Logger-->返回通知，方法名："+methodName+"，结果："+result);
    }
    @AfterThrowing(value = "execution(*com.atguigu.aop.annotation.CalculatorImpl.*(..))", throwing = "ex")
    public void afterThrowingMethod(JoinPoint joinPoint, Throwable ex){
        String methodName = joinPoint.getSignature().getName();
        System.out.println("Logger-->异常通知，方法名："+methodName+"，异常："+ex);
    }
    @Around("execution(*com.atguigu.aop.annotation.CalculatorImpl.*(..))")
    public Object aroundMethod(ProceedingJoinPoint joinPoint){
        String methodName = joinPoint.getSignature().getName();
        String args = Arrays.toString(joinPoint.getArgs());
        Object result = null;
        try {
            System.out.println("环绕通知-->目标对象方法执行之前");
            //目标对象（连接点）方法的执行
            result = joinPoint.proceed();
            System.out.println("环绕通知-->目标对象方法返回值之后");
        } catch (Throwable throwable) {
            throwable.printStackTrace();
            System.out.println("环绕通知-->目标对象方法出现异常时");
        } finally {
            System.out.println("环绕通知-->目标对象方法执行完毕");
        }
        return result;
    }
}

```

```xml
<!--
基于注解的AOP的实现：
1、将目标对象和切面交给IOC容器管理（注解+扫描）
2、开启AspectJ的自动代理，为目标对象自动生成代理
3、将切面类通过注解@Aspect标识
-->
<context:component-scan base-package="com.atguigu.aop.annotation"></context:component-scan>
<aop:aspectj-autoproxy />

或者在启动类添加 @EnableAspectJAutoProxy
```

### 切入点表达式

```java
@Before("execution(public int com.atguigu.aop.annotation.CalculatorImpl.func(int,int))")
// execution(方法) : 固定格式
// public int 权限修饰符 返回值类型 ;用*号代替“权限修饰符”和“返回值”部分表示“权限修饰符”和“返回值”不限
// 想要明确指定一个返回值类型，那么必须同时写明权限修饰符
@Before("execution(* com.atguigu.aop.annotation.CalculatorImpl.func(int,int))")
// com.atguigu.aop.annotation.CalculatorImpl 方法所在类的全限定名;
@Before("execution(* *.atguigu.aop.annotation.CalculatorImpl.func(int,int))") //一个*号只能代表包的层次结构中的一层，表示这一层是任意的
@Before("execution(* *..CalculatorImpl.func(int,int))") //*.. 表示包名任意、包的层次深度任意
@Before("execution(* *..*.func(int,int))") //类名部分整体用*号代替，表示类名任意
@Before("execution(* *..*Impl.func(int,int))") //使用*号代替类名的一部分
// func 方法名
@Before("execution(* *..*Impl.*(int,int))") //可以使用*号表示方法名任意
@Before("execution(* *..*Impl.*func(int,int))") //可以使用*号代替方法名的一部分
// (int,int) 参数列表 ---基本数据类型和对应的包装类型是不一样的
@Before("execution(* *..*Impl.*func(..))") //(..)表示参数列表任意
@Before("execution(* *..*Impl.*func(int,..))") //使用(int,..)表示参数列表以一个int类型的参数开头
```

切入点表达式可以重用

```java
@Before("execution(* *..*Impl.*func(int,..))")
void func1()
// 同一包名下
@After("func1()")
// 不同包名下
@Before("com.atguigu.aop.CommonPointCut.func1()")
```

监听含有某种注解的方法

```java
@Aspect
@Component
public class MetricAspect {
    @Around("@annotation(metricTime)") //MetricTime 是一个注解,做参数,将参数变量放入通知
    public Object metric(ProceedingJoinPoint joinPoint, MetricTime metricTime) throws Throwable {
        String name = metricTime.value();
        long start = System.currentTimeMillis();
        try {
            return joinPoint.proceed();
        } finally {
            long t = System.currentTimeMillis() - start;
            // 写入日志或发送至JMX:
            System.err.println("[Metrics] " + name + ": " + t + "ms");
        }
    }
}
```

## 基于 XML 的 AOP（了解）

```xml
<context:component-scan base-package="com.atguigu.aop.xml"></context:componentscan>
<aop:config>
    <!--配置切面类-->
    <aop:aspect ref="loggerAspect">
        <aop:pointcut id="pointCut" expression="execution(*    com.atguigu.aop.xml.CalculatorImpl.*(..))"/>
        <aop:before method="beforeMethod" pointcut-ref="pointCut"></aop:before>
        <aop:after method="afterMethod" pointcut-ref="pointCut"></aop:after>
        <aop:after-returning method="afterReturningMethod" returning="result" pointcut-ref="pointCut"></aop:after-returning>
        <aop:after-throwing method="afterThrowingMethod" throwing="ex" pointcutref="pointCut"></aop:after-throwing>
        <aop:around method="aroundMethod" pointcut-ref="pointCut"></aop:around>
    </aop:aspect>
    <aop:aspect ref="validateAspect" order="1">
        <aop:before method="validateBeforeMethod" pointcut-ref="pointCut">
        </aop:before>
    </aop:aspect>
</aop:config>
```

# spring jdbc

Spring 提供了一个 Spring JDBC 模块，它对 JDBC API 进行了封装，其的主要目的降低 JDBC API 的使用难度，以一种更直接、更简洁的方式使用 JDBC API。

使用 Spring JDBC，开发人员只需要定义必要的参数、指定需要执行的 SQL 语句，即可轻松的进行 JDBC 编程，对数据库进行访问。

至于驱动的加载、数据库连接的开启与关闭、SQL 语句的创建与执行、异常处理以及事务处理等繁杂乏味的工作，则都是由 Spring JDBC 完成的。这样就可以使开发人员从繁琐的 JDBC API 中解脱出来，有更多的精力专注于业务的开发。

## JdbcTemplate

Spring JDBC 提供了多个实用的数据库访问工具，以简化 JDBC 的开发，其中使用最多就是 JdbcTemplate。

JdbcTemplate 是 Spring JDBC 核心包（core）中的核心类，它可以通过配置文件、注解、Java 配置类等形式获取数据库的相关信息，实现了对 JDBC 开发过程中的驱动加载、连接的开启和关闭、SQL 语句的创建与执行、异常处理、事务处理、数据类型转换等操作的封装。我们只要对其传入 SQL 语句和必要的参数即可轻松进行 JDBC 编程。

```xml
<dependencies>
    <!-- 基于Maven依赖传递性，导入spring-context依赖即可导入当前所需所有jar包 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.3.1</version>
    </dependency>
    <!-- Spring 持久化层支持jar包 -->
    <!-- Spring 在执行持久化层操作、与持久化层技术进行整合过程中，需要使用orm、jdbc、tx三个
    jar包 -->
    <!-- 导入 orm 包就可以通过 Maven 的依赖传递性把其他两个也导入 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-orm</artifactId>
        <version>5.3.1</version>
    </dependency>
    <!-- Spring 测试相关 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-test</artifactId>
        <version>5.3.1</version>
    </dependency>
    <!-- junit测试 -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>
    <!-- MySQL驱动 -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.16</version>
    </dependency>
    <!-- 数据源 -->
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid</artifactId>
        <version>1.0.31</version>
    </dependency>
</dependencies>
```

```s
# jdbc.properties
jdbc.user=root
jdbc.password=atguigu
jdbc.url=jdbc:mysql://localhost:3306/ssm
jdbc.driver=com.mysql.cj.jdbc.Driver
```

```xml
<!-- 导入外部属性文件 -->
<context:property-placeholder location="classpath:jdbc.properties" />
<!-- 配置数据源 -->
<bean id="druidDataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="url" value="${atguigu.url}"/>
    <property name="driverClassName" value="${atguigu.driver}"/>
    <property name="username" value="${atguigu.username}"/>
    <property name="password" value="${atguigu.password}"/>
</bean>
<!-- 配置 JdbcTemplate -->
<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
<!-- 装配数据源 -->
    <property name="dataSource" ref="druidDataSource"/>
</bean>
```

JdbcTemplate 的全限定命名为 `org.springframework.jdbc.core.JdbcTemplate`，它提供了大量的查询和更新数据库的方法，如下表所示。

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:spring-jdbc.xml")
public class JDBCTemplateTest {
    @Autowired
    private JdbcTemplate jdbcTemplate;
    @Test
    //测试增删改功能
    public void testUpdate(){
        String sql = "insert into t_emp values(null,?,?,?)";
        int result = jdbcTemplate.update(sql, "张三", 23, "男");
        System.out.println(result);
    }
}

```

## 事务

Spring 支持以下 2 种事务管理方式。

**编程式事务管理** 编程式事务管理是通过编写代码实现的事务管理。这种方式能够在代码中精确地定义事务的边界，我们可以根据需求规定事务从哪里开始，到哪里结束。
**声明式事务管理** Spring 声明式事务管理在底层采用了 AOP 技术，其最大的优点在于无须通过编程的方式管理事务，只需要在配置文件中进行相关的规则声明，就可以将事务规则应用到业务逻辑中。

选择编程式事务还是声明式事务，很大程度上就是在**控制权细粒度**和**易用性**之间进行权衡。
编程式对事物控制的细粒度更高，我们能够精确的控制事务的边界，事务的开始和结束完全取决于我们的需求，但这种方式存在一个致命的缺点，那就是事务规则与业务代码耦合度高，难以维护，因此我们很少使用这种方式对事务进行管理。
声明式事务易用性更高，对业务代码没有侵入性，耦合度低，易于维护，因此这种方式也是我们最常用的事务管理方式。

Spring 的声明式事务管理主要通过以下 2 种方式实现：
基于 XML 方式的声明式事务管理
基于注解方式的声明式事务管理

### 编程式事务管理

```java
Connection conn = ...;
try {
    // 开启事务：关闭事务的自动提交
    conn.setAutoCommit(false);
    // 核心操作
    // 提交事务
    conn.commit();
}catch(Exception e){
    // 回滚事务
    conn.rollBack();
}finally{
    // 释放数据库连接
    conn.close();
}
```

### 事务管理器

Spring 并不会直接管理事务，而是通过事务管理器对事务进行管理的。

在 Spring 中提供了一个 `org.springframework.transaction.PlatformTransactionManager` 接口，这个接口被称为 Spring 的事务管理器，其源码如下。

```java
public interface PlatformTransactionManager extends TransactionManager {
    // 用于获取事务的状态信息
    TransactionStatus getTransaction(@Nullable TransactionDefinition definition) throws TransactionException;
    // 用于提交事务
    void commit(TransactionStatus status) throws TransactionException;
    //	用于回滚事务
    void rollback(TransactionStatus status) throws TransactionException;
}
```

Spring 为不同的持久化框架或平台（例如 JDBC、Hibernate、JPA 以及 JTA 等）提供了不同的 PlatformTransactionManager 接口实现，这些实现类被称为**事务管理器实现**。

实现类 说明
`org.springframework.jdbc.datasource.DataSourceTransactionManager` 使用 `Spring JDBC` 或 `iBatis` 进行持久化数据时使用。
`org.springframework.orm.hibernate3.HibernateTransactionManager` 使用 `Hibernate` 3.0 及以上版本进行持久化数据时使用。
`org.springframework.orm.jpa.JpaTransactionManager` 使用 `JPA` 进行持久化时使用。
`org.springframework.jdo.JdoTransactionManager` 当持久化机制是 `Jdo` 时使用。
`org.springframework.transaction.jta.JtaTransactionManager` 使用 `JTA` 来实现事务管理，在一个事务跨越多个不同的资源（即分布式事务）使用该实现。
Spring 将 XML 配置中的事务信息封装到对象 TransactionDefinition 中，然后通过事务管理器的 getTransaction() 方法获得事务的状态（TransactionStatus），并对事务进行下一步的操作。

TransactionDefinition 接口提供了获取事务相关信息的方法，接口定义如下。

```java
public interface TransactionDefinition {
    //获取事务的传播行为
    int getPropagationBehavior();
    //获取事务的隔离级别
    int getIsolationLevel();
    //获取事务的名称
    String getName();
    //获取事务的超时时间
    int getTimeout();
    //获取事务是否只读
    boolean isReadOnly();
}
```

Spring 中提供了以下隔离级别，我们可以根据自身的需求自行选择合适的隔离级别。

方法 说明
`ISOLATION_DEFAULT` 使用后端数据库默认的隔离级别
`ISOLATION_READ_UNCOMMITTED` 允许读取尚未提交的更改，可能导致脏读、幻读和不可重复读
`ISOLATION_READ_COMMITTED` Oracle 默认级别，允许读取已提交的并发事务，防止脏读，可能出现幻读和不可重复读
`ISOLATION_REPEATABLE_READ` MySQL 默认级别，多次读取相同字段的结果是一致的，防止脏读和不可重复读，可能出现幻读
`ISOLATION_SERIALIZABLE` 完全服从 ACID 的隔离级别，防止脏读、不可重复读和幻读

事务传播行为（propagation behavior）指的是，当一个事务方法被另一个事务方法调用时，这个事务方法应该如何运行;事务方法指的是能让数据库表数据发生改变的方法，例如新增数据、删除数据、修改数据的方法。

Spring 提供了以下 7 种不同的事务传播行为。

名称 说明
`PROPAGATION_MANDATORY` 支持当前事务，如果不存在当前事务，则引发异常。
`PROPAGATION_NESTED` 如果当前事务存在，则在嵌套事务中执行。
`PROPAGATION_NEVER` 不支持当前事务，如果当前事务存在，则引发异常。
`PROPAGATION_NOT_SUPPORTED` 不支持当前事务，始终以非事务方式执行。
`PROPAGATION_REQUIRED` 默认传播行为，如果存在当前事务，则当前方法就在当前事务中运行，如果不存在，则创建一个新的事务，并在这个新建的事务中运行。
`PROPAGATION_REQUIRES_NEW` 创建新事务，如果已经存在事务则暂停当前事务。
`PROPAGATION_SUPPORTS` 支持当前事务，如果不存在事务，则以非事务方式执行。
TransactionStatus 接口提供了一些简单的方法，来控制事务的执行、查询事务的状态，接口定义如下。

```java
public interface TransactionStatus extends SavepointManager {
    //	获取是否是新事务
    boolean isNewTransaction();
    //	获取是否存在保存点
    boolean hasSavepoint();
    //设置事务回滚
    void setRollbackOnly();
    //获取事务是否回滚
    boolean isRollbackOnly();
    //	获取事务是否完成
    boolean isCompleted();
}
```

### xml

1. **引入 tx 命名空间**
   Spring 提供了一个 tx 命名空间，借助它可以极大地简化 Spring 中的声明式事务的配置。
   想要使用 tx 命名空间，第一步就是要在 XML 配置文件中添加 tx 命名空间的约束。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/tx
        http://www.springframework.org/schema/tx/spring-tx.xsd">
```

注意：由于 Spring 提供的声明式事务管理是依赖于 Spring AOP 实现的，因此我们在 XML 配置文件中还应该添加与 aop 命名空间相关的配置。

2. **配置事务管理器**
   接下来，我们就需要借助数据源配置，定义相应的事务管理器实现（`PlatformTransactionManager` 接口的实现类）的 Bean，配置内容如下

```xml
<!--配置数据源 -->
<bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
    <!--数据库连接地址-->
    <property name="url" value="xxx"/>
    <!--数据库的用户名-->
    <property name="username" value="xxx"/>
    <!--数据库的密码-->
    <property name="password" value="xxx"/>
    <!--数据库驱动-->
    <property name="driverClassName" value="xxx"/>
</bean>
<!--配置事务管理器，以 JDBC 为例-->
<bean id="transactionManager"
      class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource"></property>
</bean>
```

在以上配置中，配置的事务管理器实现为 DataSourceTransactionManager，即为 JDBC 和 iBatis 提供的 PlatformTransactionManager 接口实现。 3. **配置事务通知**
在 Spring 的 XML 配置文件中配置事务通知，指定事务作用的方法以及所需的事务属性。

```xml
<!--配置通知-->
<tx:advice id="tx-advice" transaction-manager="transactionManager">
    <!--配置事务参数-->
    <tx:attributes>
        <tx:method name="create*" propagation="REQUIRED" isolation="DEFAULT" read-only="false" timeout="10"/>
    </tx:attributes>
</tx:advice>
```

事务管理器配置
当我们使用` <tx:advice>` 来声明事务时，需要通过 `transaction-manager` 参数来定义一个事务管理器，这个参数的取值默认为 `transactionManager`

如果我们自己设置的事务管理器（第 2 步中设置的事务管理器 id）恰好与默认值相同，则可以省略对改参数的配置。

```xml
<tx:advice id="tx-advice" >
    <!--配置事务参数-->
    <tx:attributes>
        <tx:method name="create*" propagation="REQUIRED" isolation="DEFAULT" read-only="false" timeout="10"/>
    </tx:attributes>
</tx:advice>
```

但如果我们自己设置的事务管理器 id 与默认值不同，则必须手动在` <tx:advice>` 元素中通过 `transaction-manager` 参数指定。
对于`<tx:advice>` 来说，事务属性是被定义在`<tx:attributes>` 中的，该元素可以包含一个或多个 `<tx:method>` 元素。

`<tx:method> `元素包含多个属性参数，可以为某个或某些指定的方法（name 属性定义的方法）定义事务属性，如下表所示。

事务属性 说明
`propagation` 指定事务的传播行为。
`isolation` 指定事务的隔离级别。
`read-only` 指定是否为只读事务。
`timeout` 表示超时时间，单位为“秒”；声明的事务在指定的超时时间后，自动回滚，避免事务长时间不提交会回滚导致的数据库资源的占用。
`rollback-for` 指定事务对于那些类型的异常应当回滚，而不提交。
`no-rollback-for` 指定事务对于那些异常应当继续运行，而不回滚。 4. **配置切点切面**

`<tx:advice>` 元素只是定义了一个 AOP 通知，它并不是一个完整的事务性切面。我们在 `<tx:advice> `元素中并没有定义哪些 Bean 应该被通知，因此我们需要一个切点来做这件事。

在 Spring 的 XML 配置中，我们可以利用 Spring AOP 技术将事务通知（tx-advice）和切点配置到切面中，配置内容如下。

```xml
<!--配置切点和切面-->
<aop:config>
    <!--配置切点-->
    <aop:pointcut id="tx-pt" expression="execution(* net.biancheng.c.service.impl.OrderServiceImpl.*(..))"/>
    <!--配置切面-->
    <aop:advisor advice-ref="tx-advice" pointcut-ref="tx-pt"></aop:advisor>
</aop:config>
```

在以上配置中用到了 aop 命名空间，这就是我们为什么在给工程导入依赖时要引入 spring-aop 等 Jar 包的原因。

### 注解

1. **开启注解事务**

   tx 命名空间提供了一个 `<tx:annotation-driven>` 元素，用来开启注解事务，简化 Spring 声明式事务的 XML 配置。

`<tx:annotation-driven>` 元素的使用方式也十分的简单，我们只要在 Spring 的 XML 配置中添加这样一行配置即可。
`<tx:annotation-driven transaction-manager="transactionManager"></tx:annotation-driven>`

与 `<tx:advice>` 元素一样，`<tx:annotation-driven>` 也需要通过 `transaction-manager` 属性来定义一个事务管理器，这个参数的取值默认为 `transactionManager`。如果我们使用的事务管理器的 id 与默认值相同，则可以省略对该属性的配置，形式如下。
`<tx:annotation-driven/>`

通过 `<tx:annotation-driven>` 元素开启注解事务后，Spring 会自动对容器中的 Bean 进行检查，找到使用 `@Transactional` 注解的 Bean，并为其提供事务支持。

1. **使用 @Transactional 注解**
   `@Transactional` 注解是 Spring 声明式事务编程的核心注解，该注解既可以在类上使用，也可以在方法上使用。
   若 `@Transactional` 注解在类上使用，则表示类中的所有方法都支持事务；若 `@Transactional` 注解在方法上使用，则表示当前方法支持事务。
   `@Transactional` 注解包含多个属性，其中常用属性如下表。

事务属性 说明
`propagation` 指定事务的传播行为。
`isolation` 指定事务的隔离级别。
`read-only` 指定是否为只读事务。
`timeout` 表示超时时间，单位为“秒”；声明的事务在指定的超时时间后，自动回滚，避免事务长时间不提交会回滚导致的数据库资源的占用。
`rollback-for` 指定事务对于那些类型的异常应当回滚，而不提交。
`no-rollback-for` 指定事务对于那些异常应当继续运行，而不回滚。

`@Transactional(isolation = Isolation.DEFAULT, propagation = Propagation.REQUIRED, timeout = 10, readOnly = false)`

# 定时任务 Scheduler

```java
@Configuration
@ComponentScan
@EnableWebMvc
@EnableScheduling // 启动类开启定时任务
@EnableTransactionManagement
@PropertySource({ "classpath:/jdbc.properties", "classpath:/task.properties" })
public class AppConfig {
    ...
}

// 接下来，我们可以直接在一个Bean中编写一个public void无参数方法，然后加上@Scheduled注解
@Component
public class TaskService {
    final Logger logger = LoggerFactory.getLogger(getClass());

    @Scheduled(initialDelay = 60_000, fixedRate = 60_000) // 启动延迟60秒，并以60秒的间隔执行任务
    public void checkSystemStatusEveryMinute() {
        logger.info("Start check system status...");
    }

    // 使用 cron 表达式 秒 分 小时 天 月份 星期 年
    @Scheduled(cron = "${task.report:0 15 2 * * *}")
    public void cronDailyReport() {
        logger.info("Start daily report task...");
    }
}
```

集成 Quartz: 1. 多服务端集群,配置一个 JDBC 数据源，以便存储所有的任务调度计划以及任务执行状态
