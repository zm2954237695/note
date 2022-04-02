求和案例纯vue版本

Count.vue

```vue
<template>
	<div>
		<h1>当前求和为:{{sum}}</h1>
		<select v-model.number="n">
			<!--将value中的n转化为数字，还可以用v-bind绑定value做到-->
			<option value ="1">1</option>
			<option value ="2">2</option>
			<option value ="3">3</option>
		</select>
		<button @click="increment">+</button>
		<button @click="decrement">-</button>
		<button @click="incrementOdd">当前求和为奇数再加</button>
		<button @click="incrementWait">等一等再加</button>
	</div>
</template>

<script>
	export default{
		name:'Count',
		data(){
			return {
				n:1,
				sum:0
			}
		},
		methods:{
			increment(){
				this.sum+=this.n
			},
			decrement(){
				this.sum-=this.n
			},
			incrementOdd(){
				if(this.sum%2){
					this.sum+=this.n
				}
			},
			incrementWait(){
				setTimeout(()=>{
					this.sum+=this.n
				},500)
			},
			
			
		}
	}
</script>

<style>
</style>

```

app.vue

````vue
<template>
  <div class="app">
    <Count/>
  </div>
</template>

<script>
import Count from './components/Count'
export default {
  name: "App",
  components: {Count},
}
</script>

<style scoped>
.app {
  background-color: gray;
  padding: 5px;
}
</style>

````

