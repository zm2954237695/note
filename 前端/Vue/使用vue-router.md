使用vue-router

首先先安装vue-router

```shell
npm install vue-router
```

在main.js引入vue-router

```js
import Vue from 'vue';
import App from './App.vue';
import VueRouter from 'vue-router' //引入vue-router
import router from './router' //引入配置好的路由
Vue.use(VueRouter)
Vue.config.productionTip = false;
new Vue({
  render: (h) => h(App),
  router:router  //注册路由器
}).$mount('#app');
```

打造路由的main.js

```js
import  Vue from 'vue'
import Router from 'vue-router'
import  content from '../components/content'
Vue.use(Router)
export default new Router({
routes:[
	{
		path:'/content', //路径
		name:'content',   //路由名 一般和组件名一样
		component:content //组件名
	},
	]
})
```

