SpringBoot整合MyBatis的配置文件

```yaml
# 数据源
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/sharding_0?serverTimezone=UTC
    username: root
    password: guoyibin
    driver-class-name: com.mysql.jdbc.Driver # 等待连接获取的最大超时时间

# mybatis配置
mybatis:
  mapper-locations: classpath:mapper/*.xml    # mapper映射文件位置
  type-aliases-package: com.guo.pojo    # 实体类所在的位置

server:
  port:9000
```



##### SpringBoot 解决跨域

```java
package com.guo.deyu.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.CorsRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

//全局配置类
@Configuration
//解决跨域
public class WebConfig implements WebMvcConfigurer {
    @Override
    /*
        * 1. 与访问路径
        * 2. 请求来源
        * 3. 方法
        * 4. 允许携带
        * 5. 最大时间
     */
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")
                .allowedOrigins("Http://localhost:8080","null")
                .allowedMethods("GET","POST","PUT","OPTIONS","DELETE")
                .allowCredentials(true)
                .maxAge(3600);
    }
}

```

 ##### 前端页面

Login组件

```vue
<template>
  <div class="login_container">
    <div class="login_box">
      <div class="avatar_box">
        <img src="../assets/logo.png" alt />
      </div>
      <el-form
        ref="loginFormRef"
        :rules="loginRules"
        :model="loginForm"
        class="login_form"
        label-width="0px"
      >
        <el-form-item prop="username">
          <el-input
            v-model="loginForm.username"
            prefix-icon="el-icon-s-custom"
          ></el-input>
        </el-form-item>
        <!--密码-->
        <el-form-item prop="password">
          <el-input
            v-model="loginForm.password"
            prefix-icon="el-icon-lock"
            type="password"
          ></el-input>
        </el-form-item>
        <el-form-item class="btns">
          <el-button type="primary" @click="login()">登录</el-button>
          <el-button type="info" @click="resetLoginForm()">重置</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      loginForm: {
        username: 'admin',
        password: '123456',
      },
      loginRules: {
        //校验用户名
        username: [
          { required: true, message: '用户名为必填项', trigger: 'blur' },
          {
            min: 5,
            max: 12,
            message: '长度在 5 到 12 个字符',
            trigger: 'blur',
          },
        ],
        //校验密码
        password: [
          { required: true, message: '用户密码为必填项', trigger: 'blur' },
          {
            min: 6,
            max: 10,
            message: '长度在 6 到 10 个字符',
            trigger: 'blur',
          },
        ],
      },
    };
  },
  methods: {
    //重置表单内容
    resetLoginForm() {
      this.$refs.loginFormRef.resetFields();
    },
    //登录方法
    login() {
      this.$refs.loginFormRef.validate(async (valid) => {
        if (!valid) return; //验证失败
        const { data: res } = await this.$http.post('/test'); //访问后台
        if (res == 'OK') {
          this.$message.success('操作成功');  //信息提示
          this.$router.push({ path: '/home' }); //页面路由跳转
        } else {
          this.$message.error('操作失败');  //错误提示
        }
      });
    },
  },
};
</script>
<style scoped>
.login_container {
  background-color: #2b4b6b;
  height: 100%;
}
.login_box {
  width: 450px;
  height: 300px;
  background-color: #fff;
  border-radius: 3px;
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
}
.avatar_box {
  width: 130px;
  height: 130px;
  border: 1px solid #eee;
  border-radius: 50%;
  padding: 5px;
  box-shadow: 0 0 10px #ddd;
  position: absolute;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: #0ee;
}
img {
  width: 100%;
  height: 100%;
  border-radius: 50%;
  background-color: #eee;
}
.btns {
  display: flex;
  justify-content: flex-end;
}
.login_form {
  position: absolute;
  bottom: 0%;
  width: 100%;
  padding: 0 10px;
  box-sizing: border-box;
}
</style>

```

账号和密码表单

```vue
<el-form
        ref="loginFormRef"
        :rules="loginRules"
        :model="loginForm"
        class="login_form"
        label-width="0px"
>
        <el-form-item prop="username">
          <el-input
            v-model="loginForm.username"
            prefix-icon="el-icon-s-custom"
          ></el-input>
        </el-form-item>
        <!--密码-->
        <el-form-item prop="password">
          <el-input
            v-model="loginForm.password"
            prefix-icon="el-icon-lock"
            type="password"
          ></el-input>
        </el-form-item>
        <el-form-item class="btns">
          <el-button type="primary" @click="login()">登录</el-button>
          <el-button type="info" @click="resetLoginForm()">重置</el-button>
        </el-form-item>
 </el-form>
```

重置功能的方法resetLoginForm()

```javascript
resetLoginForm() {
   
      this.$refs.loginFormRef.resetFields();
},
```

使用axios给后端发请求

在main.js引入axios

```js
import axios from 'axios';
//挂载axios
Vue.prototype.$http = axios;
//设置访问路径
axios.defaults.baseURL = 'http://localhost:9000';
```

pom文件引入json的依赖

```xml
<!--引入json-->
    <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.47</version>
    </dependency>
```



dao层编写Userdao登录需要用到的的接口方法。

```java
 public User getUserByMessage(String username,String password);
```

在mapper文件中写sql语句

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
                PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
                "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

        <!-- namespace属性是名称空间，必须唯一 -->
<mapper namespace="com.guo.deyu.dao.UserDao">
   <select id="getUserByMessage" resultType="com.guo.deyu.bean.User">
        SELECT  * FROM  easyuser WHERE
        username=#{username} AND password = #{password} and state=1
   </select>

</mapper>
```

controller层测试编写的方法 并封装成json对象返回给前端。

```java
@RestController
@CrossOrigin //解决跨域问题
public class LoginController {


    @Autowired
    private UserDao userDao;
    @RequestMapping("/login")
    public String login(@RequestBody User user){
        User us = userDao.getUserByMessage(user.getUsername(), user.getPassword());
        System.out.println("User:"+us);
        Map<String, Object> res = new HashMap<>();
        String state="error";
        if (us != null){
            state="ok";
        }
        res.put("state",state);
        res.put("user",user);
        String res_json = JSON.toJSONString(res);
        return res_json;
    }
}
```

登录成功就会跳转到Home.vue

```js
 login() {
      this.$refs.loginFormRef.validate(async (valid) => {
        if (!valid) return; //验证失败
        const { data: res } = await this.$http.post('login', this.loginForm); //访问后台
        if (res.state == 'ok') {
          this.$message.success('操作成功'); //信息提示
          this.$router.push({ path: '/home' }); //页面路由跳转
        } else {
          this.$message.error('操作失败'); //错误提示
        }
      });
    },
```

```
路由导航守卫是为了路由跳转之前做的检查及操作
比如：用户没有登录 而是直接通过修改浏览器 url 来访问网页，这种操作无疑是不安全的。路由导航守卫可以让我们对用户要跳转的路由做一次检查，符合条件后放行，不符合条件则强制用户跳转登录页面。
```

配置路由导航守卫

```js
//挂载路由导航守卫
//to即将进入的路由 from即将离开的路由 next是否同行
router.beforeEach((to, from, next) => {
  //next(url) 重定向到url上  next()继续访问to路径
  if (to.path == '/login') return next();
  //获取user
  const userFlag = window.sessionStorage.getItem('user'); //取出当前用户
  if (!userFlag) return next('/login');
  next(); //符合要求 放行
});
```

```js
//获取导航菜单方法
created(){
     this.getMenuList()
  },//页面加载完就会调用方法
    async getMenuList(){
        const {data:res} = await  this.$http.get("menus")
        console.log(res);
    }
```

退出功能实现

```vue
logout() {
      window.sessionStorage.clear(); //清除session
      this.$router.push('/login');
  },
```

后端查询数据库获取菜单列表，封装成json返回给前端

```java
package com.guo.deyu.controller;

import com.alibaba.fastjson.JSON;
import com.guo.deyu.bean.MainMenu;
import com.guo.deyu.dao.MenuDao;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.CrossOrigin;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.HashMap;
import java.util.List;
import java.util.Map;

//@CrossOrigin
@RestController
public class MenuController {
    @Autowired
    private MenuDao menuDao;
    @CrossOrigin
    @RequestMapping("/menus")
    public String getAllMenus(){

        HashMap<String, Object> data = new HashMap<>();
        List<MainMenu> menus = menuDao.getMainMenus();
        data.put("data",menus);
        data.put("status",200);
        String data_json = JSON.toJSONString(data);
        System.out.println("成功访问！！！");
        return data_json;
    }

}

```

前端获取导航数据并显示在页面上

Home.vue

```vue
 //获取导航菜单方法
    async getMenuList(){
        const {data:res} = await  this.$http.get("menus")
        if(res.status!=200){
           return this.$message.error("获取列表失败!!!");
        }
        this.menuList=res.data;
    },
```

使用element-ui

```vue
 <el-menu
          background-color="#545c64"
          text-color="#fff"
          active-text-color="#409eff"
          unique-opened :collapse="isCollapse"
          :collapse-transition="false"
          :router="true"
        >
          <el-submenu :index="item.id+''" v-for="item in menuList" :key="item.id">
            <template slot="title">
              <i :class="iconsObject[item.id]"></i>
              <span>{{item.title}}</span>
            </template>

             <el-menu-item :index="it.path" v-for="it in item.slist" :key="it.id">
             <template slot="title">
              <i :class="iconsObject[it.id]"></i>
              <span>{{it.title}}</span>
            </template>
          </el-menu-item>
            </el-submenu>

        </el-menu>
```

![image-20211230202924858](C:\Users\逐梦\AppData\Roaming\Typora\typora-user-images\image-20211230202924858.png)

![image-20211230203008606](C:\Users\逐梦\AppData\Roaming\Typora\typora-user-images\image-20211230203008606.png)

控制菜单中点击哪个项就跳转到哪个项

##### 路径保存

修改el-memu,绑定default-active属性，动态修改path.

```
 <el-menu
          background-color="#545c64"
          text-color="#fff"
          active-text-color="#409eff"
          unique-opened :collapse="isCollapse"
          :collapse-transition="false"
          :router="true"
          :default-active="activePath"
        >
```

data加入activePath配置项.当点击侧边栏的一项时，就会发生路由调转。我们绑定如下事件：

```vue
<el-menu-item :index="it.path" v-for="it in item.slist" :key="it.id" @click="saveNavState(it.path)">
```

实现saveNavState

```js
 saveNavState(activePath){
      window.sessionStorage.setItem('activePath',activePath);
      this.activePath=activePath;
  }
  //由于页面加载时，会保存上一次跳转的path,所以在create方法中加入：
  this.activePath=window.sessionStorage.getItem('activePath')//取出session里面的path,动态修改activePath
```

```
 @RequestBody主要用来接收前端传递给后端的json字符串中的数据的(请求体中的数据的)；而最常用的使用请求体传参的无疑是POST请求了，所以使用@RequestBody接收数据时，一般都用POST方式进行提交。在后端的同一个接收方法里，@RequestBody与@RequestParam()可以同时使用，@RequestBody最多只能有一个，而@RequestParam()可以有多个。

注：一个请求，只有一个RequestBody；一个请求，可以有多个RequestParam。

注：当同时使用@RequestParam（）和@RequestBody时，@RequestParam（）指定的参数可以是普通元素、
       数组、集合、对象等等(即:当，@RequestBody 与@RequestParam()可以同时使用时，原SpringMVC接收
       参数的机制不变，只不过RequestBody 接收的是请求体里面的数据；而RequestParam接收的是key-value
       里面的参数，所以它会被切面进行处理从而可以用普通元素、数组、集合、对象等接收)。
       即：如果参数时放在请求体中，application/json传入后台的话，那么后台要用@RequestBody才能接收到；
             如果不是放在请求体中的话，那么后台接收前台传过来的参数时，要用@RequestParam来接收，或者形参前 什么也不写也能接收。
```

elment的分页

```html
<!-- 分页 -->
      <div>
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="queryInfo.pageNum"
          :page-sizes="[1, 2, 5, 100]"
          :page-size="queryInfo.pageSize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total"
      ></el-pagination>
      </div>
```

UserController编写的增删改查接口

```java
package com.guo.deyu.controller;

import com.alibaba.fastjson.JSON;
import com.guo.deyu.bean.QueryInfo;
import com.guo.deyu.bean.User;
import com.guo.deyu.dao.UserDao;
import org.apache.ibatis.annotations.Param;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.HashMap;
import java.util.List;
import java.util.Map;

@RestController
public class UserController {

    @Autowired
    private UserDao userDao;
    @CrossOrigin
    @RequestMapping("/alluser")
    public String getUserList(QueryInfo queryInfo){
        System.out.println(queryInfo.getQuery());
        int nums = userDao.getUserCounts("%" + queryInfo.getQuery() + "%");
        int pageStart= (queryInfo.getPageNum()-1)*queryInfo.getPageSize();
        List<User> users = userDao.getAllUser("%" + queryInfo.getQuery() + "%", pageStart, nums);

        Map<String, Object> res = new HashMap<>();
        res.put("nums",nums);
        res.put("data",users);
        String s= JSON.toJSONString(res);

        return s;
    }

    @RequestMapping("userstate")
    @CrossOrigin
    public String updateUserState(@RequestParam ("id")Integer id,
                                  @RequestParam("state") Boolean state){

        int i = userDao.updateState(id, state);
        return i>0?"success":"error";
    }


    @RequestMapping("/adduser")
    @CrossOrigin
    public String addUser(@RequestBody User user){
        user.setRole("普通用户");
        user.setState(false);
        int i = userDao.addUser(user);
        return i>0?"success":"error";

    }

    @RequestMapping("/deleteuser")
    @CrossOrigin
    public String deleteUser(int id){
        int i = userDao.deleteUser(id);
        return i>0?"success":"error";
    }

    @RequestMapping("/getupdateuser")
    @CrossOrigin
    public String getUpdateUser(int id){
        User user = userDao.getUpdateUser(id);
        String json_user = JSON.toJSONString(user);
        return json_user;
    }


    @RequestMapping("/updateuser")
    @CrossOrigin
    public String updateUser(@RequestBody User user){
        int i = userDao.editUser(user);
        return i>0?"success":"error";

    }
}

```

ListUser.vue

```vue
<template>
<div>用户列表

  <!-- 面包屑导航 -->
  <el-breadcrumb separator-class="el-icon-arrow-right">
    <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
    <el-breadcrumb-item>权限管理</el-breadcrumb-item>
    <el-breadcrumb-item>用户列表</el-breadcrumb-item>
  </el-breadcrumb>

    <!-- 卡片视图区 -->
    <el-card>
      <el-row :gutter="25">
        <el-col :span="10">          <!-- 搜索添加 -->
          <el-input placeholder="请输入搜索内容" v-model="queryInfo.query" clearable @clear="getUserList">
            <el-button slot="append" icon="el-icon-search" @click="getUserList"></el-button>
          </el-input>
        </el-col>
        <el-col :span="5">
          <el-button type="primary" @click="addDialogVisible = true">添加用户</el-button>
        </el-col>
      </el-row>
      <el-table :data="userList" border stripe>
        <el-table-column type="index"></el-table-column> <!--索引列-->
        <el-table-column label="用户名" prop="username"></el-table-column>
        <el-table-column label="邮箱" prop="email"></el-table-column>
        <el-table-column label="密码" prop="password"></el-table-column>
        <el-table-column label="角色" prop="role"></el-table-column>
        <el-table-column label="状态" prop="state">
          <template slot-scope="scope">
            <el-switch v-model="scope.row.state" @change="userStateChanged(scope.row)"></el-switch>
          </template>
        </el-table-column>
        <el-table-column label="操作">
          <template slot-scope="scope">
            <!-- 修改 -->
            <el-button type="primary" icon="el-icon-edit" size="mini" @click="showEditDialog(scope.row.id)"></el-button>
            <!-- 删除 -->
            <el-button type="danger" icon="el-icon-delete" size="mini" @click="deleteUser(scope.row.id)"></el-button>
            <!-- 权限 -->
            <el-tooltip effect="dark" content="分配权限" placement="top-start" :enterable="false">
              <el-button type="warning" icon="el-icon-setting" size="mini"></el-button>
            </el-tooltip>
          </template>
        </el-table-column>
      </el-table>

      <!-- 分页 -->
      <div>
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="queryInfo.pageNum"
          :page-sizes="[1, 2, 5, 100]"
          :page-size="queryInfo.pageSize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total"
      ></el-pagination>
      </div>
  </el-card>

  <!--新增用户区域-->
  <el-dialog title="添加用户" :visible.sync="addDialogVisible" width="50%"
             @close="addDialogClosed">
    <!-- 内容主体区域 -->
    <el-form :model="addForm" :rules="addFormRules"
             ref="addFormRef" label-width="70px">
      <!-- 用户名 -->
      <el-form-item label="用户名" prop="username">
        <el-input v-model="addForm.username"></el-input>
      </el-form-item>
      <!-- 密码 -->
      <el-form-item label="密码" prop="password">
        <el-input v-model="addForm.password" show-password="false"></el-input>
      </el-form-item>
      <!-- 邮箱 -->
      <el-form-item label="邮箱" prop="email">
        <el-input v-model="addForm.email"></el-input>
      </el-form-item>
    </el-form>
    <!-- 内容底部区域 -->
    <span slot="footer" class="dialog-footer">
        <el-button @click="addDialogVisible =false">取 消</el-button>
        <el-button type="primary" @click="addUser">确 定</el-button>
      </span>
  </el-dialog>


  <el-dialog title="修改用户" :visible.sync="editDialogVisible" width="50%"
             @close="editDialogClosed">
    <!-- 内容主体区域 -->
    <el-form :model="editForm" :rules="editFormRules"
             ref="editFormRef" label-width="70px">
      <!-- 用户名 -->
      <el-form-item label="用户名" prop="username">
        <el-input v-model="editForm.username" disabled="true"></el-input>
      </el-form-item>
      <!-- 密码 -->
      <el-form-item label="密码" prop="password">
        <el-input v-model="editForm.password" show-password="false"></el-input>
      </el-form-item>
      <!-- 邮箱 -->
      <el-form-item label="邮箱" prop="email">
        <el-input v-model="editForm.email"></el-input>
      </el-form-item>
    </el-form>
    <!-- 内容底部区域 -->
    <span slot="footer" class="dialog-footer">
        <el-button @click="editDialogVisible =false">取 消</el-button>
        <el-button type="primary" @click="editUserInfo">确 定</el-button>
      </span>
  </el-dialog>
</div>
</template>

<script>
export default {
  created() {
    this.getUserList();
  },
  data(){
    return {
      queryInfo:{
        query:"",
        pageNum:1,
        pageSize:5,  //每页最大数
      },
      userList:[],//用户列表
      total:0, //总记录数
      addDialogVisible:false,//对话框状态
      addForm:{
        username:'',
        password:'',
        email:''
      },
      editForm:{},
      editDialogVisible:false,
      //表单验证
      addFormRules: {
        //校验用户名
        username: [
          {required: true, message: '请输入用户名', trigger: 'blur'},
          {
            min: 5,
            max: 8,
            message: '长度在 5 到 8 个字符',
            trigger: 'blur',
          },
        ],
        password: [
          {required: true, message: '请输入密码', trigger: 'blur'},
          {
            min: 5,
            max: 12,
            message: '长度在 5 到 12 个字符',
            trigger: 'blur',
          },
        ],
        email: [
          {required: true, message: '请输入邮箱', trigger: 'blur'},
          {
            min: 5,
            max: 15,
            message: '长度在 5 到 15 个字符',
            trigger: 'blur',
          },
        ],
      },
      editFormRules: {
        password: [
          {required: true, message: '请输入密码', trigger: 'blur'},
          {
            min: 5,
            max: 12,
            message: '长度在 5 到 12 个字符',
            trigger: 'blur',
          },
        ],
        email: [
          {required: true, message: '请输入邮箱', trigger: 'blur'},
          {
            min: 5,
            max: 15,
            message: '长度在 5 到 15 个字符',
            trigger: 'blur',
          },
        ],
      }
    }
  },

  methods:{
    async getUserList(){
       const {data:res} = await  this.$http.get("/alluser",{params:this.queryInfo});
       this.userList=res.data;
       this.total=res.nums;
    },
    //最大数
    handleSizeChange(newSize){
      this.queryInfo.pageSize=newSize
      this.getUserList();
    },
    //pageNum的触发动作
    handleCurrentChange(newPage){
      this.queryInfo.pageNum=newPage;
      this.getUserList()
    },

   async userStateChanged(userInfo){
     const {data:res} =  await  this.$http.put(`userstate?id=${userInfo.id}&state=${userInfo.state}`)
     if (res!="success"){
       userInfo.id=!userInfo.id;
       return this.$message.error("操作失败")
     }
     this.$message.success('操作成功')
    },
    addDialogClosed(){
      this.$refs.addFormRef.resetFields();
    },
    addUser(){
      this.$refs.addFormRef.validate(async valid=>{
        if(!valid)return ;
        const {data:res} = await this.$http.post('adduser',this.addForm);
        if(res!="success"){
          return this.$message.error("操作失败");
        }
        this.$message.success("操作成功!!!");
        this.addDialogVisible=false;
        this.getUserList();
      });
    },
    async deleteUser(id){
     const confirmResult =await this.$confirm('此操作将永久删除用户,是否继续','提示',{
        confirmButtonText:'确定',
        cancelButtonText:'取消',
        type:'warning'
      }).catch(err=>err)
      if(confirmResult!='confirm'){ //取消
        return this.$message.info('已取消删除');
      }
     const {data:res} =  await this.$http.delete(`deleteuser?id=`+id)
      if(res!="success"){
        return this.$message.error('删除失败')
      }
      this.$message.success('删除成功')
      this.getUserList()
    },
    async showEditDialog(id){
      const {data:res} = await this.$http.get('getupdateuser?id='+id)
      this.editForm = res  //查询的信息填入editForm
      this.editDialogVisible = true //开启编辑对话框
    },
    editDialogClosed(){
      this.$refs.editFormRef.resetFields()  //清空对话框的数据
    },
    editUserInfo(){
      this.$refs.editFormRef.validate(async valid=>{
          if(!valid)return ;
         const { data: res} =  await  this.$http.put('updateuser',this.editForm)
         if(res!="success"){
           this.$message.error('操作失败')
         }
          this.$message.success('操作成功')
          this.editDialogVisible=false
          this.getUserList()
      })
    }
  }


}
</script>

<style scoped>
.el-breadcrumb{
  margin-bottom: 15px;
  font-size: 12px;
}
</style>
```

