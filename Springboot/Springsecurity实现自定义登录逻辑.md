Springsecurity实现自定义登录逻辑

配置Security的配置类

```java
@Configuration
public class SecurityConfig {

    @Bean
    public PasswordEncoder getPw(){
        return new BCryptPasswordEncoder();
    }
}

```

实现UserDetail接口

```java
package com.guo.demo.service.impl;

import org.springframework.security.core.authority.AuthorityUtils;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.security.crypto.password.PasswordEncoder;

public class UserDetailsServiceImpl implements UserDetailsService {

    private PasswordEncoder pw;
    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        //1.查询数据库判断用户是否存在，不存在就抛出用户名不存在的异常
        if(!"admin".equals(username)){
            throw new UsernameNotFoundException("用户名不存在");
        }
        //2.把查询的密码（注册时已经加密过)进行解析，或者直接把密码放入构造方法
      String pa=pw.encode("123");
        return new User(username,pa, AuthorityUtils.
                commaSeparatedStringToAuthorityList("admin,normal"));

    }
}

```

配置自定义登录页面，和对非login页面的非法访问进行拦截。

```java

    protected void configure(HttpSecurity http) throws Exception {
        http.formLogin()
                 .usernameParameter("username")
                .passwordParameter("password")//设置请求账户和密码的参数名
                .loginProcessingUrl("/login")//当发现/login时认为是登录，必须和表单提交的地址一样,才会去执行
                 //UserDetailsServiceImpl的逻辑
                .loginPage("/login.html")//自定义登录页面
                .successForwardUrl("/toMain")//登录成功跳转页面必须是post请求
                .failureForwardUrl("/toError");
        http.authorizeHttpRequests() //所有的请求都必须被认证，必须登录之后被访问。
              .antMatchers("/error.html").permitAll()
            .antMatchers("/login.html").permitAll()//login.html可以直接放行
                .anyRequest().authenticated();
        http.csrf().disable();//关闭csrf防护
 }
```

在controller实现post请求的toMain

```java
@RequestMapping("toMain")
    public String toMain(){
        return "redirect:main.html";
   }
```

在实际开发中一般是用到前后端分离，不会通过controller层编写post请求，因此![image-20211221105646329](C:\Users\逐梦\AppData\Roaming\Typora\typora-user-images\image-20211221105646329.png)

这个并不能满足我们的需求，我们对其进行修改。需要编写一个类实现AuthenticationSuccessHandler接口。

```java
public class MyAuthenticationHandler  implements AuthenticationSuccessHandler {
    private String url;
    public MyAuthenticationHandler(String url){
        this.url=url;
    }
    @Override
 public void onAuthenticationSuccess(HttpServletRequest request, HttpServletResponse response,
                                        Authentication authentication) throws IOException, ServletException {

      response.sendRedirect(url);
    }
}
```

然后在原先的Springsecurity类中对代码进行修改

![image-20211221105855601](C:\Users\逐梦\AppData\Roaming\Typora\typora-user-images\image-20211221105855601.png)

```java
  @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.formLogin()
                .usernameParameter("username")
                .passwordParameter("password")
                .loginProcessingUrl("/login")//当发现/login时认为是登录，必须和表单提交的地址一样,才会去执行
                                             //UserDetailsServiceImpl的逻辑
                .loginPage("/login.html")//自定义登录页面
               // .successForwardUrl("/toMain")//登录成功跳转页面必须是post请求
                //登录成功处理器不能和successForwardUrl共存
                .successHandler(new MyAuthenticationHandler("www.baidu.com"));
               // .failureForwardUrl("/toError");
                .failureHandler(new MyAuthenticationHandler("www.baiidu.com"))
        http.authorizeHttpRequests() //所有的请求都必须被认证，必须登录之后被访问。
                .antMatchers("/error.html").permitAll()
                .antMatchers("/login.html").permitAll()//login.html可以直接放行
                .anyRequest().authenticated();
        http.csrf().disable();//关闭csrf防护
    }
```

登录成功后的处理器的方法

```java
public void onAuthenticationSuccess(HttpServletRequest request, HttpServletResponse response,
                                        Authentication authentication) throws IOException, ServletException {

          //response.sendRedirect(url);
        User user = (User) authentication.getPrincipal();
        System.out.println(user.getUsername());
        System.out.println(user.getPassword());
        System.out.println(user.getAuthorities());
        response.sendRedirect(url);
  }
```

关于授权认证方法中

anyRequest表示除去被anyMachers放行外的所有的请求都必须被认证，相当于被拦截器拦截了

anyMatchers("").permitAll()表示对指定的资源进行放行.一般对js，css，images资源进行放行.

```java
.anyMatchers("/js/**","/css/**","image/**").permitAll()
```

regexMatchers实用正则表达式进行放行

```java
.regexMacheres(".+[.]png").permitAll()
//
.regexMacheres(HttpMethod.POST,"/demo")
// 指定请求的方式为post
```

如果在配置文件中已经指定了访问的前缀xxx,则可以通过以下方法来访问。

```java
.mvcMachers("/demo").servletPath("/xxx")
.permitAll();
//或者
.antMachers("/xxx/demo").permitAll()
//.antMachers("/xxx/demo").hasAuthority("admin")//权限判断，判断是否拥有权限
    //hasAnyAuthority权限判断，判断是否拥有多个权限的任意一个
```

![image-20211222113609693](C:\Users\逐梦\AppData\Roaming\Typora\typora-user-images\image-20211222113609693.png)

定于了角色abc，其中前缀ROLE_是固定要写的，代表一个角色.

```
.hasRole()判断是否有某个角色 前缀ROLE_应该去除。
.hasAnyRole() 判断多个角色。
.hasIpAddress() 判断IP地址
```

自定义的403错误处理

编写handler方法实现AccessDeniedHandler 接口

```java
@Component
public class MyAccessDeniedHandler implements AccessDeniedHandler {


    @Override
    public void handle(HttpServletRequest request, HttpServletResponse response, AccessDeniedException accessDeniedException) throws IOException, ServletException {
         response.setStatus(HttpServletResponse.SC_FORBIDDEN);
         response.setHeader("Content-Type","application/json;charset=utf-8");

        PrintWriter writer = response.getWriter();
        writer.write("{\"status\":\"error\",\"msg\":\"权限不足,清连续管理员\"}");
        writer.flush();
        writer.close();
    }
}
```

在sercurity中进行使用

```java
@Autowired
    private  MyAccessDeniedHandler    myAccessDeniedHandler;
  
  
  
  http.exceptionHandling()
                .accessDeniedHandler(myAccessDeniedHandler);
```

access结合自定义方法实现权限控制。

编写自定义serviceimpl(service)类

```java
public class MyServiceImpl implements MyService {

    @Override
    public boolean hasPermission(HttpServletRequest request, Authentication authentication) {
        Object obj = authentication.getPrincipal();
        if (obj instanceof UserDetails){
            UserDetails userDetails = (UserDetails) obj;
            Collection<? extends GrantedAuthority> authorities = userDetails.getAuthorities();
            return authorities.contains(new SimpleGrantedAuthority((request.getRequestURI())));
        }
      return false;
    }
}

```

在config类中进行使用

```java
.anyRequest().access("@myServiceImpl.hasPermission(request,authentication)");
```

使用注解来定义角色，该角色如果没有在用户自定义配置的类的权限列表中，就会报500错误。

```java
在主启动类中加上
@EnableGlobalMethodSecurity(securedEnabled=true) 才能使用@Secured注解
    
  在需要角色权限控制的类中加上@Secured("ROLE_abc");
ROLE_不能删除。
    
@PreAuthorize("hasRole('ROLE_abc')")
  可以以ROLE_开头，也可以不以ROLE_开头。
```

remember me功能

编写一个PersistentTokenRepository的bean

```java
  @Bean
    public PersistentTokenRepository  getPersistentTokenRepository(){
        JdbcTokenRepositoryImpl jdbcTokenRepository = new JdbcTokenRepositoryImpl();
        jdbcTokenRepository.setDataSource(dataSource);
        //自动建表，第一次启动需要，第二次启动注释掉
        jdbcTokenRepository.setCreateTableOnStartup(true);
        return jdbcTokenRepository;
 }
```

在配置类中使用

```java
   @Autowired
    private DataSource dataSource;
    @Autowired
    private PersistentTokenRepository persistentTokenRepository;
 
 http.rememberMe()
      .tokenValiditySeconds(60)//失效时间
                .userDetailsService(userDetailsService)
                .tokenRepository(persistentTokenRepository);
```

退出登录

```java
http.logout()
        .logoutUrl()//设置自定义退出的url
    .logoutSuccessUrl("/login.html");
```

取服务器端生成的token

```html
<input type="hidden" th:value="${_csrf.token}" name="_csrf" th:if="${_csrf}">
```

