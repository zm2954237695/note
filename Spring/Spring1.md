# Spring

###### 	优点：

######                 ①Spring是一个开源的免费的框架（容器）

​                ②Spring是一个轻量级的、非入侵式的框架！

​                ③控制反转(IOC)、面向切面编程(AOP)！

​                ④支持事务的处理，对框架整合的支持！

######   总之：Spring就是一个轻量级的控制反转(IOC)和面向切面编程(AOP)的框架！



## IOC

1.什么是IOC

（1）控制反转，把对象创建和对象之间的调用过程，交给Spring进行管理.

 (2) 目的：为了耦合度降低

2.IOC底层原理

(1)XML解析、工厂模式、反射

导入spring的相关依赖



(2)IOC的过程

1. 编写xml配置文件，配置创建的对象

   ```xml
   <bean id="dao" class="com.x.x"></bean>
   ```

2. 由service类和dao类，创建工厂类

    ```java
    class UserFactory{
        String classValue = class属性值;//xml解析(dom4j)
        Class clazz = Class.forName(classValue);//通过反射获得字节码文件
        return (UserDao)class.newInstance();
    }
    ```

3. IOC(接口)

    (1). IOC思想基于IOC容器完成，IOC容器底层就是对象工厂.

    (2). Spring提供IOC容器实现两种方式:(两个接口)

     1). BeanFactory  : IOC容器实现的基本方式，是Spring内部的使用接口，不提供开发人员进行使用.

      *加载配置文件时不会创建对象，在获取对象时才创建.

     2).ApplicationContext: BeanFactory接口的子接口,提供更多更强大的功能，一般提供给开发人员使用.

     *获取配置文件就创建对象了。

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.3.9</version>
</dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.3.9</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-web</artifactId>
        <version>5.3.9</version>
    </dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>4.3.5.RELEASE</version>
</dependency>
```

spring的配置文件（beans.xml)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd">
    
 
    
</beans>
```

通过<bean> 标签来注入 

ref:引用Spring容器中创建好的对象

value：具体的值

```xml
<bean id="hello" class="com.guo.pojo.Hello">
    <property name="str" value="binbin"/>
</bean>
```

测试代码：

```java
ApplicationContext context =
       new ClassPathXmlApplicationContext("Beans.xml");//spring的上下文
Hello hello = (Hello)context.getBean("hello");
hello.show();
```

（有参）构造器注入

方式一：(如有多个参数类型相同就不适用了)

```xml
<bean id="user" class="com.guo.pojo.User">
    <constructor-arg type="java.lang.String" value="hh"/>
</bean>
```

方式二：通过参数名

```xml
<bean id="user" class="com.guo.pojo.User">
    <constructor-arg name="name" value="hh"/>
</bean>
```

方式三：通过参数下标

```xml
<bean id="user" class="com.guo.pojo.User">
    <constructor-arg index="0" value="hh"/>
</bean>
```

```xml
<alias name="user" alias="usernew"/>
```

别名  在bean标签中的name也可以用来取别名

import 一般用于团队开发，导入多个bean.xml在一个总的xml里面。

```xml
<import resource=""/>
```

set注入：

Bean注入 ：先在容器中注册，再用ref

```xml
<bean id="address" class="com.guo.pojo.Address"/>
<bean id="student" class="com.guo.pojo.People">
    <property name="address" ref="address"/>
```

数组注入：

```xml
<property name="books">
    <array>
        <value>1</value>
        <value>2</value>
    </array>
</property>
```

list注入：

```xml
<property name="hobbys">
    <list>
        <value>1</value>
        <value>2</value>
    </list>
</property>
```

Map注入：

```xml
<property name="card">
    <map>
        <entry key="身份证" value="111111"/>
    </map>
</property>
```

Set注入：

```xml
<property name="games">
    <set>
        <value>11</value>
    </set>
</property>
```

null注入：

```xml
<property name="wife">
    <null/>
</property>
```

properties注入：

```xml
<property name="info">
    <props>
        <prop key="学号">1914</prop>
        <prop key="username">11111</prop>
    </props>
</property>
```

p命名空间：

```xml
xmlns:p="http://www.springframework.org/schema/p"
```

可以直接注入属性的值（相当于set注入）

```xml
<bean id="hello" class="com.guo.pojo.Hello" p: >
```

c命名空间注入：（构造器注入）





scope作用域：

###### singleton 单例 （只产生一个对象）

Bean的实列个数：1个

Bean的实例化时机：当Spring核心文件被加载时，实例化配置的Bean实例。

Bean的生命周期:

- 对象创建：当应用加载，创建容器时，对象就被创建了。
- 对象运行：只要容器在，对象一直活着。
- 对象销毁：当应用卸载，销毁容器时，对象就被销毁了。

###### prototype 原型 (每次从容器get的时候都会产生新对象)

Bean的实列个数：多个

Bean的实例化时机：当调用getBean()方法时实例化Bean

Bean的生命周期:

- 对象创建：当使用对象时，创建新的对象实例。
- 对象运行：只要在使用中，对象一直活着。
- 对象销毁：当对象长时间不使用时，就被java的垃圾回收器回收了。

request:web项目中，Spring创建一个bean的对象，将对象存入到request域中。

session:web项目中，Spring创建一个bean的对象，将对象存入到session域中。



自动装配：

byName：会自动在容器上下文中查找，和自己对象set方法后面的值对应的beanid

byType：会自动在容器上下文中查找，和自己对象属性类型相同的beanid



小结：

byname的时候，需要保证所有的bean的id唯一，并且这个bean需要和自动注入的属性的set方法的值一致

bytype的时候，需要保证所有的bean的class唯一，并且这个bean需要和自动注入的属性的类型一致

```xml
<bean id = "userService" class = "com.guo.dao.impl.UserServiceImpl">
<property name = "userDao" ref = "userDao"></property>
</bean>
```









使用注解开发：

导入支持

```xml
http://www.springframework.org/schema/context
http://www.springframework.org/schema/context/spring-context.xsd
 http://www.springframework.org/schema/aop
 http://www.springframework.org/schema/aop/spring-aop.xsd">
```

```xml
<context:annotation-config/>
```

开启注解支持



@Auaowired（先按类型找再按名字找）

首先在容器中查询对应类型的bean

　　　　如果查询结果刚好为一个，就将该bean装配给@Autowired指定的数据

当然可以使用@Qualifier(value="xxx")进行指定.

　　　　如果查询的结果不止一个，那么@Autowired会根据名称来查找。

　　　　如果查询的结果为空，那么会抛出异常。解决方法时，使用required=false

@Nullable 可以设置对象的值为null。

@Resource 也可以实现自动装配(java的注解) 默认通过byname实现， 找不到就byType ，都找不到就报错。



指定要扫描的包，该包下的注解就会生效

```xml
<context:component-scan base-package="com.guo.pojo"/>
```

@Component:

等价于：

```xml
<bean id="hello" class="com.guo.pojo.Hello">
    <property name="str" value="binbin"/>
</bean>
```

```
@Value("hh")
```

可以给类的属性赋值.



@Repository ,@Service @Controller @Component

这四个注解功能是一样的，都是将某个类注册到Spring中。

@Scope 作用域.



使用Configuration来配置类

```java
@Configuration
public class guoconfig {
    //注册一个bean，就相当于我们之前写的一个bean标签
    //这个方法的名字，就相当于bean标签中的id属性
    //这个方法的返回值，就相当于bean标签中的class属性
    @Bean
    public User getUser(){
        return new User();
    }
}
```

测试代码：

```java
ApplicationContext context = new AnnotationConfigApplicationContext(guoconfig.class);
User user=context.getBean("getUser",User.class);
```

##### Bean的依赖注入概念

依赖注入:它是Spring框架核心IOC的具体实现。

在编写程序时，通过控制反转，把对象的创建交给了Spring，但是代码中不可能出现没有依赖的情况。IOC解耦只是降低他们的依赖关系，但不会消除，例如：业务层仍会调用持久层的方法。

那这种业务层和持久层的依赖关系，在使用Spring之后，就让Spring来维护了。简单的说，就是坐等框架把持久层对象传入业务层，而不用我们自己去获取。

### String相关API

#### ApplicationContext的实现类

1) ClassPathXmlApplicationContext

它是从类的根路径下加载配置文件 推荐使用这种。

2）FileSystemXmlApplicationContext

它是从磁盘路径中加载配置文件，配置文件可以在磁盘的任意位置。

3）AnnotationConfigApplicationContext

当使用注解配置容器对象时，需要使用此类来创建Spring容器。它用来读取注解。





### 数据源（DataSource）

- 数据源(连接池)是提供程序性能而出现的。
- 事先实例化数据源，初始化部分连接资源。
- 使用连接资源时从数据源中获取。
- 使用完毕后将连接资源归还给数据源。



###### 开发步骤

1. 导入数据源的坐标和数据库驱动坐标
2. 创建数据源对象
3. 设置数据源的基本连接数据
4. 使用数据源获取连接和最终释放连接。 

c3p0数据源  ComboPooledDataSource

druid数据源  DruidDataSource



加载jdbc.properties配置文件

```java
ResourceBundle rb = ResourceBundle.getBundle("jdbc");

String driver = rb.getString("jdbc.driver");
```

###### Spring中配置数据源

1. 导入Spring的坐标。

2. 编写spring的xml配置文件，在spring容器中注入数据源的bean。

```xml
<bean id = "dataSource" class = "">
<property name = "driverClass" value = "com.mysql.jdbc.Driver"></property>
    
    <property name = "jdbcUrl" value = "jdbc:mysql://localhost:3306/test"></property>
    <property name = "user" value = "root"></property>
    <property name = "password" value = "root"></property>
</bean>
```

3. 测试Spring容器产生数据源对象，先获取上下文，再进行getBean。

spring加载外部的properties文件

```xml
<context:property-placeholder location="classpath:xx.properties"
```

