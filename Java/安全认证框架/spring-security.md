# Spring Security

**优点**

- 功能强大，支持多种认证和授权机制，如 OAuth2.0、JWT、LDAP 等。
- 集成方便，可以与 Spring Boot、Spring MVC、Spring Data 等其他 Spring 项目无缝集成，也可以与其他技术栈配合使用。
- 社区活跃，拥有强大的社区支持和丰富的文档资源，遇到问题容易找到解决方案。
  **缺点**
- 学习成本高，配置复杂，需要理解 Spring Security 的架构和原理，才能灵活地使用和定制。
- 侵入性强，与 Spring 框架耦合度高，不适合非 Spring 项目使用。
- 性能开销大，过滤器链的执行和安全组件的调用会增加系统的响应时间和资源消耗。

如果你的项目是基于 Spring 框架开发的，并且需要实现一些高级的安全机制，如 OAuth2.0、JWT 等，则可以选择 Spring Security 作为你的安全框架。
如果你的项目是一个大型的企业级应用，并且需要一个完整的安全解决方案，则可以选择 Spring Security 作为你的安全框架。

## 引入依赖

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-security</artifactId>
</dependency>
<dependency>
  <groupId>io.jsonwebtoken</groupId>
  <artifactId>jjwt</artifactId>
  <version>0.9.1</version>
</dependency>
```

## 配置 security
1. 密码编码器,校验密码
2. 设置请求权限
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.authentication.AuthenticationManager;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter;

@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Autowired
    private UserDetailsService userDetailsService;

    @Autowired
    private JwtAuthenticationFilter jwtAuthenticationFilter;

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        // 使用自定义的UserDetailsService和PasswordEncoder
        auth.userDetailsService(userDetailsService).passwordEncoder(new BCryptPasswordEncoder());
    }
    @Override
    public void configure(WebSecurity web) throws Exception {
        web.ignoring().antMatchers("/js/**","/css/**","/images/**");
    }
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        // 关闭csrf和session
        http.csrf().disable().sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS);
        // 配置登录和登出的url和逻辑
        // http.formLogin().loginProcessingUrl("/login").successHandler(new LoginSuccessHandler()).failureHandler(new LoginFailureHandler());
        // http.logout().logoutUrl("/logout").logoutSuccessHandler(new LogoutSuccessHandler() );
        // 配置需要认证和授权的url和角色
        http.authorizeRequests()
                .antMatchers( "/login","/logout").permitAll()
                .antMatchers("/admin/**").hasRole("ADMIN")
                .antMatchers("/user/**").hasRole("USER")
                .anyRequest().authenticated();
        // 添加自定义的Jwt过滤器
        http.addFilterBefore(jwtAuthenticationFilter, UsernamePasswordAuthenticationFilter.class);
    }

    @Bean
    @Override
    public AuthenticationManager authenticationManagerBean() throws Exception {
        // 重写此方法，以便在Controller层调用
        return super.authenticationManagerBean();
    }
}

```

## 实现 UserDetailsService
UserDetailsService 
```java
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.stereotype.Service;

import java.util.*;
import java.util.stream.Collectors;

@Service
public class UserDetailsServiceImpl implements UserDetailsService {
    public  static final Map<String,User> userMap = new HashMap<>();
    static {
        userMap.put("admin",new User("admin","$2a$10$Z5andkIrX3tSLAab0faEBe2xkZX0zsqm.t0HDRBru9QwyJ7UZmpXa",generateList("ADMIN")));
        userMap.put("user",new User("user","$2a$10$Z5andkIrX3tSLAab0faEBe2xkZX0zsqm.t0HDRBru9QwyJ7UZmpXa",generateList("USER")));
    }

    public static List<String> generateList(String ...elements){
        List<String> list =  new ArrayList<>();
        for(String e :elements){
            list.add(e);
        }
        return list;
    }
    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        // 根据用户名从数据库中查询用户信息，如果不存在则抛出异常
        User user = userMap.get(username);
        if (user == null) {
            throw new UsernameNotFoundException("用户不存在");
        }
        // 返回一个UserDetails对象，包含用户名、密码、角色等信息
        return new org.springframework.security.core.userdetails.User(user.getUserName(), user.getPassword(), getAuthorities(user.getRoles()));
    }

    // 将用户的角色转换为GrantedAuthority对象的集合，用于授权判断
    private Collection<? extends GrantedAuthority> getAuthorities(List<String> roles) {
        return roles.stream().map(role -> new SimpleGrantedAuthority("ROLE_" + role)).collect(Collectors.toList());
    }
}
```

## 实现 JwtAuthenticationFilter,token校验过滤器

```java
import io.jsonwebtoken.Claims;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.security.web.authentication.WebAuthenticationDetailsSource;
import org.springframework.stereotype.Component;
import org.springframework.web.filter.OncePerRequestFilter;

import javax.servlet.FilterChain;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.Collection;
import java.util.List;
import java.util.stream.Collectors;

@Component
public class JwtAuthenticationFilter extends OncePerRequestFilter {

    @Autowired
    private JwtUtils jwtUtils;

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
        // 获取请求头中的Authorization字段，即token
        String token = request.getHeader("Authorization");
        if (token != null && token.startsWith("Bearer ")) {
            // 去掉token前缀，解析token，获取用户名和角色信息
            token = token.substring(7);
            Claims claims = jwtUtils.parseToken(token);
            String username = claims.getSubject();
            List<String> roles = claims.get("roles", List.class);
            // 如果token有效，构造一个UsernamePasswordAuthenticationToken对象，设置到SecurityContext中，用于授权判断
            if (username != null && SecurityContextHolder.getContext().getAuthentication() == null) {
                UsernamePasswordAuthenticationToken authenticationToken = new UsernamePasswordAuthenticationToken(username, null, getAuthorities(roles));
                authenticationToken.setDetails(new WebAuthenticationDetailsSource().buildDetails(request));
                SecurityContextHolder.getContext().setAuthentication(authenticationToken);
            }
        }
        // 继续执行下一个过滤器
        filterChain.doFilter(request, response);
    }

    // 将用户的角色转换为GrantedAuthority对象的集合，用于授权判断
    private Collection<? extends GrantedAuthority> getAuthorities(List<String> roles) {
        return roles.stream().map(role -> new SimpleGrantedAuthority("ROLE_" + role)).collect(Collectors.toList());
    }
}
```

## 实现 JwtUtils

```java
import io.jsonwebtoken.*;
import org.springframework.stereotype.Component;

import java.util.Date;
import java.util.List;

/*
token生成与验证工具类
*/
@Component
public class JwtUtils {

  // 签名密钥，可以从配置文件中读取
  private String secretKey = "secret";

  // 签名过期时间，可以从配置文件中读取
  private long expirationTime = 3600 * 1000;

  // 根据用户名和角色生成token
  public String generateToken(String username, List<String> roles) {
    // 设置token的过期时间
    Date expirationDate = new Date(System.currentTimeMillis() + expirationTime);
    // 创建一个JwtBuilder对象，设置token的用户名、角色、过期时间、签名密钥等信息
    JwtBuilder builder = Jwts.builder()
      .setSubject(username)
      .claim("roles", roles)
      .setExpiration(expirationDate)
      .signWith(SignatureAlgorithm.HS256, secretKey);
    // 返回生成的token字符串
    return builder.compact();
  }

  // 解析token，获取Claims对象，包含用户名、角色、过期时间等信息
  public Claims parseToken(String token) {
    // 使用JwtParser对象，设置签名密钥，解析token，返回Claims对象
    JwtParser parser = Jwts.parser().setSigningKey(secretKey);
    return parser.parseClaimsJws(token).getBody();
  }

  // 判断token是否过期
  public boolean isExpired(String token) {
    // 获取token的过期时间，与当前时间比较，返回结果
    Date expirationDate = parseToken(token).getExpiration();
    return expirationDate.before(new Date());
  }

  // 刷新token，生成一个新的token，延长过期时间
  public String refreshToken(String token) {
    // 获取原来的token中的用户名和角色信息
    Claims claims = parseToken(token);
    String username = claims.getSubject();
    List<String> roles = claims.get("roles", List.class);
    // 调用生成token的方法，返回新的token
    return generateToken(username, roles);
  }
}
```

## 实现 controller

```java
@RestController
public class UserController {

  @Autowired
  private AuthenticationManager authenticationManager;

  @Autowired
  private JwtUtils jwtUtils;

  // 登录接口，参数为用户名和密码，返回一个包含token的JSON数据
  @PostMapping("/login")
  public String login(@RequestParam String username, @RequestParam String password) {
    // 使用用户名和密码创建一个UsernamePasswordAuthenticationToken对象，用于认证
    UsernamePasswordAuthenticationToken authenticationToken = new UsernamePasswordAuthenticationToken(username, password);
    try {
      // 调用AuthenticationManager的authenticate方法进行认证，如果成功，则返回一个包含用户信息和权限信息的Authentication对象
      Authentication authentication = authenticationManager.authenticate(authenticationToken);
      // 获取用户信息和权限信息，转换为UserDetails对象
      UserDetails userDetails = (UserDetails) authentication.getPrincipal();
      // 获取用户的角色列表，去掉前缀"ROLE_"
      List<String> roles = userDetails.getAuthorities().stream().map(GrantedAuthority::getAuthority).map(role -> role.substring(5)).collect(Collectors.toList());
      // 调用JwtUtils的generateToken方法，根据用户名和角色列表生成token
      String token = jwtUtils.generateToken(userDetails.getUsername(), roles);
      // 返回一个包含token的JSON数据
      return "{\"token\": \"" + token + "\"}";
    } catch (AuthenticationException e) {
      // 如果认证失败，则抛出异常或返回错误信息
      throw e;
      // return "{\"error\": \"用户名或密码错误\"}";
    }
  }

  // 注销接口，无需参数，返回一个成功信息
  @GetMapping("/logout")
  public String logout() {
    // 获取当前登录的用户信息，如果存在，则注销登录，清除SecurityContext中的Authentication对象
    Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
    if (authentication != null) {
      SecurityContextHolder.clearContext();
    }
    // 返回一个成功信息
    return "{\"message\": \"注销成功\"}";
  }

  // 测试接口，需要ADMIN角色才能访问，返回一个欢迎信息
  @GetMapping("/admin/hello")
  public String adminHello() {
    return "{\"message\": \"Hello, admin\"}";
  }

  // 测试接口，需要USER角色才能访问，返回一个欢迎信息
  @GetMapping("/user/hello")
  public String userHello() {
    return "{\"message\": \"Hello, user\"}";
  }
}

```
