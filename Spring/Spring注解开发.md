### Spring注解开发

**开启组件扫描**.

```xml
<context:component-scan base-package="com.guo"></context:component-scan>
```

![img](https://img2020.cnblogs.com/blog/1894435/202008/1894435-20200809224304574-1961982060.png)

Spring的新注解

```java
@Configuration	声明该文件是Spring核心/配置文件->.可以定义多个配置类

@ComponentScan	声明该配置文件的组件扫描范围(包)
		@ComponentScan("com.itheima")

@PropertySource	定义properties配置文件,读取文件信息 ->声明该类将会引入该properties文件
		@Properties("classpath:jdbc.properties") 该类引入jdbc.properties文件

@Import		引入其他的配置文件或者配置类
		@Import(JdbcContextConfiguration.class) 引入该@JdbcContextConfiguration类(分配置类)
		@Import(xxx),需要引入多个时候,使用多个@Import注解

@Bean		非用户自定义的Bean,用于方法上,标注该方法的返回值将会存储到Spring容器中
		@Bean("id"),暂时可以设想成另类的@Componen
```

##### Spring 集成Junit步骤

1. 导入spring集成junit的坐标
2. 使用@Runwith注解替换原来的运行期。
3. 使用@ContextConfiguration指定配置文件或者配置类
4. 使用@Autowired注入需要测试的对象。
5. 创建测试方法进行测试。

##### Spring集成web的监听器。

1. 导入监听器的坐标（配置ContextLoaderListener监听器)
2. 使用WebApplicationContextUtils获得应用上下文对象
