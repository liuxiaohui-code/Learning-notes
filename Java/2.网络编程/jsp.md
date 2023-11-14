# jsp

解决问题: 原生 servlet 响应静态页面,用 PrintWriter 输出 HTML 比较痛苦，因为不但要正确编写 HTML，还需要插入各种变量。

1. JSP 是 Java Server Pages 的缩写，它的文件必须放到`/src/main/webapp` 下，文件名必须以`.jsp `结尾
2. 整个文件与 HTML 并无太大区别，但需要插入变量，或者动态输出的地方，使用特殊指令`<% … %>`
   1. 包含在`<%--`和`--%>`之间的是 JSP 的注释，它们会被完全忽略
   2. 包含在`<%`和`%>`之间的是 Java 代码，可以编写任意 Java 代码；
   3. 如果使用`<%= xxx %>`则可以快捷输出一个变量的值。
3. 内置变量
   1. out：表示 HttpServletResponse 的 PrintWriter；
   2. session：表示当前 HttpSession 对象；
   3. request：表示 HttpServletRequest 对象。
4. 访问 JSP 页面时，直接指定完整路径。例如，`http://localhost:8080/hello.jsp`
5. 原理: JSP 在执行前首先被编译成一个 Servlet。在 Tomcat 的临时目录下，可以找到一个 hello_jsp.java 的源文件，这个文件就是 Tomcat 把 JSP 自动转换成的 Servlet 源码

```html
<html>
  <head>
    <title>Hello World - JSP</title>
  </head>
  <body>
    <%-- JSP Comment --%>
    <h1>Hello World!</h1>
    <p>
      <% out.println("Your IP address is "); %>
      <span style="color:red"> <%= request.getRemoteAddr() %> </span>
    </p>
  </body>
</html>
```

# page 指令引入 Java 类

```java
<%@ page import="java.io.*" %>
<%@ page import="java.util.*" %>
```

# inclue 指令引入另一个 jsp

```java
<html>
<body>
    <%@ include file="header.jsp"%>
    <h1>Index Page</h1>
    <%@ include file="footer.jsp"%>
</body>
```

# 自定义标签

1. 引入 taglib 的 jar 包
2. 需要正确声明

```java
<c:out value = "${sessionScope.user.name}"/>
```
