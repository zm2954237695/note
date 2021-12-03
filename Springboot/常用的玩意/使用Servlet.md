## 使用Servlet

编写MyServlet类继承父类HttpServlet，重写doGet和doPost方法

```java
public class MyServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doPost(req,resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.setContentType("text/html;charset=utf-8");
        PrintWriter out = resp.getWriter();
        out.println("执行的是Servlet");
        out.flush();
        out.close();

    }
}
```

对编写的servlet类进行配置。编写WebApplicationConfig 类，创建一个方法，该方法返回值为ServletRegistrationBean对象。new一个ServletRegistrationBean对象，用该类的有参构造方法，第一个参数是要注册的servlet对象，第二个参数是url地址。

```java
@Configuration
public class WebApplicationConfig {
    @Bean
    public ServletRegistrationBean servletRegistrationBean(){
        return new ServletRegistrationBean(new MyServlet(),
                "/myservlet");
    }
}
```

