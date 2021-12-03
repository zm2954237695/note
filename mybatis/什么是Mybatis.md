## 什么是Mybatis

- MyBatis 是一款优秀的持久层框架

- 它支持自定义 SQL、存储过程以及高级映射。

- MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。

- MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。

- MyBatis 本是apache的一个开源项目iBatis, 2010年这个项目由apache software foundation 迁移到了google code，并且改名为MyBatis。

- 2013年11月迁移到Github。

  maven仓库

  ```xml
  <dependency>  
      <groupId>org.mybatis</groupId>  <artifactId>mybatis</artifactId>  <version>3.5.7</version>
  </dependency>
  ```

mybatis-config.xml

~~~xml
```
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE configuration
          PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-config.dtd">
    <configuration>
      <environments default="development">
          <environment id="development">
              <transactionManager type="JDBC"/>
              <dataSource type="POOLED">
                  <property name="driver" value="com.mysql.jdbc.Driver"/>
                  <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=utf-8"/>
                  <property name="username" value="root"/>
                  <property name="password" value="123456"/>
              </dataSource>
          </environment>
      </environments>
      <mappers>
          <mapper resource="com/guan/dao/UserMapper.xml"></mapper>
      </mappers>
    </configuration>
```
~~~

编写mybatis工具类

```java
//sqlSessionFactory----construct---->sqlSession
public class MybatisUtils {
  private static SqlSessionFactory sqlSessionFactory;
  static {
      //使用Mybatis第一步,获取sqlSessionFactory对象
      try {
          String resource = "mybatis-config.xml";
          InputStream inputStream = Resources.getResourceAsStream(resource);
          sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
      } catch (IOException e) {
          e.printStackTrace();
      }
  }
  //既然有了 SqlSessionFactory，顾名思义，我们可以从中获得 SqlSession 的实例
  //SqlSession 提供了在数据库执行 SQL 命令所需的所有方法
  //你可以通过 SqlSession 实例来直接执行已映射的 SQL 语句
  public static SqlSession getSqlSession() {
      return sqlSessionFactory.openSession();
  }
}
​```
```



编写java代码：

```java
public class User {
  private int id;
  private String name;
  private String pwd;
  public User() {}
  public User(int id, String name, String pwd) {
      this.id = id;
      this.name = name;
      this.pwd = pwd;
  }
  public int getId() {
      return id;
  }
  public void setId(int id) {
      this.id = id;
  }
  public String getName() {
      return name;
  }
  public void setName(String name) {
      this.name = name;
  }
  public String getPwd() {
      return pwd;
  }
  public void setPwd(String pwd) {
      this.pwd = pwd;
  }
  @Override
  public String toString() {
      return "User{" +
              "id=" + id +
              ", name='" + name + '\'' +
              ", pwd='" + pwd + '\'' +
              '}';
  }
}
```

Dao接口

UserDao.class

```java
public interface UserDao {  List<User> getUserList();}
```

<!DOCTYPE configuration
      PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
      "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <environments default="development">
      <environment id="development">
          <transactionManager type="JDBC"/>
          <dataSource type="POOLED">
              <property name="driver" value="com.mysql.jdbc.Driver"/>
              <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=utf-8"/>
              <property name="username" value="root"/>
              <property name="password" value="123456"/>
          </dataSource>
      </environment>
  </environments>
  <mappers>
      <mapper resource="com/guan/dao/UserMapper.xml"></mapper>
  </mappers>
</configuration>

UserDao.xml



```xml
<!DOCTYPE mapper
      PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
      "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.guan.dao.UserDao">
  <select id="getUserList" resultType="com.guan.pojo.User">
      select *
      from `mybatis`.`user`;
  </select>
</mapper>
```

注意点：
Type interface com.guan.dao.UserDao is not known to the MapperRegistry
MapperRegistry：每一个Mapper.xml都需要在Mybatis核心配置文件中注册

maven配置文件到处问题
由于maven约定大于配置，可能遇到配置文件无法被导出或生效，结局方案

```xml
<build>
        <resources>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>
        </resources>
</build>
```

测试代码：



```java
public class UserDaoTest {
    @Test
    public void test() {
        //获取sqlSession对象
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        //方式一：执行SQL
        UserDao mapper = sqlSession.getMapper(UserDao.class);
        List<User> userList = mapper.getUserList();
        for (User user : userList) {
            System.out.println(user);
        }
        //关闭SqlSession
        sqlSession.close();
    }
}
```



**1.namespace**
namespace中的包名要和Mapper接口的包名保持一致
**2.select**
选择，查询语句

- id：对应的namespace中的方法名
- resultType：Sql语句执行的返回值
- parameterType：传入参数类型

1.编写接口
2.编写对应的mapper中的sql语句
3.测试
**3.insert**

```xml
<insert id="insertUser" parameterType="com.guan.pojo.User">    insert into `mybatis`.`user` values (#{id},#{name},#{pwd})</insert>
```

**4.update**

```xml
<update id="updateUser" parameterType="com.guan.pojo.User">    update `mybatis`.`user`    set name = #{name},pwd = #{pwd}    where id = #{id};</update>
```

**5.delete**

```xml
<delete id="deleteUser" parameterType="int">    delete    from `mybatis`.`user`    where id = #{id};</delete>
```