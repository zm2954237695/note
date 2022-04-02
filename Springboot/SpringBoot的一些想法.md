##### SpringBoot的一些学习心得:

###### 用户注册功能

-  创建用户的实体类

- 持久层

  - 准备工作 

      ```java
      @Autowired
      private DataSource dataSource;
      
      @Test
      public void getConnection() throws Exception {
      	System.out.println(dataSource.getConnection());
      }
      ```

    测试获取数据库连接功能是否正常。

  - 规划需要的SQL语句

    -  用户注册的本质实质是向用户表中插入数据，需要执行的SQL语句大致是：

    ```mysql
    INSERT INTO t_user (除了uid以外的字段列表) VALUES (匹配的值列表)
    ```

    - 由于数据表用户名字段被设计为UNIQUE，在执行插入数据之前应该检查该用户名是否已经被注册。因此需要有“根据用户名查询用户数据的功能”.需要执行的SQL语句大致是：

      ```mysql
      SELECT * FROM t_user WHERE username=?
      ```

- 设计接口和抽象方法

  ```java
  /** 处理用户数据操作的持久层接口 */
  public interface UserMapper {
      /**
       * 插入用户数据
       * @param user 用户数据
       * @return 受影响的行数
       */
      Integer insert(User user);
  
      /**
       * 根据用户名查询用户数据
       * @param username 用户名
       * @return 匹配的用户数据，如果没有匹配的数据，则返回null
       */
      User findByUsername(String username);
  }
  ```

  由于是这是项目中第一次创建持久层接口，还应在启动类之前添加@MapperScan("com.x.mapper")注解.以配置接口文件的位置。



当结果集的数据库中字段和对应的实体类不一致时，需要自己来编写UserEntityMap

如下：

```xml
<resultMap id="UserEntityMap" type="com.cy.store.entity.User">
        <id column="uid" property="uid"/>
        <result column="is_delete" property="isDelete"/>
        <result column="created_user" property="createdUser"/>
        <result column="created_time" property="createdTime"/>
        <result column="modified_user" property="modifiedUser"/>
        <result column="modified_time" property="modifiedTime"/>
```

由于这是项目中第一次使用SQL映射，所以需要在application.properties中添加mybatis.mapper-locations属性的配置，以指定XML文件的位置。

```properties
mybatis.mapper-locations=classpath:mapper/*.xml
```

在测试类的声明之前添加@RunWith(SpringRunner.class)和@SpringBootTest注解，并在测试类中声明持久层对象，通过自动装配来注入值。

### 用户-注册-业务层

####   业务的定位

1.业务：一套完整的数据处理过程，通常表现为用户认为的一个功能，但是在开发时对应多项数据操作。在项目中，通过业务控制每个“功能”（例如注册、登录等）的处理流程和相关逻辑。

2.流程：先做什么，再做什么。例如：注册时，需要先判断用户名是否被占用，再决定是否完成注册。

3.逻辑：能干什么，不能干什么。例如：注册时，如果用户名被占用，则不允许注册；反之，则允许注册。

4.业务的主要作用是保障数据安全和数据的完整性、有效性。

#### 异常规划

当用户进行注册时，可能会因为用户名被占用而导致无法正常注册，此时需要抛出用户名被占用的异常，因此可以设计一个用户名重复的

在用户进行注册时，会执行数据库的INSERT操作，该操作也是有可能失败的。则创建InsertException异常类，继承自ServiceException类，并从父类生成子类的5个构造方法。



#### 创建接口编写抽象方法

```java
public interface IUserService {
    /**
     * 用户注册
     * @param user 用户数据
     */
    void reg(User user);
}
```

.创建业务层接口目的是为了解耦。关于业务层的抽象方法的设计原则。

> 1.仅以操作成功为前提来设计返回值类型，不考虑操作失败的情况；
> 2.方法名称可以自定义，通常与用户操作的功能相关；
> 3.方法的参数列表根据执行的具体业务功能来确定，需要哪些数据就设计哪些数据。通常情况下，参数需要足以调用持久层对应的相关功能；同时还要满足参数是客户端可以传递给控制器的；
> 4.方法中使用抛出异常的方式来表示操作失败。



#### 实现业务层里面的抽象方法

```java
@Service
public class UserServiceImpl implements IUserService {
	@Autowired
	private UserMapper userMapper;
	
	@Override
	public void reg(User user) {
		// 根据参数user对象获取注册的用户名
		String username = user.getUsername();
		// 调用持久层的User findByUsername(String username)方法，根据用户名查询用户数据
		User result = userMapper.findByUsername(username);
		// 判断查询结果是否不为null
		if (result != null) {
			// 是：表示用户名已被占用，则抛出UsernameDuplicateException异常
			throw new UsernameDuplicateException("尝试注册的用户名[" + username + "]已经被占用");
		}
		
		// 创建当前时间对象
		Date now = new Date();
		// 补全数据：加密后的密码
		String salt = UUID.randomUUID().toString().toUpperCase();
		String md5Password = getMd5Password(user.getPassword(), salt);
		user.setPassword(md5Password);
		// 补全数据：盐值
		user.setSalt(salt);
		// 补全数据：isDelete(0)
		user.setIsDelete(0);
		// 补全数据：4项日志属性
		user.setCreatedUser(username);
		user.setCreatedTime(now);
		user.setModifiedUser(username);
		user.setModifiedTime(now);
		
		// 表示用户名没有被占用，则允许注册
		// 调用持久层Integer insert(User user)方法，执行注册并获取返回值(受影响的行数)
		Integer rows = userMapper.insert(user);
		// 判断受影响的行数是否不为1
		if (rows != 1) {
			// 是：插入数据时出现某种错误，则抛出InsertException异常
			throw new InsertException("添加用户数据出现未知错误，请联系系统管理员");
		}
	}

	/**
	 * 执行密码加密
	 * @param password 原始密码
	 * @param salt 盐值
	 * @return 加密后的密文
	 */
	private String getMd5Password(String password, String salt) {
		/*
		 * 加密规则：
		 * 1、无视原始密码的强度
		 * 2、使用UUID作为盐值，在原始密码的左右两侧拼接
		 * 3、循环加密3次
		 */
		for (int i = 0; i < 3; i++) {
			password = DigestUtils.md5DigestAsHex((salt + password + salt).getBytes()).toUpperCase();
		}
		return password;
	}
}
```

MD5密码加密：

```java
	private String getMd5Password(String password, String salt) {
		/*
		 * 加密规则：
		 * 1、无视原始密码的强度
		 * 2、使用UUID作为盐值，在原始密码的左右两侧拼接
		 * 3、循环加密3次
		 */
		for (int i = 0; i < 3; i++) {
			password = DigestUtils.md5DigestAsHex((salt + password + salt).getBytes()).toUpperCase();
		}
		return password;
	}
}
```

### 用户-注册-控制器

#### 5.1 创建响应结果类

创建com.cy.store.util.JsonResult响应结果类型。

```java
/**
 * 响应结果类
 * @param <E> 响应数据的类型
 */
public class JsonResult<E> implements Serializable {
    /** 状态码 */
    private Integer state;
    /** 状态描述信息 */
    private String message;
    /** 数据 */
    private E data;

    public JsonResult() {
        super();
    }

    public JsonResult(Integer state) {
        super();
        this.state = state;
    }

    /** 出现异常时调用 */
    public JsonResult(Throwable e) {
        super();
        // 获取异常对象中的异常信息
        this.message = e.getMessage();
    }

    public JsonResult(Integer state, E data) {
        super();
        this.state = state;
        this.data = data;
    }

    // Generate: Getter and Setter
}
```

创建com.cy.store.controller.UserController控制器类，在类的声明之前添加@RestController和@RequestMapping("users")注解，在类中添加IUserService业务对象并使用@Autowired注解修饰。

```java
package com.cy.store.controller;
import com.cy.store.service.IUserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/** 处理用户相关请求的控制器类 */
@RestController
@RequestMapping("users")
public class UserController {
    @Autowired
    private IUserService userService;
}
```

2.然后在类中添加处理请求的用户注册方法。

```java
@RequestMapping("reg")
public JsonResult<Void> reg(User user) {
    // 创建返回值
    JsonResult<Void> result = new JsonResult<Void>();
    try {
        // 调用业务对象执行注册
        userService.reg(user);
        // 响应成功
        result.setState(200);
    } catch (UsernameDuplicateException e) {
        // 用户名被占用
        result.setState(4000);
        result.setMessage("用户名已经被占用");
    } catch (InsertException e) {
        // 插入数据异常
        result.setState(5000);
        result.setMessage("注册失败，请联系系统管理员");
    }
    return result;
}
```

