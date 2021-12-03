## 用SpringBoot创建拦截器对象并注册



写一个类实现HandlerInterceptor接口（重写preHandle方法)

```java


public class LoginInterceptor implements HandlerInterceptor {

     /*
       @return 返回值为true则放行
       @handler 被拦截的控制器对象
     */
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
       //通过HttpServletRequest对象来获取session对象.
        Object uid = request.getSession().getAttribute("uid");
       //return true;

        if (uid == null){
            response.sendRedirect("/web/login.html");
             return false;
        }


        return true;



    }
}
```

然后对编写的拦截器进行注册，编写一个config类实现WebMvcConfigurer接口

```java
@Configuration
public class LoginInterceptorConfigurer implements WebMvcConfigurer {


    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        HandlerInterceptor handler = new LoginInterceptor();
        //配置白名单
        List<String> patterns = new ArrayList<>();
        patterns.add("/bootstrap3/**");
        patterns.add("/css/**");
        patterns.add("/images/**");
        patterns.add("/js/**");
        patterns.add("/web/register.html");
        patterns.add("/web/login.html");
        patterns.add("/web/product.html");
        patterns.add("/web/index.html");
        patterns.add("/users/reg");
        patterns.add("/users/login");
        patterns.add("/districts/**");
        registry.addInterceptor(handler)
        .addPathPatterns("/**")
        .excludePathPatterns(patterns);
    }
}
```





