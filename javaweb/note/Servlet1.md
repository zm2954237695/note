## Servlet

maven依赖：

```xml
<dependency>
  <groupId>javax.servlet</groupId>
  <artifactId>javax.servlet-api</artifactId>
  <version>4.0.1</version>
  <scope>compile</scope>
</dependency>
 <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>4.0.1</version>
      <scope>compile</scope>
    </dependency>
```

web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
                      http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0"
         metadata-complete="true">
  <servlet>
    <servlet-name>down</servlet-name>
    <servlet-class>com.guo.servlet.FileServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>down</servlet-name>
    <url-pattern>/down</url-pattern>
  </servlet-mapping>
  <servlet>
    <servlet-name>image</servlet-name>
    <servlet-class>com.guo.servlet.ImageServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>image</servlet-name>
    <url-pattern>/img</url-pattern>
  </servlet-mapping>
  <servlet>
    <servlet-name>re</servlet-name>
    <servlet-class>com.guo.servlet.RedirectServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>re</servlet-name>
    <url-pattern>/re</url-pattern>
  </servlet-mapping>
  <servlet>
    <servlet-name>rr</servlet-name>
    <servlet-class>com.guo.servlet.RequestTest</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>rr</servlet-name>
    <url-pattern>/rr</url-pattern>
  </servlet-mapping>
  <servlet>
    <servlet-name>co</servlet-name>
    <servlet-class>com.guo.servlet.CookieServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>co</servlet-name>
    <url-pattern>/co</url-pattern>
  </servlet-mapping>
<servlet>
<servlet-name>se</servlet-name>
<servlet-class>com.guo.servlet.SessionServlet</servlet-class>
</servlet>
<servlet-mapping>
<servlet-name>se</servlet-name>
<url-pattern>/se</url-pattern>
</servlet-mapping>
</web-app>
```

