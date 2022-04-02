使用axios

````vue
可以定义成全局变量，在main.js中，给vue原型增加一个$http属性,

Vue.prototype.$http = axios;

这样每个Vue实例就能使用$http来使用axios
````

使用axios来发送请求

```js
//get请求
const {data:res} = await this.$http.get("menus")
//put请求
const {data:res} =  await  this.$http.put(`userstate?id=${userInfo.id}&state=${userInfo.state}`)
//post请求
 const {data:res} = await this.$http.post('adduser',this.addForm);

//delete请求
const {data:res} =  await this.$http.delete(`deleteuser?id=`+id)
```

或者下面的写法

```js
const _this=this
axios.post('localhost:8081/book/save',this.ruleForm).then(function (response){
    if (response.data == 'success'){
        _this.$router.push('/bookmenu')
    }
})
```

