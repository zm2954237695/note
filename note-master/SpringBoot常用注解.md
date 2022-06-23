#### SpringBoot常用注解

#####  @SpringBootApplication:启动注解

   @SpringBootConfiguration:继承@Configuration,主要用于加载配置文件

  @EnableAutoConfiguration:开启自动配置功能

  @ComponentScan 主要用于组件扫描和自动装配

##### Controller相关注解

@Controller 控制器，处理Http请求

@RestController 将方法返回的对象直接在浏览器上展示成json格式

@RequestBody 通过HttpMessageConverter读取Request Body并反序列化为Object对象

@RequestMapping 将Http请求映射到MVC和REST控制器的处理方法上

@GetMapping 将Http get请求映射到特定处理程序

@PostMapping  将Http post请求映射到特定处理程序

##### 取请求参数值

@PathVariable: 获取url中的数据

@RequestParam: 获取请求参数的值

@RequestHeader 把Request请求header部分的值绑定到方法的参数上

@CookieValue 把Request请求中关于cookie的值绑定到方法的参数上。

##### 注入bean相关









