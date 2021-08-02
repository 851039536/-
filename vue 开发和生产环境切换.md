# vue 开发和生产环境切换

### 项目根目录

新建

.env.development    (开发环境) 

```
/*  文件中的内容主要是对开发环境和生产环境的请求接口路径做修改  */
		NODE_ENV = 'development'
		VUE_APP_API_URL = '/api'
```

.env.production        (生产环境)

```
NODE_ENV = 'development'
	VUE_APP_API_URL = 'http://'
```

### axios

```js
axios.defaults.baseURL = process.env.VUE_APP_API_URL,
```



### 环境切换

package.json

```json
"scripts": {
    "serve": "vue-cli-service serve",
    "production-serve": "vue-cli-service serve --mode production",  //这个
    "build": "vue-cli-service build",
    "lint": "vue-cli-service lint"
  },
```



### 启动项目

```
yarn serve
or
yarn production-serve
or
yarn build
```

