MyBatis使用#{}可以防止SQL注入，因为Mybatis使用了预编译的功能，在SQL执行前，会将SQL发送给数据库进行编译；执行时，直接使用编译好的SQL替换占位符"?"就可以了，而SQL注入只对编译过程起作用。

MyBatis在框架底层，是JDBC的PreparedStatement类起作用，PreparedStatement对象包含了编译好的SQL语句。这种“准备好”的方式不仅能提高安全性，而且在多次执行同一个SQL时，能够提高效率。原因是SQL已编译好，再次执行时无需再编译。

##### Mybatis的#和$的区别

1. #是将传入的值当做字符串的形式，$是将传入的数据直接显示生成sql语句。
2. 使用#可以很大程度防止sql注入。
3. order by中需要使用$.

##### Mybatis传递多个参数

1. 使用map接口传递传数(Map<String,Object>)
2. 使用注解传递多个参数 @Param
3. 通过java bean传递多个参数
4. 混合使用

##### Mybatis使用分页

1. 使用sql原生的limit子句
2. 定义一个类实现interceptor接口
3. 使用第三方插件PageHelper

   无论哪种实现方式，都不能使用List返回，要自定义Pager分页对象。

 

