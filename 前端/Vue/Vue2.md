## 2.9 计算属性（computed）

### 姓名案例_插值语法实现

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

### 姓名案例_methods实现

```html
<!DOCTYPE html>
<html>
<head>
	<meta charset="UTF-8" />
	<title>姓名案例_methods实现</title>
	<!-- 引入Vue -->
	<script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
	<!-- 准备好一个容器-->
	<div id="root">
		姓：<input type="text" v-model="firstName"> <br /><br />
		名：<input type="text" v-model="lastName"> <br /><br />
		全名：<span>{{fullName()}}</span>
                //fullName带括号表示将函数的返回值展示出来
	</div>
</body>
<!-- data中的数据发生改变，vue模板会重新解析，对data重新读取，如果有在模板里面调方法，方法也会重新被调用 -->
<script type="text/javascript">
	Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示。

	new Vue({
		el: '#root',
		data: {
			firstName: '张',
			lastName: '三'
		},
		methods: {
			fullName() {
				console.log('@---fullName')
				return this.firstName + '-' + this.lastName
			}
		},
	})
</script>
</html>

```

### 姓名案例_计算属性实现

> vue将data中的数据视为属性

**计算属性：**
 **1.定义：** 要用的属性不存在，要通过已有属性(Vue实例中的属性)计算得来。
 **2.原理：** 底层借助了Object.defineproperty方法提供的getter和setter。
 **3.get函数什么时候执行？**
 (1).初次读取时会执行一次。
 (2).当依赖的数据发生改变时会被再次调用。
 **4.优势：** 与methods实现相比，内部有缓存机制（复用），效率更高，调试方便。
 **5.备注：**
 1.计算属性最终会出现在vm上，直接读取使用即可。不能写fullName.get（），没有这种写法。
 2.如果计算属性要被修改，那必须写set函数去响应修改，且set中要引起计算时依赖的数据发生改变。

```html
<!DOCTYPE html>
<html>
<head>
	<meta charset="UTF-8" />
	<title>姓名案例_计算属性实现</title>
	<!-- 引入Vue -->
	<script type="text/javascript" src="../js/vue.js"></script>
</head>

<body>
	<!-- 准备好一个容器-->
	<div id="root">
		姓：<input type="text" v-model="firstName"> <br /><br />
		名：<input type="text" v-model="lastName"> <br /><br />
		测试：<input type="text" v-model="x"> <br /><br />
		全名：<span>{{fullName}}</span> <br /><br />
		全名：<span>{{fullName}}</span> <br /><br />
		全名：<span>{{fullName}}</span> <br /><br />
		全名：<span>{{fullName}}</span>
		<!-- 多个fullName初始只会调用一次get（），因为缓存了 ，用methods方法调用没有缓存-->
	</div>
</body>
<script type="text/javascript">
	Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示。

	const vm = new Vue({
		el: '#root',
		data: {
			firstName: '张',
			lastName: '三',
			x: '你好'
		},
		computed: {
			fullName: {
			   //get有什么作用？当有人读取fullName时，get就会被调用，且返回值就作为fullName的值
			  //get什么时候调用？1.初次读取fullName时。2.所依赖的数据发生变化时。
				get() {
					console.log('get被调用了')
					console.log(this) //此处的this是vm
					return this.firstName + '-' + this.lastName
				},
				//set什么时候调用? 当fullName被修改时。
				set(value) {
					console.log('set', value)
					const arr = value.split('-')
					// 用-做分隔符,将其变为数组
					this.firstName = arr[0]
					this.lastName = arr[1]
                                        //可以用vm.fullName = '李-四'更改fullname的值
				}
                                //在多数情况下只考虑读取不考虑修改，可以把set部分删掉，简写
                                // fullName(){
				//	console.log('get被调用了')
				//	return this.firstName + '-' + this.lastName
				//}
                                //注意上方模板{{}}中依然放fullName，不带括号。
			}
		}
	})
</script>
</html>

```

## 2.10监视属性（侦听器watch）

### 天气案例-监视属性

**监视属性watch**：
 1.当被监视的属性变化时, 回调函数自动调用, 进行相关操作
 2.监视的属性必须存在，才能进行监视！！
 3.监视的两种写法：
 (1).new Vue时传入watch配置
 (2).通过vm.$watch监视

**深度监视**：
 (1).Vue中的watch默认不监测对象内部值的改变（一层）。
 (2).配置deep:true可以监测对象内部值改变（多层）。
 **备注**：
 (1).Vue自身可以监测对象内部值的改变，但Vue提供的watch默认不可以！
 (2).使用watch时根据数据的具体结构，决定是否采用深度监视。

```html
<!DOCTYPE html>
<html>
<head>
	<meta charset="UTF-8" />
	<title>天气案例_监视属性</title>
	<!-- 引入Vue -->
	<script type="text/javascript" src="../js/vue.js"></script>
</head>

<body>
	<!-- 准备好一个容器-->
	<div id="root">
		<h2>今天天气很{{info}} {{x}}</h2>
		<!-- 绑定事件的时候：@xxx="yyy" yyy可以写一些简单的语句
		比如下面两句功能相同，但是要记住模板里面找数据从vm内找，有些语句放入报错 -->
		<button @click="isHot = !isHot;x++">切换天气</button>
		<button @click="changeWeather">切换天气</button>
                <hr />
		深度监视按钮：
		<h3>a的值是:{{numbers.a}}</h3>
		<button @click="numbers.a++">点我让a+1</button>
		<h3>b的值是:{{numbers.b}}</h3>
		<button @click="numbers.b++">点我让b+1</button>
		{{numbers.c.d.e}}
	</div>
</body>

<script type="text/javascript">
	Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示。

	const vm = new Vue({
		el: '#root',
		data: {
			isHot: true,
			x: 0,
			numbers: {
				a: 1,
				b: 1,
				c: {
					d: {
						e: 100
					}
				}
			}
		},
		computed: {
			info() {
				return this.isHot ? '炎热' : '凉爽'
			}
		},
		methods: {
			changeWeather() {
				this.isHot = !this.isHot
				this.x++
			}
		},
		// 方法一
		/* watch:{
			isHot:{
				immediate:true, //初始化时让handler调用一下
				//handler什么时候调用？当isHot发生改变时。
				handler(newValue,oldValue){
					console.log('isHot被修改了',newValue,oldValue)
				}
			}
		} 
		//简写：不用immediate:true，deep: true等属性时可以简写
			// isHot(newValue, oldValue) {
			// 	console.log('isHot被修改了', newValue, oldValue, this)
			// }
		
		*/

		//深度监视：
		//监视多级结构中某个属性的变化，注意带引号
		/* 'numbers.a':{
			handler(){
				console.log('a被改变了')
			}
		} 
		但如果多级结构中属性太多的话太过繁琐
		*/
		//监视多级结构中所有属性的变化
		numbers: {
			deep: true,//deep开启深度监视，不开启的话只监视numbers变化，不能看到numbers内的数据变化
			handler() {
				console.log('numbers改变了')
			}
		}
	})
	// 方法二
	vm.$watch('isHot', {//注意带引号
		immediate: true, //初始化时让handler调用一下
		//handler什么时候调用？当isHot发生改变时。
		handler(newValue, oldValue) {
			console.log('isHot被修改了', newValue, oldValue)
		}
	})
//简写
// vm.$watch('isHot', function (newValue, oldValue) {
// 		console.log('isHot被修改了', newValue, oldValue, this)
// 	})
</script>

</html>

```

### 姓名案例_watch实现

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

## 2.11绑定样式1. class样式

```
写法:class="xxx" xxx可以是字符串、对象、数组。
	字符串写法适用于：类名不确定，要动态获取。
	数组写法适用于：要绑定多个样式，个数不确定，名字也不确定。
	对象写法适用于：要绑定多个样式，个数确定，名字也确定，但不确定用不用。
复制代码
```

### 2. style样式

```
	:style="{fontSize: xxx}"其中xxx是动态值。
	:style="[a,b]"其中a、b是样式对象。
```

```html
<!DOCTYPE html>
<html>
<head>
	<meta charset="UTF-8" />
	<title>绑定样式</title>
	<style>
		.basic {
			width: 400px;
			height: 100px;
			border: 1px solid black;
		}

		.happy {
			border: 4px solid red;
			;
			background-color: rgba(255, 255, 0, 0.644);
			background: linear-gradient(30deg, yellow, pink, orange, yellow);
		}

		.sad {
			border: 4px dashed rgb(2, 197, 2);
			background-color: gray;
		}

		.normal {
			background-color: skyblue;
		}

		.atguigu1 {
			background-color: yellowgreen;
		}

		.atguigu2 {
			font-size: 30px;
			text-shadow: 2px 2px 10px red;
		}

		.atguigu3 {
			border-radius: 20px;
		}
	</style>
	<script type="text/javascript" src="../js/vue.js"></script>
</head>

<body>
	<!-- 准备好一个容器-->
	<div id="root">
		<!-- 绑定class样式--字符串写法，适用于：样式的类名不确定，需要动态指定 -->
		<div class="basic" :class="mood" @click="changeMood">{{name}}</div> <br /><br />

		<!-- 绑定class样式--数组写法，适用于：要绑定的样式个数不确定、名字也不确定 -->
		<div class="basic" :class="classArr">{{name}}</div> <br /><br />

		<!-- 绑定class样式--对象写法，适用于：要绑定的样式个数确定、名字也确定，但要动态决定用不用 -->
		<div class="basic" :class="classObj">{{name}}</div> <br /><br />

		<!-- 绑定style样式--对象写法 -->
		<div class="basic" :style="styleObj">{{name}}</div> <br /><br />
		<!-- 绑定style样式--数组写法 -->
		<div class="basic" :style="styleArr">{{name}}</div>
	</div>
</body>

<script type="text/javascript">
	Vue.config.productionTip = false

	const vm = new Vue({
		el: '#root',
		data: {
			name: '我要进大厂',
			mood: 'normal',
			classArr: ['atguigu1', 'atguigu2', 'atguigu3'],
			classObj: {
				atguigu1: true,
				atguigu2: false,
			},
			styleObj: {
				fontSize: '40px',
				color: 'red',
			},
			styleObj2: {
				backgroundColor: 'orange'
			},
			styleArr: [
				{
					fontSize: '40px',
					color: 'blue',
				},
				{
					backgroundColor: 'gray'
				}
			]
		},
		methods: {
			changeMood() {
				//随机切换心情
				const arr = ['happy', 'sad', 'normal']
				const index = Math.floor(Math.random() * 3)
				//Math.random()	返回 0 ~ 1 之间的随机数，包含 0 不包含 1。
				//Math.floor(x)	对 x 进行下舍入，即向下取整。
				this.mood = arr[index]
			}
		},
	})
</script>
</html>

```

## 2.12条件渲染

**条件渲染：**

### 1.v-if

```
写法：
(1).v-if="表达式" 
(2).v-else-if="表达式"
(3).v-else="表达式"
适用于：切换频率较低的场景。
特点：不展示的DOM元素直接被移除。
复制代码
```

**注意：v-if可以和:v-else-if、v-else一起使用，但要求结构不能被“打断”。**



### 2.v-show

```markdown
写法：v-show="表达式"
适用于：切换频率较高的场景。
特点：不展示的DOM元素未被移除，仅仅是使用样式隐藏掉，display：none								
```

##### 3.备注：使用v-if的时，元素可能无法获取到，而使用v-show一定可以获取到。

```html
<!DOCTYPE html>
<html>

<head>
	<meta charset="UTF-8" />
	<title>条件渲染</title>
	<script type="text/javascript" src="../js/vue.js"></script>
</head>

<body>
	<!-- 准备好一个容器-->
	<div id="root">
		<h2>当前的n值是:{{n}}</h2>
		<button @click="n++">点我n+1</button>
		<!-- 使用v-show做条件渲染  元素隐藏-->
		<!-- <h2 v-show="false">欢迎来到{{name}}</h2>
		<h2 v-show="true">欢迎来到{{name}}</h2> -->
		<!-- <h2 v-show="1 === 1">欢迎来到{{name}}</h2> -->

		<!-- 使用v-if做条件渲染  元素直接被移除-->
		<!-- <h2 v-if="false">欢迎来到{{name}}</h2> -->
		<!-- <h2 v-if="1 === 1">欢迎来到{{name}}</h2> -->

		<!-- <h2 v-show="n === 1">你好</h2>
		<h2 v-show="n===2">百度</h2>
		<h2 v-show="n===3">北京</h2> -->

		<h2 v-if="n===1">你好</h2>
		<h2 v-if="n===1">百度</h2>
		<h2 v-if="n===3">北京</h2>

		<!-- v-else和v-else-if -->
		<div v-if="n === 1">Angular</div>
		<div v-else-if="n === 1">React</div> //这里react不显示
		<div v-else-if="n === 3">Vue</div>
		<div v-else>哈哈</div>

		<!-- v-if与template的配合使用,template不能与v-show一起使用 -->
		<!-- <template v-if="n === 1">
			<h2>你好</h2>
			<h2>百度</h2>
			<h2>北京</h2>
		</template> -->

	</div>
</body>

<script type="text/javascript">
	Vue.config.productionTip = false

	const vm = new Vue({
		el: '#root',
		data: {
			name: '百度',
			n: 0
		}
	})
</script>

</html>

```

## 2.13 收集表单数据 v-model详解



```js
收集表单数据：
	若：<input type="text"/>，则v-model默认收集的是value值，用户输入的就是value值。
	若：<input type="password"/>，则v-model默认收集的是value值，用户输入的就是value值。
	若：<input type="radio"/>，则v-model默认收集的是value值，因为此类型无法输入内容，则无法
        通过输入得到value值，所以要给标签手动添加value值。
	若：<input type="checkbox"/>
            1.没有配置input的value属性，那么默认读取的的就是checked是否被勾选（勾选 or 未勾选，是布尔值）
            2.配置input的value属性:
         	(1)v-model的初始值是非数组，那么收集的就是checked（勾选 or 未勾选，是布尔值）
	        (2)v-model的初始值是数组，那么收集的的就是value组成的数组
	备注：v-model的三个修饰符：
			lazy：失去焦点再收集数据
			number：输入字符串转为有效的数字
		        trim：输入首尾空格过滤

```

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8" />
		<title>收集表单数据</title>
		<script type="text/javascript" src="../js/vue.js"></script>
	</head>
	<body>
		<!-- 准备好一个容器-->
		<div id="root">
			<form @submit.prevent="demo">
				账号：<input type="text" v-model.trim="userInfo.account">
                                <br/><br/>
				密码：<input type="password" v-model="userInfo.password"> <br/><br/>
				年龄：<input type="number" v-model.number="userInfo.age"> <br/><br/>
                          //v-model默认收集的是value值，用户输入的就是value值。
				性别：
                         //  因为此类型无法输入内容，则无法通过输入得到value值，所以要给标签手动添加value值。
				男<input type="radio" name="sex" v-model="userInfo.sex" value="male">
				女<input type="radio" name="sex" v-model="userInfo.sex" value="female"> 
                                <br/><br/>
				爱好：
				学习<input type="checkbox" v-model="userInfo.hobby" value="study">
				打游戏<input type="checkbox" v-model="userInfo.hobby" value="game">
				吃饭<input type="checkbox" v-model="userInfo.hobby" value="eat">
				<br/><br/>
				所属校区
                            //如果是select，则value与v-model绑定的city属性的初始值相同的为初始默认
                            值，如果city初始值为空，则默认为第一个。
				<select v-model="userInfo.city">
					<option value="">请选择校区</option>
					<option value="beijing">北京</option>
					<option value="shanghai">上海</option>
					<option value="shenzhen">深圳</option>
					<option value="wuhan">武汉</option>
				</select>
				<br/><br/>
				其他信息：
				<textarea v-model.lazy="userInfo.other"></textarea> <br/><br/>
				<input type="checkbox" v-model="userInfo.agree">阅读并接受<a href="http://www.atguigu.com">《用户协议》</a>
				<button>提交</button>
			</form>
		</div>
	</body>

	<script type="text/javascript">
		Vue.config.productionTip = false

		new Vue({
			el:'#root',
			data:{
				userInfo:{
					account:'',
					password:'',
					age:18,
					sex:'female',
					hobby:[],
                                        //v-model的初始值是数组，那么收集的的就是value组成的数组
					city:'beijing',
					other:'',
					agree:''
				}
			},
			methods: {
				demo(){
					console.log(JSON.stringify(this.userInfo))
				}
			}
		})
	</script>
</html>

```

## 2.14过滤器（filter）

```js
过滤器：
	定义：对要显示的数据进行特定格式化后再显示（适用于一些简单逻辑的处理）。
	语法：
		1.注册过滤器：Vue.filter(name,callback) 或 new Vue{filters:{}}
		2.使用过滤器：{{ xxx | 过滤器名}}  或  v-bind:属性 = "xxx | 过滤器名"
	备注：
		1.过滤器也可以接收额外参数、多个过滤器也可以串联
		2.并没有改变原本的数据, 是产生新的对应的数据
		3.不是必须的属性，完全可以用methods和computed实现下面代码中的过滤功能
                4.当全局过滤器和局部过滤器重名时，会采用局部过滤器。
```


