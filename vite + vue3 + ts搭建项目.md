# vite + vue3 + ts搭建项目

### 使用 Vite 快速搭建

 Npm

`npm init @vitejs/app`

 Yarn

`yarn create @vitejs/app`

输入项目名称

选择vue- vue-ts

安装依赖

`npm install`

启动项目

`npm run dev`

### 修改 Vite 配置文件

```tsx
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import { resolve } from 'path'
// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue()],
  resolve: {
    alias: {
      '@': resolve('./src')
    }
  },
  base: './', // 打包路径
  server: {
    port: 4000, // 服务端口号
    open: true, // 服务启动时是否自动打开浏览器
    cors: true // 允许跨域
  }
})

```

### 配置文件

如果使用 `TS` ，则需要先安装类型声明文件。

```
npm install --save-dev @types/node
```

ts

```tsx
/*
 * @Author: your name
 * @Date: 2021-09-04 08:39:20
 * @LastEditTime: 2021-09-04 09:07:30
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \vue-vite-blog\src\env.d.ts
 */
/// <reference types="vite/client" />

declare module '*.vue' {
  import { DefineComponent } from 'vue'
  // eslint-disable-next-line @typescript-eslint/no-explicit-any, @typescript-eslint/ban-types
  const component: DefineComponent<{}, {}, any>
  export default component
}
// declare module 'axios';
// declare module 'qs';

```



### 集成路由

安装 

```
npm i vue-router@4
```

创建 `src/router/index.ts` 文件

```tsx
/*
 * @Author: your name
 * @Date: 2021-09-04 08:43:27
 * @LastEditTime: 2021-09-04 08:49:29
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \vue-vite-blog\src\router\index.ts
 */
import { createRouter, createWebHashHistory, RouteRecordRaw } from 'vue-router'

const routes: Array<RouteRecordRaw> = [
  {
    path: '/home',
    name: 'Home',
    component: () => import(/* webpackChunkName: "Home" */ '../views/Home/Home.vue')
  },
  { path: '/', redirect: { name: 'Home' } }
]

const router = createRouter({
  history: createWebHashHistory(),
  routes
})

export default router

```

1. main.ts 文件中挂载

```ts
import { createApp } from 'vue'
import App from '@/App.vue'

import router from '@/router/index'
createApp(App).use(router).mount('#app')

```

### 集成Vuex

1. `npm i vuex@next`
2. 创建 `src/store/index.ts` 文件

```tsx
/*
 * @Author: your name
 * @Date: 2020-12-07 18:59:37
 * @LastEditTime: 2021-08-09 09:57:17
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \blogs-s\src\store\index.ts
 */
import {
  createStore
} from 'vuex'

export default createStore({
  state: {

    token: "test",
    //设置页面是否新窗口打开
    SetPage: false
  },

  mutations: {

  },
  actions: {},
  modules: {}
})
```

**main.ts 文件挂载**

```tsx
import { createApp } from 'vue'
import App from '@/App.vue'

import router from '@/router/index'
import store from '@/store/index'

createApp(App).use(router).use(store).mount('#app')

```

### 集成 Axios

1. `npm i axios`
2. 配置 `Axios`

```ts
/*
 * @Author: Axios封装
 * @Date: 2020-12-08 10:39:03
 * @LastEditTime: 2021-09-04 09:04:19
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \blogs-s\src\api\index.ts
 */
import axios from 'axios'
import qs from "qs";
import store from "../store/index";
import router from '../router';
//'http://129.204.92.64:8081/' 腾讯服务器
// axios.defaults.baseURL = 'https://localhost:44367/',
axios.defaults.baseURL = process.env.VUE_APP_API_URL,
  axios.defaults.timeout = 12000;
// axios.defaults.headers.common['token'] =  AUTH_TOKEN
axios.defaults.headers.post['Content-Type'] = 'application/json;charset=UTF-8';
axios.defaults.headers.post["Access-Control-Allow-Origin-Type"] = "*"; // 允许跨域

axios.interceptors.request.use(function (config: any) {

  // 在发送请求之前做某件事
  if (
    config.method === "post" ||
    config.method === "put" ||
    config.method === "delete"
  ) {
    // 序列化
    config.data = qs.parse(config.data);
    // console.log("qs:" + config.data);
  }
  // 若是有做鉴权token , 就给头部带上token
  if (store.state.token) {
    config.headers.Authorization = store.state.token;
    // console.log("token:" + store.state.token);
  }
  return config;
}, (error: { data: { error: { message: any; }; }; }) => {
  // Message({
  //   //  饿了么的消息弹窗组件,类似toast
  //   showClose: true,
  //   message: error,
  //   type: "error.data.error.message"
  // });
  return Promise.reject(error.data.error.message);
})

axios.interceptors.response.use(function (config: any) {

  if (config.status === 200 || config.status === 204) {
    return Promise.resolve(config);
  } else {
    return Promise.reject(config);
  }

  // return config;
},
  function (error: { response: { status: any; }; }) {
    // return Promise.reject(error)

    if (error.response.status) {
      switch (error.response.status) {
        // 401: 未登录
        // 未登录则跳转登录页面，并携带当前页面的路径
        // 在登录成功后返回当前页面，这一步需要在登录页操作。                
        case 401:
          router.replace({
            path: '/login',
            query: {
              // redirect: router.currentRoute.fullPath
            }
          });
          break;
        // 403 token过期
        // 登录过期对用户进行提示
        // 清除本地token和清空vuex中token对象
        // 跳转登录页面                
        case 403:
          // Toast({
          //   message: '登录过期，请重新登录',
          //   duration: 1000,
          //   forbidClick: true
          // });
          // 清除token
          store.dispatch('FedLogOut').then(() => {
            // 跳转登录页面，并将要浏览的页面fullPath传过去，登录成功后跳转需要访问的页面 
            router.replace({
              path: '/login',
              query: {
                // redirect: router.currentRoute.fullPath
              }
            })
          })
          break;

        // 404请求不存在
        case 404:
          // Toast({
          //   message: '网络请求不存在',
          //   duration: 1500,
          //   forbidClick: true
          // });
          break;
        // 其他错误，直接抛出错误提示
        default:
        // Toast({
        //   message: error.response.data.message,
        //   duration: 1500,
        //   forbidClick: true
        // });
      }
      return Promise.reject(error.response);
    } else {
      // 处理断网的情况
      // eg:请求超时或断网时，更新state的network状态
      // network状态在app.vue中控制着一个全局的断网提示组件的显示隐藏
      // 关于断网组件中的刷新重新获取数据，会在断网组件中说明
      store.commit('changeNetwork', false);
    }
  }
)

export default axios

```

**挂载**

```tsx
import axios from './api/axios'
// 全局ctx(this) 上挂载 $axios
app.config.globalProperties.$api = axios
```

### 集成CSS预编译器

```
npm i sass -D
```



###  安装 Tailwind CSS

```
npm install -D tailwindcss@npm:@tailwindcss/postcss7-compat @tailwindcss/postcss7-compat postcss@^7 autoprefixer@^9
```

创建配置文件

```
npx tailwindcss init -p
```

```js
// tailwind.config.js
/*
 * @Author: your name
 * @Date: 2020-12-08 09:43:09
 * @LastEditTime: 2021-09-02 08:54:26
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \blogs-s\tailwind.config.js
 */
module.exports = {
  // purge: ['./src/**/*.{vue,js,ts,jsx,tsx}'], //删除未使用的CSS
  purge: {
    enabled: true,
    content: ['./src/**/*.{vue,js,ts,jsx,tsx}'],
  },
  darkMode: false, // or 'media' or 'class'
  theme: {
    screens: {

      'cx': {
        'max': '575px'
      },
      'xp': {
        'min': '375px',
        'max': '667px'
      },
      'dp': {
        'min': '414px',
        'max': '736px'
      },
      'sm': {
        'min': '640px',
        'max': '767px'
      },
      'md': {
        'min': '768px',
        'max': '1023px'
      },
      'lg': {
        'min': '1024px',
        'max': '1279px'
      },
      'xl': {
        'min': '1280px',
        'max': '1535px'
      },
      '2xl': {
        'min': '1536px'
      },
    },
    extend: {},
  },
  variants: {
    extend: {},
  },
  // add DaisyUI plugin
  plugins: [
    require('daisyui'),
  ],

  // config (optional)
  daisyui: {
    styled: true,
    themes: false,
    base: false,
    utils: true,
    logs: true,
    rtl: false,
  },
}
```

```js
// postcss.config.js
module.exports = {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
}
```

Vite 默认为您生成的 ./src/index.css 文件 

```css
/* ./src/index.css */

/*! @import */
@tailwind base;
@tailwind components;
@tailwind utilities;
```

```js
// src/main.js
import './index.css'
```

### 整合代码规范插件eslint,prettier

#### 安装依赖

```js
yarn add babel-eslint -D
yarn add @vue/eslint-config-prettier -D
yarn add eslint -D
yarn add eslint-plugin-prettier -D
yarn add eslint-plugin-vue -D
yarn add prettier -D
复制代码
```

#### 根目录建 `.eslintrc.js`

```js
//.eslintrc.js
module.exports = {
  root: true,
  env: {
    node: true,
  },
  extends: ['plugin:vue/vue3-essential', 'eslint:recommended'],
  parserOptions: {
    parser: 'babel-eslint',
  },
  rules: {
    //在此处写规则
    'no-unused-vars': 0, // 定义未使用的变量
  },
};
复制代码
```

####  根目录建 `.prettierrc.json`

```js
//.prettierrc.json
{
  //此处填写规则
  "singleQuote": true,//单引号
  "seme": true,//分号
  "tabWidth": 2,//缩进
  "TrailingCooma": "all",//尾部元素有逗号
  "bracketSpacing": true,//对象中的空格
}
复制代码
```

#### `vscode` 自动格式化

```js
 //settings.json
"editor.formatOnSave": true,//保存时格式化
"files.autoSave": "onFocusChange", //失去焦点时保存
"editor.codeActionsOnSave": {
  "source.fixAll.eslint": true
},
"eslint.validate": [
  "javascript",
  "javascriptreact",
  "typescript"
],  
```




## 配置GZIP压缩

安装依赖

```js
yarn add vite-plugin-compression -D
```

修改 `vite.config.js`

```js
//vite.config.js

import viteCompression from 'vite-plugin-compression'
plugins:[
  ...
  viteCompression({
      verbose: true,
      disable: false,
      threshold: 10240,
      algorithm: 'gzip',
      ext: '.gz'
  })
]
```


