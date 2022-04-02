#### 内置指令

### v-text指令

```js
	我们学过的指令：
		v-bind	: 单向绑定解析表达式, 可简写为 :xxx
		v-model	: 双向数据绑定
		v-for  	: 遍历数组/对象/字符串
		v-on   	: 绑定事件监听, 可简写为@
		v-if 	: 条件渲染（动态控制节点是否存存在）
		v-else 	: 条件渲染（动态控制节点是否存存在）
		v-show 	: 条件渲染 (动态控制节点是否展示)
		v-text指令：
			1.作用：向其所在的节点中渲染文本内容，放入标签则也会被当成文本解析
			2.与插值语法的区别：v-text会替换掉节点中的内容，你原来的内容会被代替，
                        无法与原来的内容一起出现，{{xx}}则可以。
```

```html
<!DOCTYPE html>
<html>
<head>
	<meta charset="UTF-8" />
	<title>v-text指令</title>
	<!-- 引入Vue -->
	<script type="text/javascript" src="../js/vue.js"></script>
</head>

<body>
	<!-- 准备好一个容器-->
	<div id="root">
		<div>你好，{{name}}</div>        //  你好，尚硅谷
		<div v-text="name">你好，</div> //   尚硅谷
		<div v-text="str"></div>       //   <h3>你好啊</h3>
	</div>
</body>

<script type="text/javascript">
	Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示。

	new Vue({
		el: '#root',
		data: {
			name: '尚硅谷',
			str: '<h3>你好啊！</h3>'
		}
	})
</script>
</html>

```

### v-html指令

```js
	v-html指令：
		1.作用：向指定节点中渲染包含html结构的内容。 更新元素的 innerHTML。注意：内容
                按普通 HTML 插入 , 不会作为 Vue 模板进行编译。如果试图使用 v-html 组合模板，可
                以重新考虑是否通过使用组件来替代。
		2.与插值语法的区别：
			(1).v-html会替换掉节点中所有的内容，{{xx}}则不会。
			(2).v-html可以识别html结构，与v-text不同。
		3.严重注意：v-html有安全性问题！！！！
			(1).在网站上动态渲染任意HTML是非常危险的，容易导致XSS攻击。
			(2).一定要在可信的内容上使用v-html，永不要用在用户提交的内容上！

```

```html

v-cloak指令（没有值）：
	1.本质是一个特殊属性，Vue实例创建完毕并接管容器后，会删掉v-cloak属性。
	2.使用css配合v-cloak可以解决网速慢时页面展示出模板{{xxx}}的问题。

```

### v-once指令

```js
v-once指令：
		1.v-once所在节点在初次动态渲染后，就视为静态内容了。
		2.以后数据的改变不会引起v-once所在结构的更新，可以用于优化性能。

```

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8" />
		<title>v-once指令</title>
		<!-- 引入Vue -->
		<script type="text/javascript" src="../js/vue.js"></script>
	</head>
	<body>
		<!-- 准备好一个容器-->
		<div id="root">
			<h2 v-once>初始化的n值是:{{n}}</h2>
			<h2>当前的n值是:{{n}}</h2>
			<button @click="n++">点我n+1</button>
		</div>
	</body>

	<script type="text/javascript">
		Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示。
		
		new Vue({
			el:'#root',
			data:{
				n:1
			}
		})
	</script>
</html>

```

**用于vue性能优化**

```html
     v-pre指令：
	1.跳过其所在节点的编译过程。
	2.可利用它跳过：没有使用指令语法、没有使用插值语法的节点，会加快编译。
	
```

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8" />
		<title>v-pre指令</title>
		<!-- 引入Vue -->
		<script type="text/javascript" src="../js/vue.js"></script>
	</head>
	<body>
		<!-- 准备好一个容器-->
		<div id="root">
			<h2 v-pre>Vue其实很简单</h2>
			<h2 >当前的n值是:{{n}}</h2>
			<button @click="n++">点我n+1</button>
		</div>
	</body>

	<script type="text/javascript">
		Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示。

		new Vue({
			el:'#root',
			data:{
				n:1
			}
		})
	</script>
</html>

```


##### 自定义指令总结：

```
一、定义语法：
		(1).局部指令：
	 new Vue({					 new Vue({
		directives:{指令名:配置对象}   或   		directives{指令名:回调函数}
	}) 				                 })
		(2).全局指令：
		Vue.directive(指令名,配置对象) 或   Vue.directive(指令名,回调函数)
            二、配置对象中常用的3个回调：
		(1).bind：指令与元素成功绑定时调用。
		(2).inserted：指令所在元素被插入页面时调用。
		(3).update：指令所在模板结构被重新解析时调用。

	三、备注：
		1.指令定义时不加v-，但使用时要加v-；
		2.指令名如果是多个单词，要使用kebab-case命名方式，别忘了加"",不要用camelCase命名。
		3.所有指令相关的this都是window
               
```

```html
<!DOCTYPE html>
<html>
<head>
	<meta charset="UTF-8" />
	<title>自定义指令</title>
	<script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
	<!-- 准备好一个容器-->
	<div id="root">
		<h2>{{name}}</h2>
		<h2>当前的n值是：<span v-text="n"></span> </h2>
		<!-- <h2>放大10倍后的n值是：<span v-big-number="n"></span> </h2> -->
		<h2>放大10倍后的n值是：<span v-big="n"></span> </h2>
		<button @click="n++">点我n+1</button>
		<hr />
		<input type="text" v-fbind:value="n">
	</div>
</body>

<script type="text/javascript">
	Vue.config.productionTip = false

	//定义全局指令
	/* Vue.directive('fbind',{
		//指令与元素成功绑定时（一上来）
		bind(element,binding){
			element.value = binding.value
		},
		//指令所在元素被插入页面时
		inserted(element,binding){
			element.focus()
		},
		//指令所在的模板被重新解析时
		update(element,binding){
			element.value = binding.value
		}
	})
	Vue.directive('big',function(element, binding) { //两个参数：当前DOM元素（span），本次绑定的所有信息
				console.log(element, binding)
				// console.log('big', this) 
				//注意此处的this是window
				element.innerText = binding.value * 10
			},)
 */
	new Vue({
		el: '#root',
		data: {
			name: '尚硅谷',
			n: 1
		},
		directives: {

			//指令名如果是多个单词，别忘了加"",方法内部放的是"key"，value值，大部分情况""可省略，但加了-之后引号必须带。
			// 全写是： 'big-number':function(element,binding){} 
			/* 'big-number'(element,binding){
				// console.log('big')
				element.innerText = binding.value * 10
			}, */

			// big函数何时会被调用？1.指令与元素成功绑定时（一上来）。2.指令所在的模板被重新解析时。
			big(element, binding) { //两个参数：当前DOM元素（span），本次绑定的所有信息
				console.log(element, binding)
				// console.log('big', this) 
				//注意此处的this是window
				element.innerText = binding.value * 10
			},
			fbind: {
				//指令与元素成功绑定时（一上来）调用
				bind(element, binding) {//两个参数：当前DOM元素（input），本次绑定的所有信息
					element.value = binding.value
				},
				//指令所在元素被插入页面时调用
				inserted(element, binding) {
					element.focus()
					// input输入框自动获取焦点，这句代码必须放在将input放入页面的后面
				},
				//指令所在的模板被重新解析时调用
				update(element, binding) {
					element.value = binding.value
					element.focus()
				}
			}
		}
	})

</script>

</html>


```

## 2.18生命周期

```
生命周期：
		1.又名：生命周期回调函数、生命周期函数、生命周期钩子。
		2.是什么：Vue在关键时刻帮我们调用的一些特殊名称的函数。
		3.生命周期函数的名字不可更改，但函数的具体内容是程序员根据需求编写的。
		4.生命周期函数中的this指向是vm 或 组件实例对象。这意味着你不能使用箭头函数来定
                义一个生命周期方法 (例如 created: () => this.fetchTodos())。

```

```html
<!DOCTYPE html>
<html>
<head>
	<meta charset="UTF-8" />
	<title>引出生命周期</title>
	<!-- 引入Vue -->
	<script type="text/javascript" src="../js/vue.js"></script>
</head>

<body>				
	<!-- 准备好一个容器-->
		<div id="root">
		<h2 :style="{opacity:opacity}">欢迎学习Vue</h2>
		<button @click="opacity = 1">透明度设置为1</button>
		<button @click="stop">点我停止变换</button>
	</div>
</body>

<script type="text/javascript">
	Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示。

	const vm = new Vue({
		el: '#root',
		data: {
			opacity: 1
		},
		methods: {
			stop() {
				this.$destroy()
			}
		},
            //Vue完成模板的解析并把初始的真实DOM元素放入页面后（挂载完毕）调用mounted(挂载)，只是初始
            //化的时候调用了一次，后续数据更改重新解析模板时不会再次调用
		mounted() {
			console.log('mounted', this)
			this.timer = setInterval(() => {
				// 这里箭头函数无this，自动寻找上下文this，找到了mounted（）的this，这个this指向vm
				console.log('setInterval')
				this.opacity -= 0.01
				if (this.opacity <= 0) this.opacity = 1
			}, 16)
		},
		beforeDestroy() {
			console.log('vm即将被销毁')
			clearInterval(this.timer)
			// 清除定时器
		},
	})

	//通过外部的定时器实现（不推荐）
	// setInterval(() => {
	// 	vm.opacity -= 0.01
	// 	if (vm.opacity <= 0) { vm.opacity = 1 }
	// }, 16)
</script>
</html>

```

