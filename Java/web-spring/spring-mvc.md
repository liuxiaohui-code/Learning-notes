# 前言

MVC 是一种软件架构的思想，将软件按照模型、视图、控制器来划分
M：Model，模型层，指工程中的 JavaBean，作用是处理数据
JavaBean 分为两类：
一类称为实体类 Bean：专门存储业务数据的，如 Student、User 等
一类称为业务处理 Bean：指 Service 或 Dao 对象，专门用于处理业务逻辑和数据访问。
V：View，视图层，指工程中的 html 或 jsp 等页面，作用是与用户进行交互，展示数据
C：Controller，控制层，指工程中的 servlet，作用是接收请求和响应浏览器
MVC 的工作流程： 用户通过视图层发送请求到服务器，在服务器中请求被 Controller 接收，Controller 调用相应的 Model 层处理请求，处理完毕将结果返回到 ontroller，Controller 再根据请求处理的结果找到相应的 View 视图，渲染数据后最终响应给浏览器

SpringMVC 是 Spring 的一个后续产品，是 Spring 的一个子项目
SpringMVC 是 Spring 为表述层开发提供的一整套完备的解决方案。在表述层框架历经 Strust、WebWork、Strust2 等诸多产品的历代更迭之后，目前业界普遍选择了 SpringMVC 作为 Java EE 项目表述层开发的首选方案。
注：三层架构分为表述层（或表示层）、业务逻辑层、数据访问层，表述层表示前台页面和后台 servlet

# hello world

## 依赖

```xml
<dependencies>
    <!-- SpringMVC -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.3.1</version>
    </dependency>
    <!-- 日志 -->
    <dependency>
        <groupId>ch.qos.logback</groupId>
        <artifactId>logback-classic</artifactId>
        <version>1.2.3</version>
    </dependency>
    <!-- ServletAPI -->
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <version>3.1.0</version>
        <scope>provided</scope>
    </dependency>
    <!-- Spring5和Thymeleaf整合包 -->
    <dependency>
        <groupId>org.thymeleaf</groupId>
        <artifactId>thymeleaf-spring5</artifactId>
        <version>3.0.12.RELEASE</version>
    </dependency>
</dependencies>
```

## 配置文件

目录结构:

```yaml
src:
  - main
  - test
  - resources
  - webapp
    - WEB-INF
      - templates/index.html
      - web.xml # spring mvc 配置文件
      - SpringMVC-servlet.xml # 控制器配置文件,名称固定,位置固定,名称由web.xml中的servlet-name加上后缀 -servlet.xml组成

Tomcat:
  deployment_directory: ..../src/webapp # 精确到webapp目录
  context_path: /springMvc  #自定义根路径
```

**默认配置**
此配置作用下，SpringMVC 的配置文件默认位于 WEB-INF 下，默认名称为`<servlet-name>-servlet.xml`，例如，以下配置所对应 SpringMVC 的配置文件位于 WEB-INF 下，文件名为 springMVCservlet.xml

### web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
         http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <servlet>
        <!--
            web.xml 自动加载,名字固定,位置固定
            位置: WEB-INF 下
            名称: <servlet-name>-servlet.xml,当前配置下的配置文件名为 SpringMVC-servlet.xml

         -->
        <servlet-name>SpringMVC</servlet-name>
        <!-- DispatcherServlet 前端控制器 -->
        <!--  JspServlet 专门解析jsp的前端控制器   -->
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>SpringMVC</servlet-name>
        <!-- /除.jsp外所有请求  /*匹配任意请求  *.do后缀匹配        -->
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```

**扩展配置方式**
可通过`init-param`标签设置 SpringMVC 配置文件的位置和名称，通过`load-on-startup`标签设置 SpringMVC 前端控制器 DispatcherServlet 的初始化时间

```xml
<!-- 配置SpringMVC的前端控制器，对浏览器发送的请求统一进行处理 -->
<servlet>
    <servlet-name>springMVC</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servletclass>
    <!-- 通过初始化参数指定SpringMVC配置文件的位置和名称 -->
    <init-param>
        <!-- contextConfigLocation为固定值 -->
        <param-name>contextConfigLocation</param-name>
        <!-- 使用classpath:表示从类路径查找配置文件，例如maven工程中的
        src/main/resources -->
        <param-value>classpath:springMVC.xml</param-value>
    </init-param>
    <!--
    作为框架的核心组件，在启动过程中有大量的初始化操作要做
    而这些操作放在第一次请求时才执行会严重影响访问速度
    因此需要通过此标签将启动控制DispatcherServlet的初始化时间提前到服务器启动时
    -->
    <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>springMVC</servlet-name>
    <!--
    设置springMVC的核心控制器所能处理的请求的请求路径
    / 所匹配的请求可以是/login或.html或.js或.css方式的请求路径但是/不能匹配.jsp请求路径的请求
    /* 则能够匹配所有请求，例如在使用过滤器时，若需要对所有请求进行过滤，就需要使用/*的写法
    -->
    <url-pattern>/</url-pattern>
</servlet-mapping>

```

## 创建请求控制器

由于前端控制器对浏览器发送的请求进行了统一的处理，但是具体的请求有不同的处理过程，因此需要创建处理具体请求的类，即请求控制器请求控制器中每一个处理请求的方法成为控制器方法,因为 SpringMVC 的控制器由一个 POJO（普通的 Java 类）担任，因此需要通过`@Controller`注解将其标识为一个控制层组件，交给 Spring 的 IoC 容器管理，此时 SpringMVC 才能够识别控制器的存在

```java
@Controller
public class HelloController {
}
```

## 创建 SpringMVC 的配置文件

```xml

<!-- 自动扫描包 -->
<context:component-scan base-package="com.atguigu.mvc.controller"/>
<!-- 配置Thymeleaf视图解析器 -->
<bean id="viewResolver" class="org.thymeleaf.spring5.view.ThymeleafViewResolver">
    <property name="order" value="1"/>
    <property name="characterEncoding" value="UTF-8"/>
    <property name="templateEngine">
        <bean class="org.thymeleaf.spring5.SpringTemplateEngine">
            <property name="templateResolver">
                <bean class="org.thymeleaf.spring5.templateresolver.SpringResourceTemplateResolver">
                    <!-- 视图前缀 -->
                    <property name="prefix" value="/WEB-INF/templates/"/>
                    <!-- 视图后缀 -->
                    <property name="suffix" value=".html"/>
                    <property name="templateMode" value="HTML5"/>
                    <property name="characterEncoding" value="UTF-8" />
                </bean>
            </property>
        </bean>
    </property>
</bean>
<!--
处理静态资源，例如html、js、css、jpg
若只设置该标签，则只能访问静态资源，其他请求则无法访问
此时必须设置<mvc:annotation-driven/>解决问题
-->
<mvc:default-servlet-handler/>
<!-- 开启mvc注解驱动 -->
<mvc:annotation-driven>
    <mvc:message-converters>
        <!-- 处理响应中文内容乱码 -->
        <bean class="org.springframework.http.converter.StringHttpMessageConverter">
            <property name="defaultCharset" value="UTF-8" />
            <property name="supportedMediaTypes">
                <list>
                    <value>text/html</value>
                    <value>application/json</value>
                </list>
            </property>
        </bean>
    </mvc:message-converters>
</mvc:annotation-driven>

```

## 控制请求

```java
// @RequestMapping注解：处理请求和控制器方法之间的映射关系
// @RequestMapping注解的value属性可以通过请求地址匹配请求，/表示的当前工程的上下文路径
// localhost:8080/springMVC/
@RequestMapping("/")
public String index() {
    //设置视图名称
    return "index";
}
@RequestMapping("/hello")
public String HelloWorld() {
    return "target";
}


```

index.html

```HTML
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
    <head>
        <meta charset="UTF-8">
        <title>首页</title>
    </head>
    <body>
        <h1>首页</h1>
        <a th:href="@{/hello}">HelloWorld</a><br/>
    </body>
</html>
```

## 总结

浏览器发送请求，若请求地址符合前端控制器的 url-pattern，该请求就会被前端控制器 DispatcherServlet 处理。前端控制器会读取 SpringMVC 的核心配置文件，通过扫描组件找到控制器，将请求地址和控制器中@RequestMapping 注解的 value 属性值进行匹配，若匹配成功，该注解所标识的控制器方法就是处理请求的方法。处理请求的方法需要返回一个字符串类型的视图名称，该视图名称会被视图解析器解析，加上前缀和后缀组成视图的路径，通过 Thymeleaf 对视图进行渲染，最终转发到视图所对应页面

# 注解

## @RequestMapping 请求映射

- 用在类上,标识此控制器下所有请求的根路径信息
- 用在方法上,实际路径是类根路经与方法上路径的拼接
- 功能:
  - 请求路径
  - 请求方法
  - 请求头
  - 请求参数
  - content-type --- consumes
  - accept --- produces

**SpringMVC 支持 ant 风格的路径**
`？`：表示任意的单个字符
`*`：表示任意的 0 个或多个字符
`**`：表示任意层数的任意目录
注意：在使用 ** 时，只能使用 /**/xxx 的方式

```java
// 1. 标识一个类,设置映射请求的请求路径的初始信息
@Controller
@RequestMapping("/test")
public class RequestMappingController {
  // 2. 标识一个方法,设置映射请求请求路径的具体信息
  // 此时请求映射所映射的请求的请求路径为：/test/testRequestMapping
  @RequestMapping("/testRequestMapping")
  public String testRequestMapping(){
    return "success";
  }
}
```

## @RequestMapping 的派生注解

- 处理 get 请求的映射-->`@GetMapping`
- 处理 post 请求的映射-->`@PostMapping`
- 处理 put 请求的映射-->`@PutMapping`
- 处理 delete 请求的映射-->`@DeleteMapping`

但是目前浏览器只支持 get 和 post，若在 form 表单提交时，为 method 设置了其他请求方式的字符串（put 或 delete），则按照默认的请求方式 get 处理;
若要发送 put 和 delete 请求，则需要通过 spring 提供的过滤器 HiddenHttpMethodFilter，

## @PathVariable 路径占位符

**SpringMVC 支持路径中的占位符（重点）**
原始方式：/deleteUser?id=1
rest 方式：/user/delete/1

```java
@RequestMapping("/testRest/{id}/{username}")
public String testRest(@PathVariable("id") String id, @PathVariable("username")String username){
```

## @Controller 控制器标识

@Controller //标识一个类为控制器,在功能上是@Component 的别名

## @RestController

`@RestController `注解是 springMVC 提供的一个复合注解，标识在控制器的类上，就相当于为类添加了`@Controller` 注解，并且为其中的每个方法添加了`@ResponseBody` 注解

# 请求参数获取

## servletAPI 获取

将 HttpServletRequest 作为控制器方法的形参，此时 HttpServletRequest 类型的参数表示封装了当前请求的请求报文的对象.

```java
// "/testParam?username=admin&password=123456"
@RequestMapping("/testParam")
public String testParam(HttpServletRequest request){
  String username = request.getParameter("username");
  String password = request.getParameter("password");
  System.out.println("username:"+username+",password:"+password);
  return "success";
}
```

## 通过控制器方法的形参获取请求参数

在控制器方法的形参位置，设置和请求参数同名的形参，当浏览器发送请求，匹配到请求映射时，在 DispatcherServlet 中就会将请求参数赋值给相应的形参.

```java
// "/testParam?username=admin&password=123456"
@RequestMapping("/testParam")
public String testParam(String username, String password){
  System.out.println("username:"+username+",password:"+password);
  return "success";
}

/**
若请求所传输的请求参数中有多个同名的请求参数，此时可以在控制器方法的形参中设置字符串数组或者字符串类型的形参接收此请求参数
若使用字符串数组类型的形参，此参数的数组中包含了每一个数据 String[]
若使用字符串类型的形参，此参数的值为每个数据中间使用逗号拼接的结果 String : "x,a,b"
*/
```

## @RequestParam

@RequestParam 是将请求参数和控制器方法的形参创建映射关系,在 Spring MVC 中，“请求参数”映射到 query 参数、表单数据和多部分请求中的部分;在 WebFlux 只映射 query 参数.

## @RequestHeader

@RequestHeader 是将请求头信息和控制器方法的形参创建映射关系

## @CookieValue

@CookieValue 是将 cookie 数据和控制器方法的形参创建映射关系

## 通过 pojo 获取请求参数

可以在控制器方法的形参位置设置一个实体类类型的形参，此时若浏览器传输的请求参数的参数名和实体类中的属性名一致，那么请求参数就会为此属性赋值

```java
// /testpojo?id=&username=张三&password=123&age=23&sex=男
@RequestMapping("/testpojo")
public String testPOJO(User user){
  System.out.println(user);
  return "success";
}
//最终结果-->User{id=null, username='张三', password='123', age=23, sex='男',
email='123@qq.com'}
```

## 请求参数乱码问题

解决获取请求参数的乱码问题，可以使用 SpringMVC 提供的编码过滤器 CharacterEncodingFilter，但是必须在 web.xml 中进行注册

```xml
<!--配置springMVC的编码过滤器-->
<filter>
  <filter-name>CharacterEncodingFilter</filter-name>
  <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
  <init-param>
    <param-name>encoding</param-name>
    <param-value>UTF-8</param-value>
  </init-param>
  <init-param>
    <param-name>forceEncoding</param-name>
    <param-value>true</param-value>
  </init-param>
</filter>
<filter-mapping>
  <filter-name>CharacterEncodingFilter</filter-name>
  <url-pattern>/*</url-pattern>
</filter-mapping>

```

## @RequestBody

@RequestBody 可以获取请求体信息，使用@RequestBody 注解标识控制器方法的形参，当前请求的请求体就会为当前注解所标识的形参赋值

```java
@RequestMapping("/test/RequestBody")
public String testRequestBody(@RequestBody String requestBody){
  System.out.println("requestBody:"+requestBody); // requestBody:username=admin&password=123456
  return "success";
}
```

使用@RequestBody 获取 json 格式的请求参数的条件：

```xml
1. 添加依赖
<dependency>
<groupId>com.fasterxml.jackson.core</groupId>
<artifactId>jackson-databind</artifactId>
<version>2.12.1</version>
</dependency>

2. web.xml
SpringMVC的配置文件中设置开启mvc的注解驱动
<!--开启mvc的注解驱动-->
<mvc:annotation-driven />
```

3.在控制器方法的形参位置，设置 json 格式的请求参数要转换成的 java 类型（实体类或 map）的参
数，并使用@RequestBody 注解标识

```java
//将json格式的数据转换为map集合
@RequestMapping("/test/RequestBody/json")
public void testRequestBody(@RequestBody Map<String, Object> map,HttpServletResponse response) throws IOException {
  System.out.println(map);
  //{username=admin, password=123456}
  response.getWriter().print("hello,axios");
}
//将json格式的数据转换为实体类对象
@RequestMapping("/test/RequestBody/json")
public void testRequestBody(@RequestBody User user, HttpServletResponse response) throws IOException {
  System.out.println(user);
  //User{id=null, username='admin', password='123456', age=null,gender='null'}
  response.getWriter().print("hello,axios");
}
```

# 返回报文

## @ResponseBody

@ResponseBody 用于标识一个控制器方法，可以将该方法的返回值直接作为响应报文的响应体响应到浏览器

```java
@RequestMapping("/testResponseBody")
public String testResponseBody(){
  //此时会跳转到逻辑视图success所对应的页面
  return "success";
}
@RequestMapping("/testResponseBody")
@ResponseBody
public String testResponseBody(){
  //此时响应浏览器数据success
  return "success";
}
// ----------------------- 添加JSON依赖,注解驱动后,可以使用@ResponseBody返回对象,MVC将会将java 对象转换为JSON放到返回体中返回给前端
//响应浏览器list集合
@RequestMapping("/test/ResponseBody/json")
@ResponseBody
public List<User> testResponseBody(){
  User user1 = new User(1001,"admin1","123456",23,"男");
  User user2 = new User(1002,"admin2","123456",23,"男");
  User user3 = new User(1003,"admin3","123456",23,"男");
  List<User> list = Arrays.asList(user1, user2, user3);
  return list;
}
//响应浏览器map集合
@RequestMapping("/test/ResponseBody/json")
@ResponseBody
public Map<String, Object> testResponseBody(){
  User user1 = new User(1001,"admin1","123456",23,"男");
  User user2 = new User(1002,"admin2","123456",23,"男");
  User user3 = new User(1003,"admin3","123456",23,"男");
  Map<String, Object> map = new HashMap<>();
  map.put("1001", user1);
  map.put("1002", user2);
  map.put("1003", user3);
  return map;
}
//响应浏览器实体类对象
@RequestMapping("/test/ResponseBody/json")
@ResponseBody
public User testResponseBody(){
  return user;
}
```

## @RestController

# 域对象共享数据

## 使用 ServletAPI 向 request 域对象共享数据

```java
@RequestMapping("/testServletAPI")
public String testServletAPI(HttpServletRequest request){
request.setAttribute("testScope", "hello,servletAPI");
return "success";
}

```

## 使用 ModelAndView 向 request 域对象共享数据

```java
@RequestMapping("/testModelAndView")
public ModelAndView testModelAndView(){
/**
* ModelAndView有Model和View的功能
* Model主要用于向请求域共享数据
* View主要用于设置视图，实现页面跳转
*/
ModelAndView mav = new ModelAndView();
//向请求域共享数据
mav.addObject("testScope", "hello,ModelAndView");
//设置视图，实现页面跳转
mav.setViewName("success");
return mav;
}
```

## 使用 Model 向 request 域对象共享数据

```java
@RequestMapping("/testModel")
public String testModel(Model model){
model.addAttribute("testScope", "hello,Model");
return "success";
}
```

## 使用 map 向 request 域对象共享数据

```java
@RequestMapping("/testMap")
public String testMap(Map<String, Object> map){
map.put("testScope", "hello,Map");
return "success";
}
```

## 使用 ModelMap 向 request 域对象共享数据

```java
@RequestMapping("/testModelMap")
public String testModelMap(ModelMap modelMap){
modelMap.addAttribute("testScope", "hello,ModelMap");
return "success";
}

```

## Model、ModelMap、Map 的关系

Model、ModelMap、Map 类型的参数其实本质上都是 BindingAwareModelMap 类型

```java
public interface Model{}
public class ModelMap extends LinkedHashMap<String, Object> {}
public class ExtendedModelMap extends ModelMap implements Model {}
public class BindingAwareModelMap extends ExtendedModelMap {}
```

## 向 session 域共享数据

```java
@RequestMapping("/testSession")
public String testSession(HttpSession session){
session.setAttribute("testSessionScope", "hello,session");
return "success";
}
```

## 向 application 域共享数据

```java
@RequestMapping("/testApplication")
public String testApplication(HttpSession session){
ServletContext application = session.getServletContext();
application.setAttribute("testApplicationScope", "hello,application");
return "success";
}
```

# spring MVC 视图

SpringMVC 中的视图是 View 接口，视图的作用渲染数据，将模型 Model 中的数据展示给用户.
SpringMVC 视图的种类很多，默认有转发视图和重定向视图
当工程引入 jstl 的依赖，转发视图会自动转换为 JstlView
若使用的视图技术为 Thymeleaf，在 SpringMVC 的配置文件中配置了 Thymeleaf 的视图解析器，由此视
图解析器解析之后所得到的是 ThymeleafView

## ThymeleafView

当控制器方法中所设置的视图名称没有任何前缀时，此时的视图名称会被 SpringMVC 配置文件中所配置
的视图解析器解析，视图名称拼接视图前缀和视图
后缀所得到的最终路径，会通过转发的方式实现跳转

```java
@RequestMapping("/testHello")
public String testHello(){
return "hello";
}
```

## 转发视图

SpringMVC 中默认的转发视图是 InternalResourceView
SpringMVC 中创建转发视图的情况：
当控制器方法中所设置的视图名称以"forward:"为前缀时，创建 InternalResourceView 视图，此时的视
图名称不会被 SpringMVC 配置文件中所配置的视图解析器解析，而是会将前缀"forward:"去掉，剩余部
分作为最终路径通过转发的方式实现跳转
例如"forward:/"，"forward:/employee"

```java
@RequestMapping("/testForward")
public String testForward(){
return "forward:/testHello";
}

```

## 重定向视图

SpringMVC 中默认的重定向视图是 RedirectView
当控制器方法中所设置的视图名称以"redirect:"为前缀时，创建 RedirectView 视图，此时的视图名称不
会被 SpringMVC 配置文件中所配置的视图解析器解析，而是会将前缀"redirect:"去掉，剩余部分作为最
终路径通过重定向的方式实现跳转
例如"redirect:/"，"redirect:/employee

```java
@RequestMapping("/testRedirect")
public String testRedirect(){
return "redirect:/testHello";
}
```

## 视图控制器 view-controller

当控制器方法中，仅仅用来实现页面跳转，即只需要设置视图名称时，可以将处理器方法使用 view-controller 标签进行表示

```xml
<!--
path：设置处理的请求地址
view-name：设置请求地址所对应的视图名称
-->
<mvc:view-controller path="/testView" view-name="success"></mvc:view-controller>

注：
当SpringMVC中设置任何一个view-controller时，其他控制器中的请求映射将全部失效，此时需
要在SpringMVC的核心配置文件中设置开启mvc注解驱动的标签：
<mvc:annotation-driven />
```

# 支持 RESTful

由于 HTML 只支持发送 get 和 post 方式的请求，那么该如何发送 put 和 delete 请求呢？
` SpringMVC 提供了 HiddenHttpMethodFilter 帮助我们将 POST 请求转换为 DELETE 或 PUT 请求 ``HiddenHttpMethodFilter ` 处理 put 和 delete 请求的条件：
a>当前请求的请求方式必须为 `post`
b>当前请求必须传输请求参数 `_method`
满足以上条件， `HiddenHttpMethodFilter` 过滤器就会将当前请求的请求方式转换为请求参数`_method` 的值，因此请求参数`_method` 的值才是最终的请求方式
在 web.xml 中注册 HiddenHttpMethodFilter

```xml
<filter>
  <filter-name>HiddenHttpMethodFilter</filter-name>
  <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
</filter>
<filter-mapping>
  <filter-name>HiddenHttpMethodFilter</filter-name>
  <url-pattern>/*</url-pattern>
</filter-mapping>
```

# 文件上传下载

## 文件下载

ResponseEntity 用于控制器方法的返回值类型，该控制器方法的返回值就是响应到浏览器的响应报文,使用 ResponseEntity 实现下载文件的功能

```java
@RequestMapping("/testDown")
public ResponseEntity<byte[]> testResponseEntity(HttpSession session) throws IOException {
  //获取ServletContext对象
  ServletContext servletContext = session.getServletContext();
  //获取服务器中文件的真实路径
  String realPath = servletContext.getRealPath("/static/img/1.jpg");
  //创建输入流
  InputStream is = new FileInputStream(realPath);
  //创建字节数组
  byte[] bytes = new byte[is.available()];
  //将流读到字节数组中
  is.read(bytes);
  //创建HttpHeaders对象设置响应头信息
  MultiValueMap<String, String> headers = new HttpHeaders();
  //设置要下载方式以及下载文件的名字
  headers.add("Content-Disposition", "attachment;filename=1.jpg");
  //设置响应状态码
  HttpStatus statusCode = HttpStatus.OK;
  //创建ResponseEntity对象
  ResponseEntity<byte[]> responseEntity = new ResponseEntity<>(bytes, headers,  statusCode);
  //关闭输入流
  is.close();
  return responseEntity;
}

```

## 文件上传

文件上传要求 form 表单的请求方式必须为 post，并且添加属性 `enctype="multipart/form-data"`,SpringMVC 中将上传的文件封装到 `MultipartFile` 对象中，通过此对象可以获取文件相关信息

1. 添加依赖

```xml
<!-- https://mvnrepository.com/artifact/commons-fileupload/commons-fileupload --
>
<dependency>
<groupId>commons-fileupload</groupId>
<artifactId>commons-fileupload</artifactId>
<version>1.3.1</version>
</dependency>

```

2. 添加配置

```xml
<!--必须通过文件解析器的解析才能将文件转换为MultipartFile对象-->
<bean id="multipartResolver"
class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
</bean>
```

3. 使用

```java
@RequestMapping("/testUp")
public String testUp(MultipartFile photo, HttpSession session) throws IOException {
  //获取上传的文件的文件名
  String fileName = photo.getOriginalFilename();
  //处理文件重名问题
  String hzName = fileName.substring(fileName.lastIndexOf("."));
  fileName = UUID.randomUUID().toString() + hzName;
  //获取服务器中photo目录的路径
  ServletContext servletContext = session.getServletContext();
  String photoPath = servletContext.getRealPath("photo");
  File file = new File(photoPath);
  if(!file.exists()){
    file.mkdir();
  }
  String finalPath = photoPath + File.separator + fileName;
  //实现上传功能
  photo.transferTo(new File(finalPath));
  return "success";
}
@RequestMapping("/testUps")
public String testUp(MultipartFile[] photos, HttpSession session) throws IOException {
  for(MultipartFile photo :photos){
    //获取上传的文件的文件名
    String fileName = photo.getOriginalFilename();
    //处理文件重名问题
    String hzName = fileName.substring(fileName.lastIndexOf("."));
    fileName = UUID.randomUUID().toString() + hzName;
    //获取服务器中photo目录的路径
    ServletContext servletContext = session.getServletContext();
    String photoPath = servletContext.getRealPath("photo");
    File file = new File(photoPath);
    if(!file.exists()){
      file.mkdir();
    }
    String finalPath = photoPath + File.separator + fileName;
    //实现上传功能
    photo.transferTo(new File(finalPath));
  }

  return "success";
}
```

# 拦截器

SpringMVC 中的拦截器用于拦截控制器方法的执行

1. 实现接口 `org.springframework.web.servlet.HandlerInterceptor`
   1. preHandle,控制器之前执行
   2. postHandle,控制器执行后执行
   3. afterCompletion,处理完视图和模型数据，渲染视图完毕之后执行 afterCompletion()
2. 注册拦截器

```xml
<bean class="com.atguigu.interceptor.FirstInterceptor"></bean>
<ref bean="firstInterceptor"></ref>
<!-- 以上两种配置方式都是对DispatcherServlet所处理的所有的请求进行拦截 -->
<mvc:interceptor>
<mvc:mapping path="/**"/>
<mvc:exclude-mapping path="/testRequestEntity"/>
<ref bean="firstInterceptor"></ref>
</mvc:interceptor>
<!--
以上配置方式可以通过ref或bean标签设置拦截器，通过mvc:mapping设置需要拦截的请求，通过
mvc:exclude-mapping设置需要排除的请求，即不需要拦截的请求
-->
```

多个拦截器执行顺序: preHandle()会按照配置的顺序执行，而 postHandle()和 afterCompletion()会按照配置的反序执行;
若某个拦截器的 preHandle()返回了 false,preHandle()返回 false 和它之前的拦截器的 preHandle()都会执行，postHandle()都不执行，返回 false 的拦截器之前的拦截器的 afterCompletion()会执行

# 异常处理器

## 基于配置的异常处理

SpringMVC 提供了一个处理控制器方法执行过程中所出现的异常的接口： `HandlerExceptionResolver`
HandlerExceptionResolver 接口的实现类有：DefaultHandlerExceptionResolver 和 SimpleMappingExceptionResolver
SpringMVC 提供了自定义的异常处理器 SimpleMappingExceptionResolver，使用方式：

```xml
<bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
  <property name="exceptionMappings">
    <props>
      <!--
      properties的键表示处理器方法执行过程中出现的异常
      properties的值表示若出现指定异常时，设置一个新的视图名称，跳转到指定页面
      -->
      <prop key="java.lang.ArithmeticException">error</prop>
    </props>
  </property>
  <!--
  exceptionAttribute属性设置一个属性名，将出现的异常信息在请求域中进行共享
  -->
  <property name="exceptionAttribute" value="ex"></property>
</bean>
```

自定义实现:

1. 实现接口 `org.springframework.web.servlet.HandlerExceptionResolver`
2. 配置异常处理器

## 基于注解的异常处理

`@ControllerAdvice` 类,将当前类标识为异常处理的组件
`@RestControllerAdvice` 类,是`@ControllerAdvice`和`@ResponseBody`的结合

```java
//@ControllerAdvice将当前类标识为异常处理的组件
@ControllerAdvice
public class ExceptionController {
  //@ExceptionHandler用于设置所标识方法处理的异常
  @ExceptionHandler(ArithmeticException.class)
  //ex表示当前请求处理中出现的异常对象
  public String handleArithmeticException(Exception ex, Model model){
    model.addAttribute("ex", ex);
    return "error";
  }
}

```

# 注解搭建 spring MVC

## 创建初始化类,替代 web.xml

在 Servlet3.0 环境中，容器会在类路径中查找实现 `javax.servlet.ServletContainerInitializer` 接口的类，如果找到的话就用它来配置 Servlet 容器。
Spring 提供了这个接口的实现，名为`SpringServletContainerInitializer`，这个类反过来又会查找实现 `WebApplicationInitializer` 的类并将配置的任务交给它们来完成。
Spring3.2 引入了一个便利的 `WebApplicationInitializer` 基础实现，名为 `AbstractAnnotationConfigDispatcherServletInitializer` ，当我们的类扩展了`AbstractAnnotationConfigDispatcherServletInitializer` 并将其部署到 `Servlet3.0` 容器的时候，容器会自动发现它，并用它来配置 Servlet 上下文。

```java
public class WebInit extends AbstractAnnotationConfigDispatcherServletInitializer {
  /**
  * 指定spring的配置类
  * @return
  */
  @Override
  protected Class<?>[] getRootConfigClasses() {
    return new Class[]{SpringConfig.class};
  }
  /**
  * 指定SpringMVC的配置类
  * @return
  */
  @Override
  protected Class<?>[] getServletConfigClasses() {
    return new Class[]{WebConfig.class};
  }
  /**
  * 指定DispatcherServlet的映射规则，即url-pattern
  * @return
  */
  @Override
  protected String[] getServletMappings() {
    return new String[]{"/"};
  }
  /**
  * 添加过滤器
  * @return
  */
  @Override
  protected Filter[] getServletFilters() {
    CharacterEncodingFilter encodingFilter = new CharacterEncodingFilter();
    encodingFilter.setEncoding("UTF-8");
    encodingFilter.setForceRequestEncoding(true);
    HiddenHttpMethodFilter hiddenHttpMethodFilter = new
    HiddenHttpMethodFilter();
    return new Filter[]{encodingFilter, hiddenHttpMethodFilter};
  }
}
```

## 创建 SpringConfig 配置类，代替 spring 的配置文件

```java
@Configuration
public class SpringConfig {
//ssm整合之后，spring的配置信息写在此类中
}

```

## 创建 WebConfig 配置类，代替 SpringMVC 的配置文件

```java
@Configuration
//扫描组件
@ComponentScan("com.atguigu.mvc.controller")
//开启MVC注解驱动
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {
  //使用默认的servlet处理静态资源
  @Override
  public void configureDefaultServletHandling(DefaultServletHandlerConfigurer   configurer) {
    configurer.enable();
  }
  //配置文件上传解析器
  @Bean
  public CommonsMultipartResolver multipartResolver(){
    return new CommonsMultipartResolver();
  }
  //配置拦截器
  @Override
  public void addInterceptors(InterceptorRegistry registry) {
    FirstInterceptor firstInterceptor = new FirstInterceptor();
    registry.addInterceptor(firstInterceptor).addPathPatterns("/**");
  }
  //配置视图控制
  /*@Override
  public void addViewControllers(ViewControllerRegistry registry) {
    registry.addViewController("/").setViewName("index");
  }*/
  //配置异常映射
  /*@Override
  public void   configureHandlerExceptionResolvers(List<HandlerExceptionResolver> resolvers) {
    SimpleMappingExceptionResolver exceptionResolver = new
    SimpleMappingExceptionResolver();
    Properties prop = new Properties();
    prop.setProperty("java.lang.ArithmeticException", "error");
    //设置异常映射
    exceptionResolver.setExceptionMappings(prop);
    //设置共享异常信息的键
    exceptionResolver.setExceptionAttribute("ex");
    resolvers.add(exceptionResolver);
  }*/
  //配置生成模板解析器
  @Bean
  public ITemplateResolver templateResolver() {
    WebApplicationContext webApplicationContext =  ContextLoader.getCurrentWebApplicationContext();
    // ServletContextTemplateResolver需要一个ServletContext作为构造参数，可通过  WebApplicationContext 的方法获得
    ServletContextTemplateResolver templateResolver = new  ServletContextTemplateResolver(  webApplicationContext.getServletContext());
    templateResolver.setPrefix("/WEB-INF/templates/");
    templateResolver.setSuffix(".html");
    templateResolver.setCharacterEncoding("UTF-8");
    templateResolver.setTemplateMode(TemplateMode.HTML);
    return templateResolver;
  }
  //生成模板引擎并为模板引擎注入模板解析器
  @Bean
  public SpringTemplateEngine templateEngine(ITemplateResolver   templateResolver) {
    SpringTemplateEngine templateEngine = new SpringTemplateEngine();
    templateEngine.setTemplateResolver(templateResolver);
    return templateEngine;
  }
  //生成视图解析器并未解析器注入模板引擎
  @Bean
  public ViewResolver viewResolver(SpringTemplateEngine templateEngine) {
    ThymeleafViewResolver viewResolver = new ThymeleafViewResolver();
    viewResolver.setCharacterEncoding("UTF-8");
    viewResolver.setTemplateEngine(templateEngine);
    return viewResolver;
  }
}
```

# SpringMVC 的执行流程

1. 用户向服务器发送请求，请求被 SpringMVC 前端控制器 DispatcherServlet 捕获。
2. DispatcherServlet 对请求 URL 进行解析，得到请求资源标识符（URI），判断请求 URI 对应的映射：
   a) 不存在
   i. 再判断是否配置了 mvc:default-servlet-handler
   ii. 如果没配置，则控制台报映射查找不到，客户端展示 404 错误
   iii. 如果有配置，则访问目标资源（一般为静态资源，如：JS,CSS,HTML），找不到客户端也会展示 404
   错误
   b) 存在则执行下面的流程

3) 根据该 URI，调用 HandlerMapping 获得该 Handler 配置的所有相关的对象（包括 Handler 对象以及
   Handler 对象对应的拦截器），最后以 HandlerExecutionChain 执行链对象的形式返回。
4) DispatcherServlet 根据获得的 Handler，选择一个合适的 HandlerAdapter。
5) 如果成功获得 HandlerAdapter，此时将开始执行拦截器的 preHandler(…)方法【正向】
6) 提取 Request 中的模型数据，填充 Handler 入参，开始执行 Handler（Controller)方法，处理请求。
   在填充 Handler 的入参过程中，根据你的配置，Spring 将帮你做一些额外的工作：
   a) HttpMessageConveter： 将请求消息（如 Json、xml 等数据）转换成一个对象，将对象转换为指定
   的响应信息
   b) 数据转换：对请求消息进行数据转换。如 String 转换成 Integer、Double 等
   c) 数据格式化：对请求消息进行数据格式化。 如将字符串转换成格式化数字或格式化日期等
   d) 数据验证： 验证数据的有效性（长度、格式等），验证结果存储到 BindingResult 或 Error 中
7) Handler 执行完成后，向 DispatcherServlet 返回一个 ModelAndView 对象。
8) 此时将开始执行拦截器的 postHandle(...)方法【逆向】。
9) 根据返回的 ModelAndView（此时会判断是否存在异常：如果存在异常，则执行
   HandlerExceptionResolver 进行异常处理）选择一个适合的 ViewResolver 进行视图解析，根据 Model
   和 View，来渲染视图。
10) 渲染视图完毕执行拦截器的 afterCompletion(…)方法【逆向】。
11) 将渲染结果返回给客户端。
