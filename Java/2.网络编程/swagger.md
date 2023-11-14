# swagger

```xml
<!-- spring boot 集成 -->
<dependency>
  <groupId>io.springfox</groupId>
  <artifactId>springfox-swagger2</artifactId>
  <version>2.8.0</version>
</dependency>
<!--传统绿色ui -->
<dependency>
  <groupId>io.springfox</groupId>
  <artifactId>springfox-swagger-ui</artifactId>
  <version>2.9.2</version>
</dependency>
<!-- 蓝色左右布局ui -->
<dependency>
  <groupId>com.github.xiaoymin</groupId>
  <artifactId>swagger-bootstrap-ui</artifactId>
  <version>1.9.6</version>
</dependency>
```

## 配置类

```java
@Configuration
@EnableSwagger2//开启Swagger2
public class SwaggerConfig {
  //配置Swagger的Bean实例
  @Bean
  public Docket swaggerSpringMvcPlugin() {
    return new Docket(DocumentationType.SWAGGER_2)
      .apiInfo(apiInfo())
      .groupName("yangl")//分组名称(可以创建多个Docket就有多个组名)
      .enable(true)//enable表示是否开启Swagger
      .select()
      //RequestHandlerSelectors指定扫描的包
      .apis(RequestHandlerSelectors.basePackage("com.qf.swagger.controller"))
      .build();
  }

  //配置API的基本信息（会在http://项目实际地址/swagger-ui.html页面显示）
  private ApiInfo apiInfo() {
    return new ApiInfoBuilder()
    .title("测试API文档标题")
    .description("测试api接口文档描述")
    .termsOfServiceUrl("http://www.baidu.com")
    .version("1.0")
    .build();
  }
}
```

## 实体类

```java
package com.qf.swagger.entity;

import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;

@ApiModel("用户实体类")
public class User {

    @ApiModelProperty("用户名")
    private String username;
    @ApiModelProperty("密码")
    private String password;
    public String getUsername() {
        return username;
    }
    public void setUsername(String username) {
        this.username = username;
    }
    public String getPassword() {
        return password;
    }
    public void setPassword(String password) {
        this.password = password;
    }
}
```

## controller

```java
import com.qf.swagger.entity.User;
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import io.swagger.annotations.ApiParam;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@Api(description ="用户管理API")
@RestController
public class UserController {

  @ApiOperation("添加用户")
  @PostMapping("/add")
  public User add(@ApiParam("用户名") String username,@ApiParam("密码") String password){
    return new User();
  }

  @ApiOperation("修改用户")
  @PostMapping("/update")
  public String update(){
    return "修改";
  }

  @ApiOperation("删除用户")
  @GetMapping("/delete")
  public Boolean delete(@ApiParam("用户编号") Integer id){
    return true;
  }

  @ApiOperation("查询用户")
  @RequestMapping("/query")
  public User query(){
    User user = new User();
    user.setUsername("jack");
    user.setPassword("1234");
    return user;
  }
}
```

## 注解总结

swagger 通过注解表明该接口会生成文档，包括接口名、请求方法、参数、返回信息

```java
@Api：修饰整个类，描述Controller的作用
@ApiOperation：描述一个类的一个方法，或者说一个接口
@ApiModel：用对象来接收参数 ，修饰类
@ApiModelProperty：用对象接收参数时，描述对象的一个字段
@ApiResponse   HTTP响应其中1个描述
@ApiResponses：HTTP响应整体描述，一般描述错误的响应
@ApiIgnore：使用该注解忽略这个API
@ApiError ：发生错误返回的信息
@ApiParam：单个参数描述
@ApiImplicitParam：一个请求参数，用在方法上
@ApiImplicitParams：多个请求参数
```

# springdoc

[官方文档](https://springdoc.org/#getting-started)

```xml
<!-- springboot -->
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-ui</artifactId>
    <version>1.6.14</version>
</dependency>
<!--  -->
<dependency>
  <groupId>org.springdoc</groupId>
  <artifactId>springdoc-openapi-webmvc-core</artifactId>
  <version>1.6.14</version>
</dependency>
```

无需其他配置,自动加载.注意: 对于 bean 不能开启懒加载

1. `/swagger-ui/index.html`
2. `/v3/api-docs` openapi 版本信息

```yaml
springdoc:
  swagger-ui:
    path: "/swagger.html" # 自定义ui路径
```

注解

```java
@Tag 修饰整个类，描述Controller的作用
@Parameter(hidden = true)or@Operation(hidden = true)or@Hidden  使用该注解忽略这个API
@Parameter  一个请求参数，用在方法上
@Parameters 多个请求参数
@Schema 用对象来接收参数 ，修饰类
@Schema 用对象接收参数时，描述对象的一个字段
@Operation(summary = "foo", description = "bar") 描述一个类的一个方法，或者说一个接口
@Parameter 单个参数描述
@ApiResponse(responseCode = "404", description = "foo") HTTP响应其中1个描述
```
