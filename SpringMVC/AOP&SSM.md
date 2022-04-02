### Spring的AOP

##### 基于jdk的动态代理

目标类

```java
package com.guo.proxy.jdk;

public class Target implements TargetInterface{
    @Override
    public void save() {
        System.out.println("save");
    }
}

```

通知

```java
package com.guo.proxy.jdk;

public class Advice {
    public void before(){
        System.out.println("before..");
    }
    public void afterReturning(){
        System.out.println("After..");
    }
}
```

目标接口

```java
package com.guo.proxy.jdk;

public interface TargetInterface {
    void save();
}

```

实现动态代理

```java
package com.guo.proxy.jdk;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

public class ProxyTest {
    public static void main(String[] args) {
        Target target = new Target();
        Advice ad = new Advice();
       TargetInterface  it=(TargetInterface) Proxy.newProxyInstance(target.getClass().getClassLoader(), target.getClass().getInterfaces(),
                new InvocationHandler() {
                    @Override
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        Object invoke = method.invoke(target, args);
                        ad.afterReturning();
                        return invoke;
                    }
                });
        it.save();
    }
}

```

基于cglib的动态代理

```java
package com.guo.proxy.cglib;

import com.guo.proxy.jdk.TargetInterface;
import org.springframework.cglib.proxy.Enhancer;
import org.springframework.cglib.proxy.MethodInterceptor;
import org.springframework.cglib.proxy.MethodProxy;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

public class ProxyTest {
    public static void main(String[] args) {
        Target target = new Target();
        Advice ad = new Advice();
        Enhancer enhancer = new Enhancer();
        enhancer.setSuperclass(Target.class);
        enhancer.setCallback(new MethodInterceptor() {
            @Override
            public Object intercept(Object proxy, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
                ad.before();
                Object invoke = method.invoke(target, args);
                ad.afterReturning();
                return null;
            }
        });
        Target proxy = (Target) enhancer.create();
        proxy.save();
    }
}

```

spring中使用AOP：

导入命名空间，编写下面的配置

```xml
 <aop:config>
       <aop:aspect ref="advice">
           <aop:before method="before" pointcut="execution(public void com.guo.aop.Target.save())"></aop:before>
       </aop:aspect>
   </aop:config>
```

##### 切点表达式的写法

```xml
execution([修饰符]返回值类型 包名.类名.方法名(参数))
```

###### ps:

- 返回值类型，包名，类名，方法名可以使用*代表任意
- 包名与类名之间有一个点，代表当前包下的类，两个点代表当前包及其子包下的类
- 参数列表可以使用两个点..表示任意个数，任意类型的参数列表。





#### SSM整合步骤

##### 1.1 准备工作

###### 1.原始方式整合

sql编写，创建数据库。

###### 2 创建Maven工程

###### 3 导入maven坐标

###### 4 编写对应的实体类

###### 5 编写mapper接口(dao层)

###### 6 编写service接口(service层)

###### 7编写service接口实现

###### 8 编写controller

###### 9 编写添加页面

###### 10编写页面列表

###### 11编写相应配置文件

- Spring配置文件：application.xml
- SpringMVC配置文件:spring-mvc.xml
- MyBatis映射文件:AccountMapper.xml
- MyBatis核心文件:sqlMapConfig.xml
- 数据库连接信息文件:jdbc.properties
- web.xml文件：web.xml
- 日志文件：log4j.xml

###### 12测试账号列表

