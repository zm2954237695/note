##### tomcat设置编码

tomecat8之前，设置编码：

1）get请求方式:

```javascript
String frame =         request.getParameter("frame");
byte[] bytes = frame.getByte("ISO-8859-1");
frame = new String(bytes,"UTF-8");


```

2)post请求方式：

```java
request.setCharacterEncoding("UTF-8");
tomcat8之后 只需要针对post方式
request.setCharacterEncoding("UTF-8");
```

##### HttpServlet

1) 继承关系:  HttpServlet->GenericServlet->Servlet
2) Servlet中的核心方法: init() , service() ,destory()
3) 服务方法: 当有请求过来时，service方法会自动响应(其实是tomcat容器调用的)。

```
servlet是单例的，线程不安全的
```

##### Http协议

1)超文本传输协议，是无状态的。

无状态是指服务器无法判断这两次请求是同一个客户端发来的还是不同客户端发来的。（通过会话跟踪技术解决).

2）Http请求响应包含两个部分：请求和响应

- 请求：

   请求包含三个部分: 1. 请求行 2. 请求信息头 3.请求主体

  1）请求行包含三个信息：1. 请求的方式 2. 请求的URL 3. 请求的协议 (一般是Http1.1)

  

  2）请求信息头包含了很多客户端需要告诉服务器的信息,比如浏览器型号，版本，能接收的内容的类型等。

  3）请求体，三种情况

  get方式,没有请求体,但有一个queryString

  post方式，有请求体，form data

  json格式,有请求体，request payload

- 响应:

   包含三部分:响应行，响应头，响应体

  1） 响应行包含三个信息:  协议，响应状态码，响应状态

  2） 响应头：包含了服务器的信息，服务器发送给浏览器的信息(内容的媒体类型，编码，内容长度等)

  3）响应头: 响应的实际内容(如请求add.html时，响应的内容是<html>...</html>)

##### 会话跟踪技术

- 客户端第一次发请求给服务器时，服务器获取session,若获取不到，则创建新的，然后响应给客户端。
- 下次客户端给服务器发请求时，会把sessionID带给服务器，那么服务器就能获取到了，那么服务器就根据此sessionID判断这次请求和上次请求的客户端是否是同一个,从而区分出状态。 

