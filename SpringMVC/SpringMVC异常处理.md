#### SpringMVC异常处理

##### 简单异常处理器 

```xml
  <bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
       <property name="defaultErrorView" value="error"/> <!-- 默认错误视图 -->
          <property name="exceptionMappings">
              <map>
                  <entry key="com.guo" value="error"/> <!--异常类型 错误视图 -->
                  <entry key="java.lang.ClassCastException" value="error"/>
              </map>
          </property>
    </bean>
```

##### 自定义异常处理步骤

1. 创建异常处理器类实现HandlerExceptionResolver接口
2. 配置异常处理器。
3. 编写异常页面
4. 相应页面跳转。



```java
public class MyExceptionResolver implements HandlerExceptionResolver {

    @Override
    public ModelAndView resolveException(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) {
        ModelAndView mv = new ModelAndView();
        if (e instanceof MyException){
            dosomethig;
        }else if(e instanceof ClassCastException){
            do somthing;
        }
        return mv;
    }
}
```



