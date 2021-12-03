# JavaWeb

# 1、基本概念

Web开发：

- web，网页的意思，www.baidu.com
- 静态web
  - html，css
  - 提供给所有人看的数据始终不会发生变化！
- 动态web
  - 淘宝，几乎是所有的网站；
  - 提供给所有人看的数据始终会发生变化，每个人在不同的事件，不同的地点看到的信息各不相同；
  - 技术栈：Servlet/JSP，ASP，PHP

在java中，动态web资源开发的技术统称为javaWeb；

## 1.2、web应用程序

web应用程序，可以提供游览器访问的程序；

- a.html、b.html….多个web资源，这些web资源可以被外界访问，对外界提供服务；
- 你们能访问到的任何一个页面或者资源，都存在于这个世界的某一个角落的计算机上。
- URL
- 这个统一的web资源会被放在同一个文件夹下，web应用程序—>Tomcat：服务器
- 一个web应用有多部分组成（静态web，动态web）
  - html，css，js
  - jsp，servlet
  - java程序
  - jar包
  - 配置文件（Properties）

web应用程序编写完毕后，若想提供给外界访问：需要一个服务器来统一管理；

## 1.3、静态web

- *.html* .htm,这些都是网页的后缀，如果服务器上一直存在这些东西，我们就可以直接进行读取。通过网络；

![img](https://kuangstudy.oss-cn-beijing.aliyuncs.com/bbs/2021/08/13/kuangstudyb390ed40-1ec1-483f-838e-b39896f3fb2f.png)静态web存在的缺点

- Web页面无法动态更新，所有用户看到都是同一个页面
  - 轮播图，点击特效：伪动态
  - JavaScript[实际开发中，它用的最多]
  - VBScript
- 它无法和数据库交换（数据无法持久化，用户无法交互）

## 1.4、动态web

页面会动态展示：”Web页面展示的效果因人而异”；

![img](https://kuangstudy.oss-cn-beijing.aliyuncs.com/bbs/2021/08/13/kuangstudye88c032c-6258-4e5a-b335-7115e8163d3e.png)

缺点

- 假如服务器的动态web资源出现了错误，我们需要重写编写我们的后台==后台程序==，重新发布
  - 停机维护

优点

- Web页面可以动态更新，所有用户看到都是不同的页面

- 它可以和数据库交换（数据持久化：注册，商品信息，用户信息….）

- ![img](https://kuangstudy.oss-cn-beijing.aliyuncs.com/bbs/2021/08/13/kuangstudy30c59e82-d27b-43be-b5da-ac8bffb6f8bc.png)

  

  ## 3.2、Tomcat启动和配置

  bin    启动，关闭的脚本文件

  conf    配置

  lib    以来的jar包

  logs    日志

  temp    临时文件

  webapps    存放网站的

  work    工作目录

  ==启动.关闭Tomcat==



请你谈谈网站时如何进行访问的！

1.输入一个域名；回车

2.检查本机的hosts配置文件下有没有这个域名映射；

​    1.有：直接返回对应的ip地址

​        127.0.0.1    www.ding.com

​    2.没有：去DNS服务器找，找到的话就返回，找不到就返回找不到；



## 4、Http

### 4.1、什么是HTTP

超文本传输协议（Hyper Text Transfer Protocol，HTTP）是一个简单的请求-响应协议，通常运行再TCP之上。

- 文本： html，字符串，~….
- 超文本：图片，音乐，视频….
- 80
  Https:安全的
- 443

### 4.2、两个时代

- http1.0
  - HTTP/1.0:客户端可以与web服务器连接，只能获得一个web资源，断开连接，如果还要用就要再请求
- http2.0
  - HTTP/1.1:客户端可以与web服务器连接，只能获得多个web资源。

### 4.3、Http请求

- 客户端—发请求（Request）—服务器

  百度

  ```java
  request url:https://www.baidu.com/ 请求地址
  request method：get   get方法/post方法
  status code：200 ok    状态码：200
  remote（远程）      address：14.215.177.39:443
  ```

  ```
  Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9Accept-Encoding: gzip, deflate, brAccept-Language: zh-CN,zh;q=0.9Connection: keep-aliveCookie: BIDUPSID=B09E488F874449A718873CB696812359; PSTM=1626143686;Host: www.baidu.com
  ```

#### 1、请求行

- 请求行中的请求方式： GET

- 请求方式：Get，Post，HEAD,DELETE,PUT,TRACT…

  - get：请求能够携带的参数比较少，大小有限制，会再游览器的url地址栏显示数据内容，不安全，但高效

  - post：请求能够携带的参数没有限制，大小没有限制，不会再游览器的url地址栏显示数据内容，安全，但不高效

    #### 2.消息头

    ```
    Accept:  告诉游览器，它支持的数据类型
    Accept-Encoding: 告诉游览器，支持哪种编码格式
    Accept-Language: 支持的语言
    Connection:        告诉游览器，请求完成是断开还是保持连接
    Cache-Control  缓存控制
    HOST:            主机
    ```

### 4.4、Http相应

- 服务器—-相应—-客户端

  百度：

  ```
  Cache-Control: private    #缓存控制Connection: keep-alive    #连接：保持连接Content-Encoding: gzip    #编码Content-Type: text/html;charset=utf-8 #类型
  ```

#### 1、响应体

```java
Accept:  告诉游览器，它支持的数据类型
Accept-Encoding: 告诉游览器，支持哪种编码格式
Accept-Language: 支持的语言
Connection:        告诉游览器，请求完成是断开还是保持连接
Cache-Control  缓存控制
HOST:            主机
refresh： 告诉客户端，多久刷新一次
location： 定位让网页重新定位：
```

#### 2、相应状态码

200： 请求响应成功
300： 请求重定向

- 重定向： 你重新到我给你的新位置去；

404： 请求资源失败

- 资源不存在

500： 服务器代码错误
502： 网关错误
常见面试题：
当你的游览器中地址栏输入地址并回车的一瞬间到页面能够展示回来，经历了什么？