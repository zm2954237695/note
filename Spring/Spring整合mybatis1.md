### Spring整合mybatis



依赖:

```xml
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-spring</artifactId>
    <version>2.0.0</version>
</dependency>
```

spring-dao.xml:(也是spring的配置文件)

配置dataSource：

```xml
<bean id="datasource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
    <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
    <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;"/>
    <property name="username" value="root"/>
    <property name="password" value="guoyibin"/>
</bean>
```

配置sqlSessionFactory:

```xml
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="datasource"/>
    <property name="configLocation" value="classpath:mybatis.xml"/>
    <property name="mapperLocations" value="classpath:com/guo/dao/*.xml"/>
</bean>
<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
    <constructor-arg index="0" ref="sqlSessionFactory"/>
</bean>
```

配置实现类：

```xml
<bean id="userDao" class="com.guo.dao.UserDaoimpl">
    <property name="sqlSession" ref="sqlSession"/>
</bean>
```

UserDaoimpl.java

```java
package com.guo.dao;

import com.guo.pojo.User;
import org.mybatis.spring.SqlSessionTemplate;

import java.util.List;

public class UserDaoimpl implements UserDao{

    private SqlSessionTemplate sqlSession;



    @Override
    public List<User> selectUser() {
        return sqlSession.getMapper(UserDao.class).selectUser();
    }

    public void setSqlSession(SqlSessionTemplate sqlSession) {
        this.sqlSession = sqlSession;
    }
}
```

另一种方法整合实现：

UserDaoimpl2.java



```java
package com.guo.dao;

import com.guo.pojo.User;
import org.apache.ibatis.session.SqlSession;
import org.mybatis.spring.support.SqlSessionDaoSupport;

import java.util.List;

public class UserDaoimpl2 extends SqlSessionDaoSupport implements UserDao{
    @Override
    public List<User> selectUser() {
     return  getSqlSession().getMapper(UserDao.class).selectUser();
    }
}
```

