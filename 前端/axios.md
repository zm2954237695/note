axios

##### 安装

使用npm

```v
npm install axios
```

使用cdn

```js
<script src="https://unpkg.com/axios/dist/axios.min.js">
 </script>
```

获取data.json里面的数据

```js
mounted(){
				 axios
				 .get('data.json')
				 .then(response => this.info = response.data)
			  }
```

