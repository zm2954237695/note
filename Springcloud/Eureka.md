#### 微服务

##### 服务拆分

单一职责：不同微服务，不要重复开发相同业务

数据独立：不要访问其它微服务的数据库

面向服务：将自己的业务暴露为接口，供其它微服务调用

##### 远程调用

- 基于RestTemplate发起的http请求实现远程调用
- http请求做远程调用是与语言无关的调用，只要知道对方的ip，端口，接口路径，请求参数即可。

使用时，在主启动类注册bean即可，在需要用时通过autowired自动注入。

```
@Bean
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }
```

![image-20220124162454621](C:\Users\逐梦\AppData\Roaming\Typora\typora-user-images\image-20220124162454621.png)

发送请求，自动序列化为 Java 对象。

**Eureka注册中心**

**order-service 如何得知 user-service 实例地址？**

- user-service 服务实例启动后，将自己的信息注册到 eureka-server(Eureka服务端)，叫做**服务注册**
- eureka-server 保存服务名称到服务实例地址列表的映射关系
- order-service 根据服务名称，拉取实例地址列表，这个叫**服务发现**或服务拉取

## 搭建注册中心

**搭建 eureka-server**

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```

**编写启动类**

注意要添加一个 `@EnableEurekaServer` **注解**，开启 eureka 的**注册中心**功能

```java
@EnableEurekaServer
@SpringBootApplication
@EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})
public class EurekaApplication {
    public static void main(String[] args) {
        SpringApplication.run(EurekaApplication.class,args);
    }
}
```

**编写配置文件**

编写一个 application.yml 文件，内容如下：

```yaml
server:
  port: 10086
spring:
  application:
    name: eureka-server
eureka:
  client:
    service-url: 
      defaultZone: http://127.0.0.1:10086/eureka
```

其中 `default-zone` 是因为前面配置类开启了注册中心所需要配置的 eureka 的**地址信息**，因为 eureka 本身也是一个微服务，这里也要将自己注册进来，当后面 eureka **集群**时，这里就可以填写多个，使用 “,” 隔开。

启动完成后，访问 [http://localhost](http://localhost/):10086/



###### 服务注册

```
将 user-service、order-service 都注册到 eureka
```

引入 SpringCloud 为 eureka 提供的 starter 依赖，注意这里是用 **client**

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

```
在启动类上添加注解：@EnableEurekaClient

在 application.yml 文件，添加下面的配置：
```

```java
spring:
  application:
      #name：orderservice
    name: userservice
eureka:
  client:
    service-url: 
      defaultZone: http:127.0.0.1:10086/eureka
```

可以右键选择复制配置，然后vmoption选项设置为-Dserver.port=8082

重新启动4个项目.

![image-20220124230921566](C:\Users\逐梦\AppData\Roaming\Typora\typora-user-images\image-20220124230921566.png)

##### 负载均衡

只需添加@LoadBalanced，即可实现负载均衡.

**SpringCloud 底层提供了一个名为 Ribbon 的组件，来实现负载均衡功能。**

**SpringCloud 底层提供了一个名为 Ribbon 的组件，来实现负载均衡功能。**

![img](https://cdn.xn2001.com/img/2021/20210901091242.png)

为什么我们只输入了 service 名称就可以访问了呢？显然是有人帮我们根据service名称获取了服务实例的ip和端口。它就是`LoadBalancerInterceptor`，这个类会在对 RestTemplate 的请求进行拦截，然后从 Eureka 根据服务 id 获取服务列表，随后利用负载均衡算法得到真实的服务地址信息，替换服务 id。

![img](https://cdn.xn2001.com/img/2021/20210901091323.png)

这里的 `intercept()` 方法，拦截了用户的 HttpRequest 请求，然后做了几件事：

- `request.getURI()`：获取请求uri，即 http://user-service/user/8
- `originalUri.getHost()`：获取uri路径的主机名，其实就是服务id `user-service`
- `this.loadBalancer.execute()`：处理服务id，和用户请求

这里的 `this.loadBalancer` 是 `LoadBalancerClient` 类型

继续跟入 `execute()` 方法：

![img](https://cdn.xn2001.com/img/2021/20210901091330.png)

- `getLoadBalancer(serviceId)`：根据服务id获取 `ILoadBalancer`，而 `ILoadBalancer` 会拿着服务 id 去 eureka 中获取服务列表。
- `getServer(loadBalancer)`：利用内置的负载均衡算法，从服务列表中选择一个。在图中**可以看到获取了8082端口的服务**

可以看到获取服务时，通过一个 `getServer()` 方法来做负载均衡:

![img](https://cdn.xn2001.com/img/2021/20210901091345.png)

![img](https://cdn.xn2001.com/img/2021/20210901091355.png)

继续跟踪源码 `chooseServer()` 方法，发现这么一段代码：

[![img](https://cdn.xn2001.com/img/2021/20210901091414.png)](https://cdn.xn2001.com/img/2021/20210901091414.png)

我们看看这个 `rule` 是谁：

[![img](https://cdn.xn2001.com/img/2021/20210901091432.png)](https://cdn.xn2001.com/img/2021/20210901091432.png)

这里的 rule 默认值是一个 `RoundRobinRule` ，看类的介绍：

[![img](https://cdn.xn2001.com/img/2021/20210901091442.png)](https://cdn.xn2001.com/img/2021/20210901091442.png)

负载均衡默认使用了轮训算法，当然我们也可以自定义。

## 负载均衡策略

负载均衡的规则都定义在 IRule 接口中，而 IRule 有很多不同的实现类：

## 自定义策略

通过定义 IRule 实现可以修改负载均衡规则，有两种方式：

1 代码方式在 order-service 中的 OrderApplication 类中，定义一个新的 IRule：

[![img](https://cdn.xn2001.com/img/2021/20210901091832.png)](https://cdn.xn2001.com/img/2021/20210901091832.png)

2 配置文件方式：在 order-service 的 application.yml 文件中，添加新的配置也可以修改规则：

```yml
userservice: # 给需要调用的微服务配置负载均衡规则，orderservice服务去调用userservice服务
  ribbon:
    NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule # 负载均衡规则 
```

**注意**：一般用默认的负载均衡规则，不做修改。

## 饥饿加载

当我们启动 orderservice，第一次访问时，时间消耗会大很多，这是因为 Ribbon 懒加载的机制。