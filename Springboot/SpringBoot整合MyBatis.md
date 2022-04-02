##### SpringBoot整合MyBatis

导入MyBatis和Mysql的依赖

```xml
  <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.47</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.5</version>
        </dependency>
```

##### 在启动类配置DAO扫描

```java
@MapperScan
```

##### 创建Thymeleaf模板

- springboot应用中resoureces\templates目录就是用来存放页面模板的
- 重要说明:
  - static目录下的资源被定义为静态资源，Springboot默认放行；如果将HTML页面创建static目录是可以直接访问的。
  - templates目录下的文件会被定义为动态网页模板，Springboot应用会拦截templates中定义的资源;如果将HTML文件定义在templates目录，则必须通过控制器跳转访问。



##### 关于Thymeleaf的使用

引入命名空间

```html
<html lang=en xmlns:th=http://www.thymeleaf.org >
```

th:text

在几乎所有HTML双标签都可以使用，将接收到的数据显示在标签的内容中

th:inline 内联

- HTML内联

   ```html
   <p th:inline = "text">图书名称:[[${book.bookName}]]</p>
   ```

- CSS内联

   ```html
   <style type = "text/css" th:inline="css">
   .style{
       color:[[${color}]]
   }
   </style>
   ```

  循环

  ```html
  <tr th:each="b:${books}">
     <td th:text="${bookId}"></td>
  ```

  