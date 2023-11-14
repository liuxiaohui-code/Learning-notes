# Thymeleaf

Thymeleaf 是一款用于渲染 XML/XHTML/HTML5 内容的模板引擎。它与 JSP，Velocity，FreeMaker 等模板引擎类似，也可以轻易地与 Spring MVC 等 Web 框架集成。与其它模板引擎相比，Thymeleaf 最大的特点是，即使不启动 Web 应用，也可以直接在浏览器中打开并正确显示模板页面

Thymeleaf 模板引擎具有以下特点：

1. 动静结合：Thymeleaf 既可以直接使用浏览器打开，查看页面的静态效果，也可以通过 Web 应用程序进行访问，查看动态页面效果。
2. 开箱即用：Thymeleaf 提供了 Spring 标准方言以及一个与 SpringMVC 完美集成的可选模块，可以快速的实现表单绑定、属性编辑器、国际化等功能。
3. 多方言支持：它提供了 Thymeleaf 标准和 Spring 标准两种方言，可以直接套用模板实现 JSTL、 OGNL 表达式；必要时，开发人员也可以扩展和创建自定义的方言。
4. 与 SpringBoot 完美整合：SpringBoot 为 Thymeleaf 提供了的默认配置，并且还为 Thymeleaf 设置了视图解析器，因此 Thymeleaf 可以与 Spring Boot 完美整合。

## maven 依赖

spring-boot 项目:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

## 配置文件

```yaml
spring:
  thymeleaf:
    cache: true # 页面缓存,默认 true
    prefix: classpath:/templates/ # 在构建URL时为视图名称添加前缀。
    encoding: UTF-8 #编码默认
    suffix: .html #生成URL时附加到视图名称的后缀。
    mode: HTML #模板
    check-template: true #渲染前检查是否存在模板
    check-template-location: true # 检查文件路径是否存在
    enable-spring-el-compiler: false # 开启spel表达式
    enable: true # 是否为Web框架启用Thymelaf视图解析
    excluded-view-names: # 应从解析中排除的视图名称（允许模式）的逗号分隔列表。
    reactive:
      chunked-mode-view-names: #以逗号分隔的视图名称列表（允许模式），当设置了最大块大小时，该列表应是在CHUNKED模式下执行的唯一视图名称。
      full-mode-view-names: #即使设置了最大块大小，也应在FULL模式下执行的视图名称（允许模式）的逗号分隔列表。
      max-chunk-size: 0B #用于写入响应的数据缓冲区的最大大小。如果设置了此选项，默认情况下模板将在CHUNKED模式下执行。
      media-types: [
          text/html,
          application/xhtml+xml,
          application/xml,
          text/xml,
          application/rss+xml,
          application/atom+xml,
          application/javascript,
          application/ecmascript,
          text/javascript,
          text/ecmascript,
          application/json,
          text/css,
          text/plain,
          text/event-stream,
        ] #视图技术支持的媒体类型。
    render-hidden-markers-before-checkboxes: false # 作为复选框标记的隐藏表单输入是否应在复选框元素本身之前呈现。
    servlet:
      content-type: text/html #写入HTTP响应的内容类型值。
      produce-partial-output-while-processing: true #Thymelaf是应该尽快开始写入部分输出，还是缓冲直到模板处理完成。
    template-resolver-order: #链中模板解析程序的顺序。默认情况下，模板解析器是链中的第一个。订单从1开始，只有在定义了其他“TemplateResolver”bean时才应该设置。
    view-names: #可以解析的视图名称（允许模式）的逗号分隔列表。
```

## 模板

1. 前提 `xmlns:th="http://www.thymeleaf.org"`

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
	<head>
		<meta charset="UTF-8" />
		<title>Title</title>
	</head>
	<body>
		<!--th:text 为 Thymeleaf 属性，用于在展示文本-->
		<h1 th:text="迎您来到Thymeleaf">欢迎您访问静态页面 HTML</h1>
	</body>
</html>
```

## controller 配置

1. 使用`@Controller`而不是`@RestController`
2. 绑定数据:
   1. 服务接口需要加入 `Model` 对象作为参数，并使用 `Model` 对象来绑定需要传递的数据，以便在 thymeleaf 中使用
   2. `HttpServletRequest.setAttribute()`
   3. 返回`ModelAndView`

```java
@Controller
public class ThymeleafController {
    @GetMapping("/getStudents")
    public ModelAndView getStudent(){
        List<Student> students = new LinkedList<>();
        Student student = new Student();
        student.setId(1);
        student.setName("全栈学习笔记");
        student.setAge(21);
        Student student1 = new Student();
        student1.setId(2);
        student1.setName("张三");
        student1.setAge(22);
        students.add(student);
        students.add(student1);
        ModelAndView modelAndView = new ModelAndView(); // 模板
        modelAndView.addObject("students",students); // 添加模板数据 ${students}
        modelAndView.setViewName("students"); // 设置模板,及student.html
        return modelAndView;
    }
    @RequestMapping("/getString")
    public String getString(HttpServletRequest request){
        String name = "全栈学习笔记";
        request.setAttribute("name",name); // 添加模板数据
        return "index.html"; // 设置映射模板
    }
    @RequestMapping("/getModel")
    public String getModel(Model model){
        model.addAttribute("key","这是一个键");
        return "index.html";
    }

}
```

## 语法规则

1. 在使用 Thymeleaf 之前，首先要在页面的 html 标签中声明名称空间，`xmlns:th="http://www.thymeleaf.org"`
   > 在 html 标签中声明此名称空间，可避免编辑器出现 html 验证错误，但这一步并非必须进行的，即使我们不声明该命名空间，也不影响 Thymeleaf 的使用。
2. 标准表达式语法
   Thymeleaf 模板引擎支持多种表达式：

### 内置对象

`#ctx` ：上下文对象；
`#vars` ：上下文变量；
`#locale`：上下文的语言环境；
`#request`：HttpServletRequest 对象（仅在 Web 应用中可用）；
`#response`：HttpServletResponse 对象（仅在 Web 应用中可用）；
`#session`：HttpSession 对象（仅在 Web 应用中可用）；
`#servletContext`：ServletContext 对象（仅在 Web 应用中可用）。 - 使用内置的工具对象

`strings`：字符串工具对象，常用方法有：equals、equalsIgnoreCase、length、trim、toUpperCase、toLowerCase、indexOf、substring、replace、startsWith、endsWith，contains 和 containsIgnoreCase 等；
`numbers`：数字工具对象，常用的方法有：formatDecimal 等；
`bools`：布尔工具对象，常用的方法有：isTrue 和 isFalse 等；
`arrays`：数组工具对象，常用的方法有：toArray、length、isEmpty、contains 和 containsAll 等；
`lists/sets`：List/Set 集合工具对象，常用的方法有：toList、size、isEmpty、contains、containsAll 和 sort 等；
`maps`：Map 集合工具对象，常用的方法有：size、isEmpty、containsKey 和 containsValue 等；
`dates`：日期工具对象，常用的方法有：format、year、month、hour 和 createNow 等。

### 变量表达式

- `${object}` 变量表达式,
  - 获取对象的属性和方法 `${person.lastName}`
  - 可以使用 ctx，vars，locale，request，response，session，servletContext 内置对象
  - 可以使用 dates，numbers，strings，objects，arrays，lists，sets，maps 等内置方法
- `*{...}` 选择变量表达式,与变量表达式功能基本一致，只是在变量表达式的基础上增加了与 `th:object` 的配合使用。当使用 `th:object` 存储一个对象后，我们可以在其`后代`中使用选择变量表达式`*{...}`获取该对象中的属性，其中，`*`即代表该对象。
- `@{...}` 链接表达式
  - `@{/xxx}` 无参请求
  - `@{/xxx(k1=v1,k2=v2)}` 有参请求
  - `@{/项目本地的资源路径}` 引入本地资源
  - `@{/webjars/资源在jar包中的路径}` 引入外部资源
- `#{...}`国际化表达式
- `~{...}`片段引用:
  - `~{templatename::fragmentname}`,模板名::`th:fragment`
  - `~{templatename::#id}` 支持 id 选择器
  - 代码块表达式需要配合 th 属性（th:insert，th:replace，th:include）一起使用。

### th 属性

- `th:id` 替换 id
- `th:text` 替换 text
- `th:utext` 替换 text,不转义特殊字符
- `th:object` 子元素使用`*{}`获取 object 的属性
- `th:value` 替换 value
- `th:with` 定义临时变量
- `th:style` 替换 style
- `th:onclick` 替换 onclick
- `th:each="value,state:array|map|iteration"` 循环
- `th:if` 判断满足显示元素
- `th:unless` 判断不满足显示元素
- `th:switch`
- `th:case`
- `th:fragment` 代码片段
- `th:insert="~{}"` 插入代码片段
- `th:replace="~{}"` 用代码片段替换整个元素
- `th:include="~{}"` 将代码块片段包含的内容插入到使用了 th:include 的 HTML 标签中
- `th:src="@{}"`
- `th:inline`

```html
<!-- th:id 替换 HTML 的 id 属性 -->
<input id="html-id" th:id="thymeleaf-id" />
<!-- th:text	文本替换，转义特殊字符	 -->
<h1 th:text="hello，bianchengbang">hello</h1>
<!-- th:utext	文本替换，不转义特殊字符	 -->
<div th:utext="'<h1>欢迎来到编程帮！</h1>'">欢迎你</div>
<!-- th:object	在父标签选择对象，子标签使用 *{…} 选择表达式选取值。 -->
<div th:object="${session.user}">
	<p th:text="*{fisrtName}">firstname</p>
</div>
<!-- th:value	替换 value 属性	 -->
<input th:value="${user.name}" />
<!-- th:with	局部变量赋值运算	 -->
<div th:with="isEvens = ${prodStat.count}%2 == 0" th:text="${isEvens}"></div>
<!-- th:style	设置样式	 -->
<div th:style="'color:#F00; font-weight:bold'">编程帮 www.biancheng.net</div>
<!-- th:onclick	点击事件 -->
<td th:onclick="'getInfo()'"></td>
<!-- th:each	遍历，支持 Iterable、Map、数组等,state包含下下标信息,可以省略 -->
<table>
	<!-- 
    state:
      index：列表状态的序号，从0开始；
      count：列表状态的序号，从1开始；
      size：列表状态，列表数据条数；
      current：列表状态，当前数据对象
      even：列表状态，是否为奇数，boolean类型
      odd：列表状态，是否为偶数，boolean类型
      first：列表状态，是否为第一条，boolean类型
      last：列表状态，是否为最后一条，boolean类型
   -->
	<tr th:each="m,state:${session.map}">
		<td th:text="${m.getKey()}"></td>
		<td th:text="${m.getValue()}"></td>
	</tr>
</table>
<!-- th:if	根据条件判断是否需要展示此标签	 -->
<a th:if="${userId == collect.userId}">
	<!-- th:unless	和 th:if 判断相反，满足条件时不显示	 -->
	<div th:unless="${m.getKey()=='name'}"></div>
	<!-- th:switch	与 Java 的 switch case语句类似,通常与 th:case 配合使用，根据不同的条件展示不同的内容 -->
	<div th:switch="${name}">
		<span th:case="a">编程帮</span>
		<span th:case="b">www.biancheng.net</span>
	</div>
	<!-- th:fragment	模板布局，类似 JSP 的 tag，用来定义一段被引用或包含的模板片段 -->
	<footer th:fragment="footer">插入的内容</footer>
	<!-- th:insert	布局标签；将使用 th:fragment 属性指定的模板片段（包含标签）插入到当前标签中。	 -->
	<div th:insert="commons/bar::footer"></div>
	<!-- th:replace	布局标签；使用 th:fragment 属性指定的模板片段（包含标签）替换当前整个标签。	 -->
	<div th:replace="commons/bar::footer"></div>
	<!-- th:selected	select 选择框选中 -->
	<select>
		<option>---</option>
		<option th:selected="${name=='a'}">编程帮</option>
		<option th:selected="${name=='b'}">www.biancheng.net</option>
	</select>
	<!-- th:src	替换 HTML 中的 src 属性 	 -->
	<img th:src="@{/asserts/img/bootstrap-solid.svg}" src="asserts/img/bootstrap-solid.svg" />
	<!-- th:inline	内联属性；该属性有 text、none、javascript 三种取值，在 <script> 标签中使用时，js 代码中可以获取到后台传递页面的对象。 -->
	<script type="text/javascript" th:inline="javascript">
		var name = /*[[${name}]]*/ "bianchengbang"
		alert(name)
	</script>
	<!-- th:action	替换表单提交地址 -->
	<form th:action="@{/user/login}" th:method="post"></form
></a>
```

## 公共页面抽取

在 Web 项目中，通常会存在一些公共页面片段（重复代码），例如头部导航栏、侧边菜单栏和公共的 js css 等。我们一般会把这些公共页面片段抽取出来，存放在一个独立的页面中，然后再由其他页面根据需要进行引用，这样可以消除代码重复，使页面更加简洁。

1. 抽取公共页面
   Thymeleaf 作为一种优雅且高度可维护的模板引擎，同样支持公共页面的抽取和引用。我们可以将公共页面片段抽取出来，存放到一个独立的页面中，并使用 Thymeleaf 提供的 th:fragment 属性为这些抽取出来的公共页面片段命名。

2. 引用公共页面
   在 Thymeleaf 中，我们可以使用以下 3 个属性，将公共页面片段引入到当前页面中。
   th:insert：将代码块片段整个插入到使用了 th:insert 属性的 HTML 标签中；
   th:replace：将代码块片段整个替换使用了 th:replace 属性的 HTML 标签中；
   th:include：将代码块片段包含的内容插入到使用了 th:include 属性的 HTML 标签中。

使用上 3 个属性引入页面片段，都可以通过以下 2 种方式实现。
~{templatename::selector}：模板名::选择器
~{templatename::fragmentname}：模板名::片段名
通常情况下，~{} 可以省略，其行内写法为 [[~{...}]] 或 [(~{...})]，其中 [[~{...}]] 会转义特殊字符，[(~{...})] 则不会转义特殊字符。

3.  传递参数
    Thymeleaf 在抽取和引入公共页面片段时，还可以进行参数传递，大致步骤如下：
    传入参数；
    引用公共页面片段时，我们可以通过以下 2 种方式，将参数传入到被引用的页面片段中：
    模板名::选择器名或片段名(参数 1=参数值 1,参数 2=参数值 2)
    模板名::选择器名或片段名(参数值 1,参数值 2)
    注：

        若传入参数较少时，一般采用第二种方式，直接将参数值传入页面片段中；
        若参数较多时，建议使用第一种方式，明确指定参数名和参数值，。

    使用参数。
