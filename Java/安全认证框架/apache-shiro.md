# Apache Shiro
**优点**
轻量级，功能简洁，只提供了核心的安全功能，如认证、授权、加密、会话管理等。
易于使用，API设计清晰，配置方式灵活，可以快速上手和定制自己的安全策略。
通用性强，可以与任何Java应用程序或框架协作，无论是Web应用还是非Web应用。
**缺点**
功能有限，不支持一些高级的安全机制，如OAuth2.0、JWT等，需要自己实现或集成第三方库。
社区不活跃，更新速度慢，文档资源少，遇到问题难以寻求帮助。
性能开销大，Realm的查询和缓存的维护会增加系统的响应时间和资源消耗。

如果你的项目是一个中小型的应用，并且只需要实现一些核心的安全功能，如认证、授权、加密、会话管理等，则可以选择Apache Shiro作为你的安全框架。
如果你的项目是一个非Web应用，并且需要一个通用的安全框架，则可以选择Apache Shiro作为你的安全框架。

https://juejin.cn/post/6991664333314850853

## 引入依赖

```xml
<dependency>
  <groupId>org.apache.shiro</groupId>
  <artifactId>shiro-spring-boot-web-starter</artifactId>
  <version>1.7.1</version>
</dependency>
<dependency>
  <groupId>io.jsonwebtoken</groupId>
  <artifactId>jjwt</artifactId>
  <version>0.9.1</version>
</dependency>
```

## 配置shiro

```java
@Configuration
public class ShiroConfig {

  @Autowired
  private UserRealm userRealm;

  @Autowired
  private JwtFilter jwtFilter;

  @Bean
  public SecurityManager securityManager() {
    // 创建一个DefaultWebSecurityManager对象，设置自定义的UserRealm和SessionManager
    DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
    securityManager.setRealm(userRealm);
    securityManager.setSessionManager(sessionManager());
    return securityManager;
  }

  @Bean
  public SessionManager sessionManager() {
    // 创建一个DefaultWebSessionManager对象，禁用Cookie和Session的创建和使用
    DefaultWebSessionManager sessionManager = new DefaultWebSessionManager();
    sessionManager.setSessionIdCookieEnabled(false);
    sessionManager.setSessionIdUrlRewritingEnabled(false);
    return sessionManager;
  }

  @Bean
  public ShiroFilterFactoryBean shiroFilterFactoryBean(SecurityManager securityManager) {
    // 创建一个ShiroFilterFactoryBean对象，设置SecurityManager和过滤器链
    ShiroFilterFactoryBean shiroFilterFactoryBean = new ShiroFilterFactoryBean();
    shiroFilterFactoryBean.setSecurityManager(securityManager);
    // 配置登录和登出的url和逻辑
    shiroFilterFactoryBean.setLoginUrl("/login");
    shiroFilterFactoryBean.setSuccessUrl("/index");
    shiroFilterFactoryBean.setUnauthorizedUrl("/unauthorized");
    // 配置需要认证和授权的url和角色
    Map<String, String> filterChainDefinitionMap = new LinkedHashMap<>();
    filterChainDefinitionMap.put("/login", "anon");
    filterChainDefinitionMap.put("/logout", "logout");
    filterChainDefinitionMap.put("/admin/**", "roles[ADMIN]");
    filterChainDefinitionMap.put("/user/**", "roles[USER]");
    filterChainDefinitionMap.put("/**", "authc");
    shiroFilterFactoryBean.setFilterChainDefinitionMap(filterChainDefinitionMap);
    // 添加自定义的Jwt过滤器，并设置到过滤器链中，拦截所有需要认证的请求
    Map<String, Filter> filters = new HashMap<>();
    filters.put("authc", jwtFilter);
    shiroFilterFactoryBean.setFilters(filters);
    return shiroFilterFactoryBean;
  }
}

```