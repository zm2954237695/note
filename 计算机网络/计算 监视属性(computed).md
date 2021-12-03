#### 计算属性(computed)

##### 姓名案例_插值语法实现

```html
<!DOCTYPE html>
<html>
<head>
	<meta charset="UTF-8" />
	<title>姓名案例_插值语法实现</title>
	<!-- 引入Vue -->
	<script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
	<!-- 准备好一个容器-->
	<div id="root">
		姓：<input type="text" v-model="firstName"> <br /><br />
		名：<input type="text" v-model="lastName"> <br /><br />
		全名：<span>{{firstName.slice(0,3)}}-{{lastName}}</span>
		<!-- 要求截取前三位，还可以在{{}}添加更多需求，但是十分不推荐，应该尽量简洁 -->
		<!-- 全名：<span>{{firstName+ '-' +lastName}}</span> -->
	</div>
</body>
<script type="text/javascript">
	Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示。

	new Vue({
		el: '#root',
		data: {
			firstName: '张',
			lastName: '三'
		}
	})
</script>
</html>

```

通过methods方法来实现

```html
<!DOCTYPE html>
<html>
<head>
	<meta charset="UTF-8" />
	<title>姓名案例_插值语法实现</title>
	<!-- 引入Vue -->
	<script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
	<!-- 准备好一个容器-->
	<div id="root">
		姓：<input type="text" v-model="firstName"> <br /><br />
		名：<input type="text" v-model="lastName"> <br /><br />
		全名：<span>{{fullname()}}</span>
			
		</span>
	</div>
</body>
<script type="text/javascript">
	Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示。

	new Vue({
		el: '#root',
		data: {
			firstName: '张',
			lastName: '三'
		},
		methods:{
			fullname(){
				return this.firstName + '-' +this.lastName;
			}
		}
	})
</script>
</html>

```

##### 姓名案例_计算属性实现

1. *定义*：要用的属性不存在，要通过已有属性(Vue实例中的属性)计算得来。

2. 原理：底层借助了Object.defineproperty方法提供的getter和setter.

3. get函数什么时候执行？

   (1). 初次读取时会执行一次。

   (2). 当依赖的数据发生改变时会被再次调用。

4. 优势:与methods相比，内部有缓存机制，效率更高，调试方便。
5. 备注：
   1. 计算属性最终会出现在vm上，直接读取使用即可。不能写fullname.get(),没有这种写法。
   2. 如果计算属性要被修改，那必须写set函数去响应修改，且set中要引起计算时依赖的数据发生改变。

```html
<!DOCTYPE html>
<html>
<head>
	<meta charset="UTF-8" />
	<title>姓名案例_插值语法实现</title>
	<!-- 引入Vue -->
	<script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
	<!-- 准备好一个容器-->
	<div id="root">
		姓：<input type="text" v-model="firstName"> <br /><br />
		名：<input type="text" v-model="lastName"> <br /><br />
		全名：<span>{{fullname}}</span>
			
		</span>
	</div>
</body>
<script type="text/javascript">
	Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示。

	new Vue({
		el: '#root',
		data: {
			firstName: '张',
			lastName: '三'
		},
		computed:{
			fullname:{
				get(){
					return this.firstName + '-' + this.lastName;
				},
				set(value){
					const arr = value.split('-');
					this.firstName = arr[0];
					this.lastName = arr[1];
				}
			}
		}
	})
</script>
</html>

```

#### 天气案例-监视属性

监视属性watch:

1. 当被监视的属性发生变化时，回调函数自动调用，进行相关操作。

2. 监视的属性必须存在,才能进行监视.

3. 监视的两种写法:

   (1). new Vue时传入watch配置。

   (2).通过vm.$watch来监视。

深度监视:

(1).Vue中的watch默认不监测对象内部值的改变(一层).

(2).使用watch时根据数据的具体结构，决定是否采用深度监视。

```html
<!DOCTYPE html>
<html>
<head>
	<meta charset="UTF-8" />
	<title>姓名案例_插值语法实现</title>
	<!-- 引入Vue -->
	<script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
	<!-- 准备好一个容器-->
	<div id="root">
		<h2>今天天气很{{info}}{{x}}</h2>
		<button @click="isHot = !isHot;x++">切换天气</button>
		<button @click="changeWeather">切换天气</button>
		<hr/>
		深度监视按钮
		<h3>a的值是:{{numbers.a}}</h3>
		<button @click="numbers.a++">点我让a+1</button>
		<h3>b的值是:{{numbers.b}}</h3>
		<button @click="numbers.b++">点我让b+1</button>
		{{numbers.c.d.e}}
	</div>
</body>
<script type="text/javascript">
	Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示。

	new Vue({
		el: '#root',
		data: {
			isHot:true,
			x:0,
			numbers:{
				a:1,
				b:1,
				c:{
					d:{
						e:100
					}
				}
			}
		},
		computed:{
			info(){
				return this.isHot?'炎热':'凉爽';
			}
		},
		methods:{
			changeWeather(){
				this.isHot = !this.isHot;
				this.x++;
			}
		},
		//第一种写法
		// watch:{
		// 	isHot:{
		// 		immediate:true,
		// 		handler(newVaule,oldValue){
		// 			console.log()
		// 		}
		// 	}
		// }
		第二种
		vm.$watch('isHot',{
			immediate:true,
			handler(newVaule,oldValue){
				
			}
		})
	})
</script>
</html>
```

**通过watch实现的姓名案例与上方计算属性实现的姓名案例比较:**

**computed和watch之间的区别**：

1. computed能完成的功能，watch都可以完成。
2. watch能完成的功能，computed不一定能完成，例如：watch可以进行异步操作。computed不可以，因为computed依赖返回值得到结果。而watch则是得到属性改变的结果。

**两个重要的小原则**：

1. 所被Vue管理的函数，最好写成普通函数，这样this的指向才是vm 或 组件实例对象。
2. 所有不被Vue所管理的函数（定时器的回调函数、ajax的回调函数等、Promise的回调函数），最好写成箭头函数，这样this的指向才是vm 或 组件实例对象。


```html
<!DOCTYPE html>
<html>
<head>
	<meta charset="UTF-8" />
	<title>姓名案例_watch实现</title>
	<!-- 引入Vue -->
	<script type="text/javascript" src="../js/vue.js"></script>
</head>

<body>
	<!-- 准备好一个容器-->
	<div id="root">
		姓：<input type="text" v-model="firstName"> <br /><br />
		名：<input type="text" v-model="lastName"> <br /><br />
		全名：<span>{{fullName}}</span> <br /><br />
	</div>
</body>

<script type="text/javascript">
	Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示。

	const vm = new Vue({
		el: '#root',
		data: {
			firstName: '张',
			lastName: '三',
			fullName: '张-三'
		},
		watch: {
			//简写
			firstName(newValue) {
				setTimeout(() => {
					console.log(this)
					this.fullName = newValue + '-' + this.lastName
				}, 1000);
			},
			lastName(newValue) {
				this.fullName = this.firstName + '-' + newValue
			}
		}
	})
</script>

</html>

```

