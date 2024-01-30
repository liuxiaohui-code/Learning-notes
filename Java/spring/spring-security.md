# servlet application

[文档](https://docs.spring.io/spring-security/reference/index.html)

```xml
<dependencies>
	<!-- ... other dependency elements ... -->
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-security</artifactId>
	</dependency>
</dependencies>
```

1. springboot 引入依赖后的默认配置
   1. 注册一个名为`springSecurityFilterChain`的`Filter`,用来保护应用程序 URL、验证提交的用户名和密码、重定向到登录表单等
   2. 使用用户名 user 和随机生成的密码创建 `UserDetailsService` bean，并将其登录到控制台。
   3. 为每个请求注册`springSecurityFilterChain`
