##### SpringBoot 起步依赖分析

**1、POM文件**

#### 1、父项目

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.5.9.RELEASE</version>
</parent>

他的父项目是
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-dependencies</artifactId>
  <version>1.5.9.RELEASE</version>
  <relativePath>../../spring-boot-dependencies</relativePath>
</parent>
他来真正管理Spring Boot应用里面的所有依赖版本；  

```

Spring Boot的版本仲裁中心；

以后我们导入依赖默认是不需要写版本；（没有在dependencies里面管理的依赖自然需要声明版本号）



#### 2、启动器

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency> 

```

**spring-boot-starter**-web：

spring-boot-starter：spring-boot场景启动器；帮我们导入了web模块正常运行所依赖的组件；

Spring Boot将所有的功能场景都抽取出来，做成一个个的starters（启动器），只需要在项目里面引入这些starter相关场景的所有依赖都会导入进来。要用什么功能就导入什么场景的启动器 

**2、主程序类，主入口类**

```java
/**
 *  @SpringBootApplication 来标注一个主程序类，说明这是一个Spring Boot应用
 */
@SpringBootApplication
public class HelloWorldMainApplication {

    public static void main(String[] args) {

        // Spring应用启动起来
        SpringApplication.run(HelloWorldMainApplication.class,args);
    }
}  

```

@**SpringBootApplication**: Spring Boot应用标注在某个类上说明这个类是SpringBoot的主配置类，SpringBoot就应该运行这个类的main方法来启动SpringBoot应用；

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = {
      @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
      @Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication { 

```

@**SpringBootConfiguration**:Spring Boot的配置类；

 标注在某个类上，表示这是一个Spring Boot的配置类；

@**Configuration**:配置类上来标注这个注解；

 配置类 ----- 配置文件；配置类也是容器中的一个组件；@Component

@**EnableAutoConfiguration**：开启自动配置功能；

 以前我们需要配置的东西，Spring Boot帮我们自动配置；@**EnableAutoConfiguration**告诉SpringBoot开启自动配置功能；这样自动配置才能生效；

```java
@AutoConfigurationPackage
@Import(EnableAutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration { 

```

@AutoConfigurationPackage：自动配置包  @Import(AutoConfigurationPackages.Registrar.class)： 

Spring的底层注解@Import，给容器中导入一个组件；导入的组件由AutoConfigurationPackages.Registrar.class；

将主配置类（@SpringBootApplication标注的类）的所在包及下面所有子包里面的所有组件扫描到Spring容器；



@**ImportResource**：导入Spring的配置文件，让配置文件里面的内容生效；

Spring Boot里面没有Spring的配置文件，我们自己编写的配置文件，也不能自动识别；

想让Spring的配置文件生效，加载进来；@**ImportResource**标注在一个配置类上

```java
@ImportResource(locations = {"classpath:beans.xml"})
导入Spring的配置文件让其生效 

```

@**PropertySource**：加载指定的配置文件；

```java
/**
 * 将配置文件中配置的每一个属性的值，映射到这个组件中
 * @ConfigurationProperties：告诉SpringBoot将本类中的所有属性和配置文件中相关的配置进行绑定；
 *      prefix = "person"：配置文件中哪个下面的所有属性进行一一映射
 *
 * 只有这个组件是容器中的组件，才能容器提供的@ConfigurationProperties功能；
 *  @ConfigurationProperties(prefix = "person")默认从全局配置文件中获取值；
 *
 */
@PropertySource(value = {"classpath:person.properties"})
@Component
@ConfigurationProperties(prefix = "person")
//@Validated
public class Person {

    /**
     * <bean class="Person">
     *      <property name="lastName" value="字面量/${key}从环境变量、配置文件中获取值/#{SpEL}"></property>
     * <bean/>
     */

   //lastName必须是邮箱格式
   // @Email
    //@Value("${person.last-name}")
    private String lastName;
    //@Value("#{11*2}")
    private Integer age;
    //@Value("true")
    private Boolean boss;  

```

SpringBoot推荐给容器中添加组件的方式；推荐使用全注解的方式

1、配置类**@Configuration**------>Spring配置文件

2、使用**@Bean**给容器中添加组件

```java
/**
 * @Configuration：指明当前类是一个配置类；就是来替代之前的Spring配置文件
 *
 * 在配置文件中用<bean><bean/>标签添加组件
 *
 */
@Configuration
public class MyAppConfig {

    //将方法的返回值添加到容器中；容器中这个组件默认的id就是方法名
    @Bean
    public HelloService helloService02(){
        System.out.println("配置类@Bean给容器中添加组件了...");
        return new HelloService();
    }
} 

```



##### 小结

- 在Spring-boot-starter-parent中定义了各种技术的版本信息，组合了一套最优搭配的技术版本。
- 在各种starter中，定义了完成该功能需要的坐标合集，其中大部分版本信息来自父工程。
- 我们的工程继承parent，引入starter后，通过依赖传递，就可以简单方便获得需要的jar包，并且不会存在版本冲突等问题。

##### 读取配置内容

1) @Value
2) Environment(通过Autowired注入)
3) @ConfigrationProperties

##### 内部配置加载顺序

SpringBoot启动时，会从以下位置加载配置文件：

1. file:./config/:当前项目下的/config目录

2. file:./config/*/

3. file:../：   当前项目的根目录

4. classpath:/config/:     classpath的/config目录

5. classpath/:classpath下的根目录.

```
注：file: 指当前项目根目录；classpath: 指当前项目的类路径，即 resources 目录。
```

以上所有位置的配置文件都会被加载，且它们优先级依次降低，序号越小优先级越高。其次，位于相同位置的 application.properties 的优先级高于 application.yml。

所有位置的文件都会被加载，高优先级配置会覆盖低优先级配置，形成互补配置，即：

- 存在相同的配置内容时，高优先级的内容会覆盖低优先级的内容；
- 存在不同的配置内容时，高优先级和低优先级的配置内容取并集。

# Spring Boot外部配置文件

- spring.config.location 

- spring.config.additional-location 

  这两个参数可以指定外部配置文件的路径

通过命令行

```bash
java -jar springboot.jar --spring.config.location = D:\myconfig\xx.yml
```



##### Condition 小结

- 自定义条件:
  1) 定义条件类：自定义条件类实现Condition接口，重写matches方法,在matches方法中进行逻辑判断，返回boolean值。matches有两个参数:
     - context:上下文对象，可以获取属性值，获取类加载器，获取BeanFactory等。
     - metadata:元数据对象，用于获取注解属性。
- SpringBoot提供的常用条件注解：
  - ConditionalOnProperty:判断配置文件中是否有对应属性和值才初始化Bean
  - ConditionalOnClass:判断环境中是否有对应的字节码文件才初始化bean
  - ConditionalOnMissingBean：判断环境中没有对应Bean才初始化Bean。

# Spring Boot Profile（多环境配置）

## 多 Profile 文件方式

Spring Boot 的配置文件共有两种形式：.properties 文件和 .yml 文件，不管哪种形式，它们都能通过文件名的命名形式区分出不同的环境的配置，文件命名格式为：

```java
application-{profile}.properties/yml
```

其中，{profile} 一般为各个环境的名称或简称，例如 dev、test 和 prod 等等。

#### properties 配置

在 helloworld 的 src/main/resources 下添加 4 个配置文件：

- application.properties：主配置文件
- application-dev.properties：开发环境配置文件
- application-test.properties：测试环境配置文件

在 applcation.properties 文件中，指定默认服务器端口号为 8080，并通过以下配置激活生产环境（prod）的 profile

```properties
#默认端口号
server.port=8080

#激活指定的profile
spring.profiles.active=dev
```

在 application-dev.properties 中，指定开发环境端口号为 8081，配置如下

```properties
# 开发环境
server.port=8081
```

这时配置就会加载dev.properties.

## 激活 Profile

除了可以通过在配置文件中激活外，可以通过命令行激活

###### 命令行

```bash
来到该项目jar文件所在目录
jvar -jar hello.jar --spring.profiles.active=dev
```

###### 虚拟机参数激活

```bash
javar -Dspring.profiles.active=prod -jar hello.jar
```

## 日志框架的选择

市面上常见的日志框架有很多，它们可以被分为两类：日志门面（日志抽象层）和日志实现，如下表。
 

| 日志分类               | 描述                                                         | 举例                                                         |
| ---------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 日志门面（日志抽象层） | 为 Java 日志访问提供一套标准和规范的 API 框架，其主要意义在于提供接口。 | JCL（Jakarta Commons Logging）、SLF4j（Simple Logging Facade for Java）、jboss-logging |
| 日志实现               | 日志门面的具体的实现                                         | Log4j、JUL（java.util.logging）、Log4j2、Logback             |

Spring Boot 选用 SLF4J + Logback 的组合来搭建日志系统。

## SLF4J 的使用

在项目开发中，记录日志时不应该直接调用日志实现层的方法，而应该调用日志门面（日志抽象层）的方法。

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class HelloWorld {
    public static void main(String[] args) {
        Logger logger = LoggerFactory.getLogger(HelloWorld.class);
       //调用 sl4j 的 info() 方法，而非调用 logback 的方法
        logger.info("Hello World");
    }
}
```

SpringBoot 底层使用 slf4j+logback 的方式记录日志，当我们引入了依赖了其他日志框架的第三方框架（例如 Hibernate）时，只需要把这个框架所依赖的日志框架排除，即可实现日志框架的统一，示例代码如下。

```xml
<dependency>
    <groupId>org.apache.activemq</groupId>
    <artifactId>activemq-console</artifactId>
    <version>${activemq.version}</version>
    <exclusions>
        <exclusion>
            <groupId>commons-logging</groupId>
            <artifactId>commons-logging</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

# Spring Boot静态资源映射

所有通过 WebJars 引入的前端资源都存放在当前项目类路径（classpath）下的“/META-INF/resources/webjars/” 目录中。

Spring Boot 通过 MVC 的自动配置类 WebMvcAutoConfiguration 为这些 WebJars 前端资源提供了默认映射规则。

