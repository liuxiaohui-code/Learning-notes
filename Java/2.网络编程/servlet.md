# Java EE

什么是 JavaEE？JavaEE 是 Java Platform Enterprise Edition 的缩写，即 Java 企业平台。我们前面介绍的所有基于标准 JDK 的开发都是 JavaSE，即 Java Platform Standard Edition。此外，还有一个小众不太常用的 JavaME：Java Platform Micro Edition，是 Java 移动开发平台（非 Android）

```
┌────────────────┐
│     JavaEE     │
│┌──────────────┐│
││    JavaSE    ││
││┌────────────┐││
│││   JavaME   │││
││└────────────┘││
│└──────────────┘│
└────────────────┘
```

javaME 是一个裁剪后的“微型版”JDK，现在使用很少，我们不用管它。
JavaEE 基于 JavaSE，只是多了一大堆服务器相关的库以及 API 接口。

JavaEE 最核心的组件就是基于 Servlet 标准的 Web 服务器，开发者编写的应用程序是基于 Servlet API 并运行在 Web 服务器内部的：

```
┌─────────────┐
│┌───────────┐│
││ User App  ││
│├───────────┤│
││Servlet API││
│└───────────┘│
│ Web Server  │
├─────────────┤
│   JavaSE    │
└─────────────┘
```

此外，JavaEE 还有一系列技术标准：

EJB：Enterprise JavaBean，企业级 JavaBean，早期经常用于实现应用程序的业务逻辑，现在基本被轻量级框架如 Spring 所取代；
JAAS：Java Authentication and Authorization Service，一个标准的认证和授权服务，常用于企业内部，Web 程序通常使用更轻量级的自定义认证；
JCA：JavaEE Connector Architecture，用于连接企业内部的 EIS 系统等；
JMS：Java Message Service，用于消息服务；
JTA：Java Transaction API，用于分布式事务；
JAX-WS：Java API for XML Web Services，用于构建基于 XML 的 Web 服务；
…
目前流行的基于 Spring 的轻量级 JavaEE 开发架构，使用最广泛的是 Servlet 和 JMS，以及一系列开源组件。

# Web 基础

1. 基于 B/S 模式,浏览器/服务器模式
2. 浏览器和服务器之间的传输协议是 HTTP

# 编写 HTTP Server

一个 HTTP Server 本质上是一个 TCP 服务器

```java
public class Server {
    public static void main(String[] args) throws IOException {
        ServerSocket ss = new ServerSocket(8080); // 监听指定端口
        System.out.println("server is running...");
        for (;;) {
            Socket sock = ss.accept();
            System.out.println("connected from " + sock.getRemoteSocketAddress());
            Thread t = new Handler(sock);
            t.start();
        }
    }
}
class Handler extends Thread {
    Socket sock;
    public Handler(Socket sock) {
        this.sock = sock;
    }
    public void run() {
        try (InputStream input = this.sock.getInputStream()) {
            try (OutputStream output = this.sock.getOutputStream()) {
                handle(input, output);
            }
        } catch (Exception e) {
            try {
                this.sock.close();
            } catch (IOException ioe) {
            }
            System.out.println("client disconnected.");
        }
    }
    // 只需要在handle()方法中，用Reader读取HTTP请求，用Writer发送HTTP响应，即可实现一个最简单的HTTP服务器。
    private void handle(InputStream input, OutputStream output) throws IOException {
    System.out.println("Process new http request...");
    var reader = new BufferedReader(new InputStreamReader(input, StandardCharsets.UTF_8));
    var writer = new BufferedWriter(new OutputStreamWriter(output, StandardCharsets.UTF_8));
    // 读取HTTP请求:
    boolean requestOk = false;
    String first = reader.readLine();
    if (first.startsWith("GET / HTTP/1.")) {
        requestOk = true;
    }
    for (;;) {
        String header = reader.readLine();
        if (header.isEmpty()) { // 读取到空行时, HTTP Header读取完毕
            break;
        }
        System.out.println(header);
    }
    System.out.println(requestOk ? "Response OK" : "Response Error");
    if (!requestOk) {
        // 发送错误响应:
        writer.write("404 Not Found\r\n");
        writer.write("Content-Length: 0\r\n");
        writer.write("\r\n");
        writer.flush();
    } else {
        // 发送成功响应:
        String data = "<html><body><h1>Hello, world!</h1></body></html>";
        int length = data.getBytes(StandardCharsets.UTF_8).length;
        writer.write("HTTP/1.0 200 OK\r\n");
        writer.write("Connection: close\r\n");
        writer.write("Content-Type: text/html\r\n");
        writer.write("Content-Length: " + length + "\r\n");
        writer.write("\r\n"); // 空行标识Header和Body的分隔
        writer.write(data);
        writer.flush();
    }
}
}
```

# Servlet 开发

1. 基于 http 协议,一般通过 tcp 通讯,使用着 http 报文格式
2. 一个 Servlet 类在服务器中只有一个实例，但对于每个 HTTP 请求，Web 服务器会使用多线程执行请求。

要编写一个完善的 HTTP 服务器，以 HTTP/1.1 为例，需要考虑的包括：

识别正确和错误的 HTTP 请求；
识别正确和错误的 HTTP 头；
复用 TCP 连接；
复用线程；
IO 异常处理；
…
这些基础工作需要耗费大量的时间，并且经过长期测试才能稳定运行。如果我们只需要输出一个简单的 HTML 页面，就不得不编写上千行底层代码，那就根本无法做到高效而可靠地开发。

因此，在 JavaEE 平台上，处理 TCP 连接，解析 HTTP 协议这些底层工作统统扔给现成的 Web 服务器去做，我们只需要把自己的应用程序跑在 Web 服务器上。为了实现这一目的，JavaEE 提供了 Servlet API，我们使用 Servlet API 编写自己的 Servlet 来处理 HTTP 请求，Web 服务器实现 Servlet API 接口，实现底层功能：

```
                 ┌───────────┐
                 │My Servlet │
                 ├───────────┤
                 │Servlet API│
┌───────┐  HTTP  ├───────────┤
│Browser│<──────>│Web Server │
└───────┘        └───────────┘
```

一个最简单的 Servlet:一个 Servlet 总是继承自 HttpServlet，然后覆写 doGet()或 doPost()方法。

```java
// WebServlet注解表示这是一个Servlet，并映射到地址/:
@WebServlet(urlPatterns = "/")
public class HelloServlet extends HttpServlet {
  //  HttpServletRequest和HttpServletResponse两个对象，分别代表HTTP请求和响应。
  // 我们使用Servlet API时，并不直接与底层TCP交互，也不需要解析HTTP协议
  protected void doGet(HttpServletRequest req, HttpServletResponse resp)throws ServletException, IOException {
    // 设置响应类型:
    resp.setContentType("text/html");
    // 获取输出流:
    PrintWriter pw = resp.getWriter();
    // 写入响应:
    pw.write("<h1>Hello, world!</h1>");
    // 最后不要忘记flush强制输出:
    pw.flush();
  }
}
```

一个 Servlet 类在服务器中只有一个实例，但对于每个 HTTP 请求，Web 服务器会使用多线程执行请求。因此，一个 Servlet 的 doGet()、doPost()等处理请求的方法是多线程并发执行的。如果 Servlet 中定义了字段，要注意多线程并发访问的问题：

```Java
public class HelloServlet extends HttpServlet {
    private Map<String, String> map = new ConcurrentHashMap<>();
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 注意读写map字段是多线程并发的:
        this.map.put(key, value);
    }
}
```

# 依赖与运行环境

## 依赖

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.itranswarp.learnjava</groupId>
    <artifactId>web-servlet-hello</artifactId>
    <!-- 打包类型不是jar，而是war，表示Java Web Application Archive -->
    <packaging>war</packaging>
    <version>1.0-SNAPSHOT</version>
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
        <java.version>11</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>4.0.0</version>
            <!-- 注意到<scope>指定为provided，表示编译时使用，但不会打包到.war文件中，因为运行期Web服务器本身已经提供了Servlet API相关的jar包。 -->
            <scope>provided</scope>
        </dependency>
    </dependencies>
    <build>
        <finalName>hello</finalName>
    </build>
</project>
```

## 项目结构

```
web-servlet-hello
├── pom.xml
└── src
    └── main
        ├── java
        │   └── com
        │       └── itranswarp
        │           └── learnjava
        │               └── servlet
        │                   └── HelloServlet.java
        ├── resources
        └── webapp
            └── WEB-INF
                └── web.xml
```

## web.xml

在工程目录下创建一个 web.xml 描述文件，放到 src/main/webapp/WEB-INF 目录下（固定目录结构，不要修改路径，注意大小写）。文件内容可以固定如下：

```xml
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd">
<web-app>
  <display-name>Archetype Created Web Application</display-name>
</web-app>

<!-- 不使用注解 -->
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
                      http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1"
         metadata-complete="true">

    <servlet>
        <servlet-name>AddServlet</servlet-name> <!--3. 找到servlet-->
        <servlet-class>com.lxh.servlets.AddServlet</servlet-class> <!--4. 调用相应的类方法;get-doGet() post-doPost() ...-->
    </servlet>
    <servlet-mapping>
        <!-- 可以使用 @WebServlet("/add") 注解替换 -->
        <servlet-name>AddServlet</servlet-name> <!--2. 寻找servlet-->
        <url-pattern>/add</url-pattern> <!--1. 访问路径-->
    </servlet-mapping>
</web-app>
```

## 编译运行

运行 Maven 命令 `mvn clean package`，在 target 目录下得到一个 `hello.war` 文件，这个文件就是我们编译打包后的 Web 应用程序。

普通的 Java 程序是通过启动 JVM，然后执行 main()方法开始运行。但是 Web 应用程序有所不同，我们无法直接运行 war 文件，必须先启动 Web 服务器，再由 Web 服务器加载我们编写的 HelloServlet，这样就可以让 HelloServlet 处理浏览器发送的请求。

因此，我们首先要找一个支持 Servlet API 的 Web 服务器。常用的服务器有：

Tomcat：由 Apache 开发的开源免费服务器；
Jetty：由 Eclipse 开发的开源免费服务器；
GlassFish：一个开源的全功能 JavaEE 服务器。
还有一些收费的商用服务器，如 Oracle 的 WebLogic，IBM 的 WebSphere。

无论使用哪个服务器，只要它支持 Servlet API 4.0（因为我们引入的 Servlet 版本是 4.0），我们的 war 包都可以在上面运行。

tomcat 运行: `把hello.war复制到Tomcat的webapps目录下，然后切换到bin目录，执行startup.sh或startup.bat启动Tomcat服务器`,在浏览器输入 http://localhost:8080/hello/即可看到 HelloServlet 的输出.

细心的童鞋可能会问，为啥路径是/hello/而不是/？因为一个 Web 服务器允许同时运行多个 Web App，而我们的 Web App 叫 hello，因此，第一级目录/hello 表示 Web App 的名字，后面的/才是我们在 HelloServlet 中映射的路径。

**那能不能直接使用/而不是/hello/？**

答案是肯定的。先关闭 Tomcat（执行 shutdown.sh 或 shutdown.bat），然后删除 Tomcat 的 webapps 目录下的所有文件夹和文件，最后把我们的 hello.war 复制过来，改名为 ROOT.war，文件名为 ROOT 的应用程序将作为默认应用，启动后直接访问 http://localhost:8080/即可。

实际上，类似 Tomcat 这样的服务器也是 Java 编写的，启动 Tomcat 服务器实际上是启动 Java 虚拟机，执行 Tomcat 的 main()方法，然后由 Tomcat 负责加载我们的.war 文件，并创建一个 HelloServlet 实例，最后以多线程的模式来处理 HTTP 请求。如果 Tomcat 服务器收到的请求路径是/（假定部署文件为 ROOT.war），就转发到 HelloServlet 并传入 HttpServletRequest 和 HttpServletResponse 两个对象。

因为我们编写的 Servlet 并不是直接运行，而是由 Web 服务器加载后创建实例运行，所以，类似 Tomcat 这样的 Web 服务器也称为 **Servlet 容器**。

在 Servlet 容器中运行的 Servlet 具有如下特点：

1. 无法在代码中直接通过 new 创建 Servlet 实例，必须由 Servlet 容器自动创建 Servlet 实例；
2. Servlet 容器只会给每个 Servlet 类创建唯一实例；
3. Servlet 容器会使用多线程执行 doGet()或 doPost()方法。

# Java 启动 tomcat 调试

1. 编写 Servlet；
2. 打包为 war 文件；
3. 复制到 Tomcat 的 webapps 目录下；
4. 启动 Tomcat。

**这个过程是不是很繁琐？如果我们想在 IDE 中断点调试，还需要打开 Tomcat 的远程调试端口并且连接上去。**

因为 Tomcat 实际上也是一个 Java 程序，我们看看 Tomcat 的启动流程：

1. 启动 JVM 并执行 Tomcat 的 main()方法；
2. 加载 war 并初始化 Servlet；
3. 正常服务。 启动 Tomcat 无非就是设置好 classpath 并执行 Tomcat 某个 jar 包的 main()方法，我们完全可以把 Tomcat 的 jar 包全部引入进来，然后自己编写一个 main()方法，先启动 Tomcat，然后让它加载我们的 webapp 就行。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.itranswarp.learnjava</groupId>
    <artifactId>web-servlet-embedded</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>war</packaging>
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
        <java.version>11</java.version>
        <tomcat.version>9.0.26</tomcat.version>
    </properties>
    <dependencies>
        <!-- 不必引入Servlet API，因为引入Tomcat依赖后自动引入了Servlet API。 -->
        <dependency>
            <groupId>org.apache.tomcat.embed</groupId>
            <artifactId>tomcat-embed-core</artifactId>
            <version>${tomcat.version}</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.tomcat.embed</groupId>
            <artifactId>tomcat-embed-jasper</artifactId>
            <version>${tomcat.version}</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
</project>
```

启动 Tomcat 服务器,这样，我们直接运行 main()方法，即可启动嵌入式 Tomcat 服务器，然后，通过预设的 tomcat.addWebapp("", new File("src/main/webapp")，Tomcat 会自动加载当前工程作为根 webapp，可直接在浏览器访问 http://localhost:8080/

```java
public class Main {
    public static void main(String[] args) throws Exception {
        // 启动Tomcat:
        Tomcat tomcat = new Tomcat();
        tomcat.setPort(Integer.getInteger("port", 8080));
        tomcat.getConnector();
        // 创建webapp:
        Context ctx = tomcat.addWebapp("", new File("src/main/webapp").getAbsolutePath());
        WebResourceRoot resources = new StandardRoot(ctx);
        resources.addPreResources(
                new DirResourceSet(resources, "/WEB-INF/classes", new File("target/classes").getAbsolutePath(), "/"));
        ctx.setResources(resources);
        tomcat.start();
        tomcat.getServer().await();
    }
}
```

通过 main()方法启动 Tomcat 服务器并加载我们自己的 webapp 有如下好处：

1. 启动简单，无需下载 Tomcat 或安装任何 IDE 插件；
2. 调试方便，可在 IDE 中使用断点调试；
3. 使用 Maven 创建 war 包后，也可以正常部署到独立的 Tomcat 服务器中。

# 生命周期

1. 初始化 `init() `仅在创建 Servlet 时调用一次,
2. 处理客户端请求,并返回结果 `service()`; 由容器自动调用,识别请求方式,分配给响应的 doGet \ doPost 等方法处理
3. 终止 `destory()`;只会调用一次,在 Servlet 生命周期结束时被调用。destroy() 方法可以让您的 Servlet 关闭数据库连接、停止后台线程、把 Cookie 列表或点击计数器写入到磁盘，并执行其他类似的清理活动。在调用 destroy() 方法之后，servlet 对象被标记为垃圾回收。

# 路径映射

一个 Web App 就是由一个或多个 Servlet 组成的，每个 Servlet 通过注解说明自己能处理的路径。

```java
@WebServlet(urlPatterns = "/hello")
public class HelloServlet extends HttpServlet {
    @Override //要处理GET请求，我们要覆写doGet()
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ...
    }
    // 类似的，要处理POST请求，就需要覆写doPost()方法。
    // 因此，一个Servlet如果映射到/hello，那么所有请求方法都会由这个Servlet处理，至于能不能返回200成功响应，要看有没有覆写对应的请求方法。
}
```

一个 Webapp 完全可以有多个 Servlet，分别映射不同的路径

```java
@WebServlet(urlPatterns = "/hello")
public class HelloServlet extends HttpServlet {
    ...
}
@WebServlet(urlPatterns = "/signin")
public class SignInServlet extends HttpServlet {
    ...
}
@WebServlet(urlPatterns = "/")
public class IndexServlet extends HttpServlet {
    ...
}
```

浏览器发出的 HTTP 请求总是由 Web Server 先接收，然后，根据 Servlet 配置的映射，不同的路径转发到不同的 Servlet：

```
               ┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┐
               │            /hello    ┌───────────────┐│
                          ┌──────────>│ HelloServlet  │
               │          │           └───────────────┘│
┌───────┐    ┌──────────┐ │ /signin   ┌───────────────┐
│Browser│───>│Dispatcher│─┼──────────>│ SignInServlet ││
└───────┘    └──────────┘ │           └───────────────┘
               │          │ /         ┌───────────────┐│
                          └──────────>│ IndexServlet  │
               │                      └───────────────┘│
                              Web Server
               └ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┘
```

这种根据路径转发的功能我们一般称为 Dispatch。映射到/的 IndexServlet 比较特殊，它实际上会接收所有未匹配的路径，相当于`/*`

Dispatcher 的逻辑可以用伪代码实现如下：

```java
String path = ...
if (path.equals("/hello")) {
    dispatchTo(helloServlet);
} else if (path.equals("/signin")) {
    dispatchTo(signinServlet);
} else {
    // 所有未匹配的路径均转发到"/"
    dispatchTo(indexServlet);
}
```

## 重定向

重定向是指当浏览器请求一个 URL 时，服务器返回一个重定向指令，告诉浏览器地址已经变了，麻烦使用新的 URL 再重新发送新请求。

例如，我们已经编写了一个能处理/hello 的 HelloServlet，如果收到的路径为/hi，希望能重定向到/hello，可以再编写一个 RedirectServlet：

```java
@WebServlet(urlPatterns = "/hi")
public class RedirectServlet extends HttpServlet {
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 构造重定向的路径:
        String name = req.getParameter("name");
        String redirectToUrl = "/hello" + (name == null ? "" : "?name=" + name);
        // 发送重定向响应:
        resp.sendRedirect(redirectToUrl);
      /*
      浏览器会收到如下响应:
        HTTP/1.1 302 Found
        Location: /hello
      当浏览器收到 302 响应后，它会立刻根据 Location 的指示发送一个新的 GET /hello 请求，这个过程就是重定向,并且浏览器的地址栏路径自动更新为/hello
      */
    }
}
```

重定向有两种：一种是 302 响应，称为**临时重定向**，一种是 301 响应，称为**永久重定向**。两者的区别是，如果服务器发送 301 永久重定向响应，浏览器会缓存/hi 到/hello 这个重定向的关联，下次请求/hi 的时候，浏览器就直接发送/hello 请求了。

重定向有什么作用？重定向的目的是当 Web 应用升级后，如果请求路径发生了变化，可以将原来的路径重定向到新路径，从而避免浏览器请求原路径找不到资源。

HttpServletResponse 提供了快捷的 redirect()方法实现 302 重定向。如果要实现 301 永久重定向，可以这么写：

```Java
resp.setStatus(HttpServletResponse.SC_MOVED_PERMANENTLY); // 301
resp.setHeader("Location", "/hello");
```

## 转发

Forward 是指内部转发。当一个 Servlet 处理请求的时候，它可以决定自己不继续处理，而是转发给另一个 Servlet 处理。

```java
@WebServlet(urlPatterns = "/morning")
public class ForwardServlet extends HttpServlet {
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
      // ForwardServlet在收到请求后，它并不自己发送响应，而是把请求和响应都转发给路径为/hello的Servlet，
      req.getRequestDispatcher("/hello").forward(req, resp);
    }
}
```

# HttpServletRequest

HttpServletRequest 封装了一个 HTTP 请求，它实际上是从 ServletRequest 继承而来。最早设计 Servlet 时，设计者希望 Servlet 不仅能处理 HTTP，也能处理类似 SMTP 等其他协议，因此，单独抽出了 ServletRequest 接口，但实际上除了 HTTP 外，并没有其他协议会用 Servlet 处理，所以这是一个过度设计。

头信息 描述
`Accept` 这个头信息指定浏览器或其他客户端可以处理的 MIME 类型。值 image/png 或 image/jpeg 是最常见的两种可能值。
`Accept-Charset` 这个头信息指定浏览器可以用来显示信息的字符集。例如 ISO-8859-1。
`Accept-Encoding` 这个头信息指定浏览器知道如何处理的编码类型。值 gzip 或 compress 是最常见的两种可能值。
`Accept-Language` 这个头信息指定客户端的首选语言，在这种情况下，Servlet 会产生多种语言的结果。例如，en、en-us、ru 等。
`Authorization` 这个头信息用于客户端在访问受密码保护的网页时识别自己的身份。
`Connection` 这个头信息指示客户端是否可以处理持久 HTTP 连接。持久连接允许客户端或其他浏览器通过单个请求来检索多个文件。值 Keep-Alive 意味着使用了持续连接。
`Content-Length` 这个头信息只适用于 POST 请求，并给出 POST 数据的大小（以字节为单位）。
`Cookie` 这个头信息把之前发送到浏览器的 cookies 返回到服务器。
`Host` 这个头信息指定原始的 URL 中的主机和端口。
`If-Modified-Since` 这个头信息表示只有当页面在指定的日期后已更改时，客户端想要的页面。如果没有新的结果可以使用，服务器会发送一个 304 代码，表示 Not Modified 头信息。
`If-Unmodified-Since` 这个头信息是 If-Modified-Since 的对立面，它指定只有当文档早于指定日期时，操作才会成功。
`Referer` 这个头信息指示所指向的 Web 页的 URL。例如，如果您在网页 1，点击一个链接到网页 2，当浏览器请求网页 2 时，网页 1 的 URL 就会包含在 Referer 头信息中。
`User-Agent` 这个头信息识别发出请求的浏览器或其他客户端，并可以向不同类型的浏览器返回不同的内容。
我们通过 HttpServletRequest 提供的接口方法可以拿到 HTTP 请求的几乎全部信息，常用的方法有：

```Java
// 参数
getParameter(name)：返回请求参数，GET请求从URL读取参数，POST请求从Body中读取参数。
getParameterValues()：如果参数出现一次以上，则调用该方法，并返回多个值，例如复选框。
getParameterNames()：如果您想要得到当前请求中的所有参数的完整列表，则调用该方法。
getQueryString()：返回请求参数，例如，"name=Bob&a=1&b=2"；
// 方法
getMethod()：返回请求方法，例如，"GET"，"POST"；
getScheme()：返回协议类型，例如，"http"，"https"；
// url
getRequestURI()：返回请求路径，但不包括请求参数，例如，"/hello"；
getRemoteAddr()：返回客户端的IP地址；
// ContentType
getContentType()：获取请求Body的类型，例如，"application/x-www-form-urlencoded"；
getContextPath()：获取当前Webapp挂载的路径，对于ROOT来说，总是返回空字符串""；
// headers
getHeader(name)：获取指定的Header，对Header名称不区分大小写；
getHeaderNames()：返回所有Header名称；
// post 方法请求体
getInputStream()：如果该请求带有HTTP Body，该方法将打开一个输入流用于读取Body；
getReader()：和getInputStream()类似，但打开的是Reader；
```

此外，HttpServletRequest 还有两个方法：`setAttribute()`和`getAttribute()`，可以给当前`HttpServletRequest`对象附加多个`Key-Value`，相当于把`HttpServletRequest`当作一个`Map<String, Object>`使用。

# HttpServletResponse

`HttpServletResponse` 封装了一个 HTTP 响应。由于 HTTP 响应必须先发送 Header，再发送 Body，所以，操作 `HttpServletResponse` 对象时，必须先调用设置 Header 的方法，最后调用发送 Body 的方法。

`HTTP/1.1 200 OK`
头信息 描述
`Allow` 这个头信息指定服务器支持的请求方法（GET、POST 等）。
`Cache-Control` 这个头信息指定响应文档在何种情况下可以安全地缓存。可能的值有：public、private 或 no-cache 等。Public 意味着文档是可缓存，Private 意味着文档是单个用户私用文档，且只能存储在私有（非共享）缓存中，no-cache 意味着文档不应被缓存。
`Connection` 这个头信息指示浏览器是否使用持久 HTTP 连接。值 close 指示浏览器不使用持久 HTTP 连接，值 keep-alive 意味着使用持久连接。
`Content-Disposition` 这个头信息可以让您请求浏览器要求用户以给定名称的文件把响应保存到磁盘。
`Content-Encoding` 在传输过程中，这个头信息指定页面的编码方式。
`Content-Language` 这个头信息表示文档编写所使用的语言。例如，en、en-us、ru 等。
`Content-Length` 这个头信息指示响应中的字节数。只有当浏览器使用持久（keep-alive）HTTP 连接时才需要这些信息。
`Content-Type` 这个头信息提供了响应文档的 MIME（Multipurpose Internet Mail Extension）类型。
`Expires` 这个头信息指定内容过期的时间，在这之后内容不再被缓存。
`Last-Modified` 这个头信息指示文档的最后修改时间。然后，客户端可以缓存文件，并在以后的请求中通过 If-Modified-Since 请求头信息提供一个日期。
`Location` 这个头信息应被包含在所有的带有状态码的响应中。在 300s 内，这会通知浏览器文档的地址。浏览器会自动重新连接到这个位置，并获取新的文档。
`Refresh` 这个头信息指定浏览器应该如何尽快请求更新的页面。您可以指定页面刷新的秒数。
`Retry-After` 这个头信息可以与 503（Service Unavailable 服务不可用）响应配合使用，这会告诉客户端多久就可以重复它的请求。
`Set-Cookie` 这个头信息指定一个与页面关联的 cookie。

常用的设置 `Header` 的方法有：

```Java
setStatus(sc)：设置响应代码，默认是 200；
setContentType(type)：设置 Body 的类型，例如，"text/html"；
setCharacterEncoding(charset)：设置字符编码，例如，"UTF-8"；
setHeader(name, value)：设置一个 Header 的值；
addHeader(name, value)：给响应添加一个 Header，因为 HTTP 协议允许有多个相同的 Header；
```

响应数据

```java
1. 写入响应时，需要通过getOutputStream()获取写入流，或者通过getWriter()获取字符流，二者只能获取其中一个。
2. 写入响应前，无需设置setContentLength()，因为底层服务器会根据写入的字节数自动设置，如果写入的数据量很小，实际上会先写入缓冲区，如果写入的数据量很大，服务器会自动采用Chunked编码让浏览器能识别数据结束符而不需要设置Content-Length头。
3. 但是，写入完毕后调用flush()却是必须的，因为大部分Web服务器都基于HTTP/1.1协议，会复用TCP连接。如果没有调用flush()，将导致缓冲区的内容无法及时发送到客户端。此外，写入完毕后千万不要调用close()，原因同样是因为会复用TCP连接，如果关闭写入流，将关闭TCP连接，使得Web服务器无法复用此TCP连接。

```

# 用户身份识别

因为 HTTP 协议是一个无状态协议，即 Web 应用程序无法区分收到的两个 HTTP 请求是否是同一个浏览器发出的。

1. header,存储自定义数据
2. session,基于唯一 ID 识别用户身份的机制称为 Session
3. cookie,为了跟踪用户状态，服务器可以向浏览器分配一个唯一 ID，并以 Cookie 的形式发送到浏览器，浏览器在后续访问时总是附带此 Cookie，这样，服务器就可以识别用户身份。

## session

1. 基于唯一 ID 识别用户身份的机制称为 Session。
2. 第一次访问服务器后，会自动获得一个 Session ID。
3. 过期时间: 如果用户在一段时间内没有访问服务器，那么 Session 会自动失效，下次即使带着上次分配的 Session ID 访问，服务器也认为这是一个新用户，会分配新的 Session ID。
4. servlet: HttpSession,Web 服务器在内存中自动维护了一个 ID 到 HttpSession 的映射表;
   1. 存储 session:`httpServletRequest.getSession().setAttribute("user", name);`把键值对存储到 session 中;
   2. 获取 session:`httpServletRequest.getSession().getAttribute("user");`在 session 的有效时间内,可以在不同的 servlet 中访问 session 中的值
   3. 删除 session:`httpServletRequest.getSession().removeAttribute("user");`移除 session 信息
   4. Session 有默认的存活时间(30 分钟)
5. 服务器识别 Session 的关键就是依靠一个名为 JSESSIONID 的 Cookie。在 Servlet 中第一次调用 req.getSession()时，Servlet 容器自动创建一个 Session ID，然后通过一个名为 JSESSIONID 的 Cookie 发送给浏览器：header:`Set-Cookie`;
   1. JSESSIONID 是由 Servlet 容器自动创建的，目的是维护一个浏览器会话，它和我们的登录逻辑没有关系；
   2. 登录和登出的业务逻辑是我们自己根据 HttpSession 是否存在一个"user"的 Key 判断的，登出后，Session ID 并不会改变；
   3. 即使没有登录功能，仍然可以使用 HttpSession 追踪用户，例如，放入一些用户配置信息等。
6. session 数据存储到内存中,如果遇到内存不足的情况，就需要把部分不活动的 Session 序列化到磁盘上,放入 Session 的对象要小
7. 反向代理时,1 浏览器多服务器;如果多台 Web Server 采用无状态集群，那么反向代理总是以轮询方式将请求依次转发给每台 Web Server，这会造成一个用户在 Web Server 1 存储的 Session 信息，在 Web Server 2 和 3 上并不存在，即从 Web Server 1 登录后，如果后续请求被转发到 Web Server 2 或 3，那么用户看到的仍然是未登录状态。
   1. 方案一:在所有 Web Server 之间进行 Session 复制，但这样会严重消耗网络带宽，并且，每个 Web Server 的内存均存储所有用户的 Session，内存使用率很低。
   2. 采用粘滞会话（Sticky Session）机制，即反向代理在转发请求的时候，总是根据 JSESSIONID 的值判断，相同的 JSESSIONID 总是转发到固定的 Web Server，但这需要反向代理的支持。
8. 使用 Session 机制，会使得 Web Server 的集群很难扩展，因此，Session 适用于中小型 Web 应用程序。对于大型 Web 应用程序来说，通常需要避免使用 Session 机制。
9. 很多浏览器不支持 cookie,可以使用 url 重写设置 session:`http://w3cschool.cc/file.htm;sessionid=12345`,session 会话标识符被附加为 sessionid=12345，标识符可被 Web 服务器访问以识别客户端。

```java
HttpSession session = request.getSession();

1	public Object getAttribute(String name)//该方法返回在该 session 会话中具有指定名称的对象，如果没有指定名称的对象，则返回 null。
2	public Enumeration getAttributeNames()//方法返回 String 对象的枚举，String 对象包含所有绑定到该 session 会话的对象的名称。
3	public long getCreationTime()//该方法返回该 session 会话被创建的时间，自格林尼治标准时间 1970 年 1 月 1 日午夜算起，以毫秒为单位。
4	public String getId()//该方法返回一个包含分配给该 session 会话的唯一标识符的字符串。
5	public long getLastAccessedTime()//该方法返回客户端最后一次发送与该 session 会话相关的请求的时间自格林尼治标准时间 1970 年 1 月 1 日午夜算起，以毫秒为单位。
6	public int getMaxInactiveInterval()//该方法返回 Servlet 容器在客户端访问时保持 session 会话打开的最大时间间隔，以秒为单位。
7	public void invalidate()//该方法指示该 session 会话无效，并解除绑定到它上面的任何对象。
8	public boolean isNew()//如果客户端还不知道该 session 会话，或者如果客户选择不参入该 session 会话，则该方法返回 true。
9	public void removeAttribute(String name)//该方法将从该 session 会话移除指定名称的对象。
10	public void setAttribute(String name, Object value)//该方法使用指定的名称绑定一个对象到该 session 会话。
11	public void setMaxInactiveInterval(int interval)//该方法在 Servlet 容器指示该 session 会话无效之前，指定客户端请求之间的时间，以秒为单位。
```

## Cookie

实际上，Servlet 提供的 HttpSession 本质上就是通过一个名为 JSESSIONID 的 Cookie 来跟踪用户会话的。除了这个名称外，其他名称的 Cookie 我们可以任意使用。

1. servlet Cookie
   1. 创建 `Cookie cookie = new Cookie("lang", lang);`;cookie 存储键值对,请记住，无论是名字还是值，都不应该包含空格或以下任何字符：`[ ] ( ) = , " / ? @ : ;`
   2. 设置生效的路径前缀:`cookie.setPath("/");`
   3. 设置有效期:`cookie.setMaxAge(8640000); // 8640000秒=100天`
   4. 将该 Cookie 添加到响应:`resp.addCookie(cookie)`,浏览器将这些信息存储在本地计算机上，以备将来使用。
   5. https `setSecure(true)`
   6. 读取:`Cookie[] cookies = req.getCookies();if (cookie.getName().equals("lang")) {return cookie.getValue();}`
   7. 删除:1. 遍历获取一个 cookie 2.设置有效期为 0 3.添加到响应

# 过滤器实现

1. 过滤器在每个 servlet 前后进行执行
2. 实现 `javax.servlet.Filter` 接口;
3. 配置文件或使用注解`@WebFilter`
4. 多个过滤器执行顺序为 `filter-mapping` 书写顺序

```xml
    <filter> <!-- 指定一个过滤器 -->
        <filter-name>test</filter-name> <!-- 为过滤器指定一个名字，该元素的内容不能为空 -->
        <filter-class>com.lxh.filters.MyFilter</filter-class> <!-- 指定过滤器的完整的限定类名 -->
        <init-param><!-- 指定初始化参数 使用FilterConfig接口对象来访问初始化参数。-->
            <param-name>Site</param-name><!-- 指定参数的名字 -->
            <param-value>xxx</param-value><!-- 指定参数的值 -->
        </init-param>
    </filter>
    <filter-mapping><!-- 设置一个 Filter 所负责拦截的资源 -->
        <filter-name>test</filter-name><!-- 设置filter的注册名称 -->
        <url-pattern>/*</url-pattern><!-- 设置 filter 所拦截的请求路径 -->
        <!--
            <servlet-name>指定过滤器所拦截的Servlet名称。
            <dispatcher>指定过滤器所拦截的资源被 Servlet 容器调用的方式，
        -->
    </filter-mapping>
```

```java
// 我们编写一个最简单的EncodingFilter，它强制把输入和输出的编码设置为UTF-8
@WebFilter(urlPatterns = "/*")
public class EncodingFilter implements Filter {
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        System.out.println("EncodingFilter:doFilter");
        request.setCharacterEncoding("UTF-8");
        response.setCharacterEncoding("UTF-8");
        // 继续处理注解
        chain.doFilter(request, response);
    }
}
@WebFilter("/*")
public class LogFilter implements Filter {
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        System.out.println("LogFilter: process " + ((HttpServletRequest) request).getRequestURI());
        chain.doFilter(request, response);
    }
}
// 注意到AuthFilter只过滤以/user/开头的路径
@WebFilter("/user/*")
public class AuthFilter implements Filter {
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        System.out.println("AuthFilter: check authentication");
        HttpServletRequest req = (HttpServletRequest) request;
        HttpServletResponse resp = (HttpServletResponse) response;
        if (req.getSession().getAttribute("user") == null) {
            // 未登录，自动跳转到登录页:
            System.out.println("AuthFilter: not signin!");
            resp.sendRedirect("/signin");
        } else {
            // 已登录，继续处理:
            chain.doFilter(request, response);
        }
    }
}
// 如果一个Filter在当前请求中生效，但什么都没有做,那么，用户将看到一个空白页，因为请求没有继续处理，默认响应是200+空白输出。
// 如果Filter要使请求继续被处理，就一定要调用chain.doFilter()！
@WebFilter("/*")
public class MyFilter implements Filter {
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        // TODO
    }
}
```

# Listener 监听器

除了 Servlet 和 Filter 外，JavaEE 的 Servlet 规范还提供了第三种组件：Listener。

1. 任何标注为`@WebListener`，且实现了特定接口的类会被 Web 服务器自动初始化。
2. `ServletContextListener` 在整个 Web 应用程序初始化完成后，以及 Web 应用程序关闭后获得回调通知
3. `HttpSessionListener` ：监听 HttpSession 的创建和销毁事件；
4. `ServletRequestListener` ：监听 ServletRequest 请求的创建和销毁事件；
5. `ServletRequestAttributeListener` ：监听 ServletRequest 请求的属性变化事件（即调用 ServletRequest.setAttribute()方法）；
6. `ServletContextAttributeListener` ：监听 ServletContext 的属性变化事件（即调用 ServletContext.setAttribute()方法）；
7. 一个 Web 服务器可以运行一个或多个 WebApp，对于每个 WebApp，Web 服务器都会为其创建一个全局唯一的 ServletContext 实例，
   1. 我们在 AppListener 里面编写的两个回调方法实际上对应的就是 ServletContext 实例的创建和销毁;
   2. ServletRequest、HttpSession 等很多对象也提供 getServletContext()方法获取到同一个 ServletContext 实例。ServletContext 实例最大的作用就是设置和共享全局信息。
   3. 此外，ServletContext 还提供了动态添加 Servlet、Filter、Listener 等功能，它允许应用程序在运行期间动态添加一个组件，虽然这个功能不是很常用。

```java
@WebListener
// ServletContextListener 在整个Web应用程序初始化完成后，以及Web应用程序关闭后获得回调通知。
public class AppListener implements ServletContextListener {
    // 在此初始化WebApp,例如打开数据库连接池等:
    public void contextInitialized(ServletContextEvent sce) {
        System.out.println("WebApp initialized.");
    }
    // 在此清理WebApp,例如关闭数据库连接池等:
    public void contextDestroyed(ServletContextEvent sce) {
        System.out.println("WebApp destroyed.");
    }
}
```

# 静态文件

我们把所有的静态资源文件放入/static/目录，在开发阶段，有些 Web 服务器会自动为我们加一个专门负责处理静态文件的 Servlet，但如果 IndexServlet 映射路径为/，会屏蔽掉处理静态文件的 Servlet 映射。因此，我们需要自己编写一个处理静态文件的 FileServlet：

```java
@WebServlet(urlPatterns = "/static/*")
public class FileServlet extends HttpServlet {
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletContext ctx = req.getServletContext();
        // RequestURI包含ContextPath,需要去掉:
        String urlPath = req.getRequestURI().substring(ctx.getContextPath().length());
        // 获取真实文件路径:
        String filepath = ctx.getRealPath(urlPath);
        if (filepath == null) {
            // 无法获取到路径:
            resp.sendError(HttpServletResponse.SC_NOT_FOUND);
            return;
        }
        Path path = Paths.get(filepath);
        if (!path.toFile().isFile()) {
            // 文件不存在:
            resp.sendError(HttpServletResponse.SC_NOT_FOUND);
            return;
        }
        // 根据文件名猜测Content-Type:
        String mime = Files.probeContentType(path);
        if (mime == null) {
            mime = "application/octet-stream";
        }
        resp.setContentType(mime);
        // 读取文件并写入Response:
        OutputStream output = resp.getOutputStream();
        try (InputStream input = new BufferedInputStream(new FileInputStream(filepath))) {
            input.transferTo(output);
        }
        output.flush();
    }
}
```

# 文件上传

对于文件上传，浏览器在上传的过程中是将文件以流的形式提交到服务器端的，如果直接使用 Servlet 获取上传文件的输入流然后再解析里面的请求参数是比较麻烦，所以一般选择采用 apache 的开源工具 common-fileupload 这个文件上传组件。这个 common-fileupload 上传组件的 jar 包可以去 apache 官网上面下载，也可以在 struts 的 lib 文件夹下面找到，struts 上传的功能就是基于这个实现的。common-fileupload 是依赖于 common-io 这个包的，所以还需要下载这个包。
需要引入的 jar 文件：commons-fileupload-1.3.2、commons-io-2.5.jar。

```java
    private static final long serialVersionUID = 1L;

    // 上传文件存储目录
    private static final String UPLOAD_DIRECTORY = "upload";

    // 上传配置
    private static final int MEMORY_THRESHOLD   = 1024 * 1024 * 3;  // 3MB
    private static final int MAX_FILE_SIZE      = 1024 * 1024 * 40; // 40MB
    private static final int MAX_REQUEST_SIZE   = 1024 * 1024 * 50; // 50MB
    /**
     * 上传数据及保存文件
     */
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // 检测是否为多媒体上传
        if (!ServletFileUpload.isMultipartContent(request)) {
            // 如果不是则停止
            PrintWriter writer = response.getWriter();
            writer.println("Error: 表单必须包含 enctype=multipart/form-data");
            writer.flush();
            return;
        }

        // 配置上传参数
        DiskFileItemFactory factory = new DiskFileItemFactory();
        // 设置内存临界值 - 超过后将产生临时文件并存储于临时目录中
        factory.setSizeThreshold(MEMORY_THRESHOLD);
        // 设置临时存储目录
        factory.setRepository(new File(System.getProperty("java.io.tmpdir")));

        ServletFileUpload upload = new ServletFileUpload(factory);

        // 设置最大文件上传值
        upload.setFileSizeMax(MAX_FILE_SIZE);

        // 设置最大请求值 (包含文件和表单数据)
        upload.setSizeMax(MAX_REQUEST_SIZE);

        // 中文处理
        upload.setHeaderEncoding("UTF-8");

        // 构造临时路径来存储上传的文件
        // 这个路径相对当前应用的目录
        String uploadPath = request.getServletContext().getRealPath("./") + File.separator + UPLOAD_DIRECTORY;


        // 如果目录不存在则创建
        File uploadDir = new File(uploadPath);
        if (!uploadDir.exists()) {
            uploadDir.mkdir();
        }

        try {
            // 解析请求的内容提取文件数据
            @SuppressWarnings("unchecked")
            List<FileItem> formItems = upload.parseRequest(request);

            if (formItems != null && formItems.size() > 0) {
                // 迭代表单数据
                for (FileItem item : formItems) {
                    // 处理不在表单中的字段
                    if (!item.isFormField()) {
                        String fileName = new File(item.getName()).getName();
                        String filePath = uploadPath + File.separator + fileName;
                        File storeFile = new File(filePath);
                        // 在控制台输出文件的上传路径
                        System.out.println(filePath);
                        // 保存文件到硬盘
                        item.write(storeFile);
                        request.setAttribute("message",
                            "文件上传成功!");
                    }
                }
            }
        } catch (Exception ex) {
            request.setAttribute("message",
                    "错误信息: " + ex.getMessage());
        }
        // 跳转到 message.jsp
        request.getServletContext().getRequestDispatcher("/message.jsp").forward(
                request, response);
    }
```

# 定时刷新

`public void setIntHeader(String header, int headerValue)`
此方法把头信息 "Refresh" 连同一个表示时间间隔的整数值（以秒为单位）发送回浏览器。

# 异常处理

当一个 Servlet 抛出一个异常时，Web 容器在使用了 `exception-type` 元素的 `web.xml` 中搜索与抛出异常类型相匹配的配置。

您必须在 web.xml 中使用 `error-page` 元素来指定对特定异常 或 HTTP 状态码 作出相应的 Servlet 调用。

```xml
<!-- error-code 相关的错误页面 -->
<error-page>
    <error-code>404</error-code>
    <location>/ErrorHandler</location>
</error-page>
<!-- exception-type 相关的错误页面 -->
<error-page>
    <exception-type>javax.servlet.ServletException</exception-type >
    <location>/ErrorHandler</location>
</error-page>
<!-- 处理所有错误 -->
<error-page>
    <exception-type>java.lang.Throwable</exception-type >
    <location>/ErrorHandler</location>
</error-page>
```

序号 属性 & 描述
1 `javax.servlet.error.status_code`
该属性给出状态码，状态码可被存储，并在存储为 java.lang.Integer 数据类型后可被分析。
2 `javax.servlet.error.exception_type`
该属性给出异常类型的信息，异常类型可被存储，并在存储为 java.lang.Class 数据类型后可被分析。
3 `javax.servlet.error.message`
该属性给出确切错误消息的信息，信息可被存储，并在存储为 java.lang.String 数据类型后可被分析。
4 `javax.servlet.error.request_uri`
该属性给出有关 URL 调用 Servlet 的信息，信息可被存储，并在存储为 java.lang.String 数据类型后可被分析。
5 `javax.servlet.error.exception`
该属性给出异常产生的信息，信息可被存储，并在存储为 java.lang.Throwable 数据类型后可被分析。
6 `javax.servlet.error.servlet_name`
该属性给出 Servlet 的名称，名称可被存储，并在存储为 java.lang.String 数据类型后可被分析。

# RESTful 风格介绍

REST：Representational State Transfer，表现层资源状态转移。
① 资源
资源是一种看待服务器的方式，即，将服务器看作是由很多离散的资源组成。每个资源是服务器上一个可命名的抽象概念。因为资源是一个抽象的概念，所以它不仅仅能代表服务器文件系统中的一个文件、
数据库中的一张表等等具体的东西，可以将资源设计的要多抽象有多抽象，只要想象力允许而且客户端应用开发者能够理解。与面向对象设计类似，资源是以名词为核心来组织的，首先关注的是名词。一个
资源可以由一个或多个 URI 来标识。URI 既是资源的名称，也是资源在 Web 上的地址。对某个资源感兴趣的客户端应用，可以通过资源的 URI 与其进行交互。
② 资源的表述
资源的表述是一段对于资源在某个特定时刻的状态的描述。可以在客户端-服务器端之间转移（交换）。资源的表述可以有多种格式，例如 HTML/XML/JSON/纯文本/图片/视频/音频等等。资源的表述格
式可以通过协商机制来确定。请求-响应方向的表述通常使用不同的格式。
③ 状态转移
状态转移说的是：在客户端和服务器端之间转移（transfer）代表资源状态的表述。通过转移和操作资源的表述，来间接实现操作资源的目的。

具体说，就是 HTTP 协议里面，四个表示操作方式的动词：GET、POST、PUT、DELETE。它们分别对应四种基本操作：GET 用来获取资源，POST 用来新建资源，PUT 用来更新资源，DELETE
用来删除资源。
REST 风格提倡 URL 地址使用统一的风格设计，从前到后各个单词使用斜杠分开，不使用问号键值对方式携带请求参数，而是将要发送给服务器的数据作为 URL 地址的一部分，以保证整体风格的一致性。

| 操作     | 传统方式         | REST 风格                |
| -------- | ---------------- | ------------------------ |
| 查询操作 | getUserById?id=1 | user/1-->get 请求方式    |
| 保存操作 | saveUser         | user-->post 请求方式     |
| 删除操作 | deleteUser?id=1  | user/1-->delete 请求方式 |
| 更新操作 | updateUser       | user-->put 请求方式      |
