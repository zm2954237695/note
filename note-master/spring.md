##### Spring

spring的注入方式

1. Setter注入

   ```xml
   <bean id="notify" class="twm.spring.start.NotifyServiceByCellPhoneImpl" />
   <bean id="order" class="twm.spring.start.Order" >
       <!-- 配置要注入的对象 -->
       <property name="notifyservice" ref="notify"/>
   </bean>
   ```

   property标签的name熟悉为该bean中set方法名称去掉set后面的部分，ref为要注入的对象，因为是对象类型，如果是基本类型，用value。

   property name只和注入方法名相关，和内部属性名没有半毛关系

   

   2. 构造器注入

   ![image-20220429192843454](C:\Users\逐梦\AppData\Roaming\Typora\typora-user-images\image-20220429192843454.png)

   1. 智能识别

      ![image-20220429193005377](C:\Users\逐梦\AppData\Roaming\Typora\typora-user-images\image-20220429193005377.png)

   2. 指明参数类型

      ```xml
      <bean id="order" class="twm.spring.start.Order" >
          <constructor-arg type="String" value="张老三"></constructor-arg>
          <constructor-arg type="twm.spring.start.NotifyService" ref="notify"></constructor-arg>
          <constructor-arg type="String" value="1234567"></constructor-arg>
      </bean>
      ```

      **3、指定参数名**

      ```xml
      <bean id="order" class="twm.spring.start.Order" >
          <constructor-arg type="String" value="张老三"></constructor-arg>
          <constructor-arg type="twm.spring.start.NotifyService" ref="notify"></constructor-arg>
          <constructor-arg type="String" value="1234567"></constructor-arg>
      </bean>
      
      ```

   4.  **指定索引index**

      ```xml
      <bean id="order" class="twm.spring.start.Order" >
          <constructor-arg index="0" value="张老三"></constructor-arg>
          <constructor-arg index="2" ref="notify"></constructor-arg>
          <constructor-arg index="1" value="2017877997"></constructor-arg>
      </bean>
      ```

## 静态工厂的方法注入

```xml
<bean id="order" class="twm.spring.start.Order">
    <constructor-arg name="username" value="张老三"></constructor-arg>
    <constructor-arg name="notifyservice1" ref="notify2"></constructor-arg>
    <constructor-arg name="orderno" value="1234567"></constructor-arg>
</bean>

<bean id="notify2" class="twm.spring.start.NotifyFactory"
    factory-method="getNotifyService" />
```

## 实例工厂的方法注入

```xml
<bean id="order" class="twm.spring.start.Order">
    <constructor-arg name="username" value="张老三"></constructor-arg>
    <constructor-arg name="notifyservice1" ref="notify2"></constructor-arg>
    <constructor-arg name="orderno" value="1234567"></constructor-arg>
</bean>


<bean id="notifyfactory" class="twm.spring.start.NotifyFactory" />
<bean id="notify2" factory-bean="notifyfactory" factory-method="getNotifyService" />
```

##### SpringBoot相对Spring的优势

- 遵循"习惯优于配置"原则，springboot只需要很少的配置
- 可以完全不使用xml文件配置，只使用自动配置和JAVA Config即可
- 内嵌的Tomcat服务器，不需要额外配置服务器
- 提供的starters简化构建配置，减少了依赖冲突的问题
- 项目可以快速构建，另外还可以更加简单的配置整合第三方框架
  
