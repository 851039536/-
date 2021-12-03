# Vue3 +Vite+ts

### Vite 初始化

```
使用 NPM：
npm init @vitejs/app
使用 Yarn：
yarn create @vitejs/app
```



选择模板

选择 `vue-ts`

 或通过附加的命令行选项直接指定项目名和模板

```
# npm 6.x
npm init @vitejs/app vite-vue3-starter --template vue-ts

# npm 7+（需要额外的双横线）
npm init @vitejs/app vite-vue3-starter -- --template vue-ts

# yarn
yarn create @vitejs/app vite-vue3-starter --template vue-ts

```

安装依赖

```bash
npm install
复制代码
```

启动项目

```bash
npm run dev
```

###  Vite 配置文件

Vite 配置文件 `vite.config.ts` 位于根目录下

```tsx
// eslint-disable-next-line import/no-extraneous-dependencies
import { defineConfig } from 'vite'
// eslint-disable-next-line import/no-extraneous-dependencies
import vue from '@vitejs/plugin-vue'
// eslint-disable-next-line import/no-extraneous-dependencies
import styleImport from 'vite-plugin-style-import'
// 如果编辑器提示 path 模块找不到，则可以安装一下 @types/node -> npm i @types/node -D
import { resolve } from 'path'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    vue(),
    styleImport({
      libs: [
        {
          libraryName: 'ant-design-vue',
          esModule: true,
          resolveStyle: (name) => {
            return `ant-design-vue/es/${name}/style/index`
          }
        }
      ]
    })
  ],
  resolve: {
    extensions: ['.vue', '.mjs', '.js', '.ts', '.jsx', '.tsx', '.json', '.node', '.scss'],
    alias: {
      '@': resolve('./src'),
      '@vi': resolve('./src/views'),
      '@h': resolve('./src/hooks')
    }
  },
  base: '/', // 设置打包路径
  server: {
    port: 4001, // 设置服务启动端口号
    open: true, // 设置服务启动时是否自动打开浏览器
    cors: true // 允许跨域

    // 设置代理，根据我们项目实际情况配置
    // proxy: {
    //   '/api': {
    //     target: 'http://xxx.xxx.xxx.xxx:8000',
    //     changeOrigin: true,
    //     secure: false,
    //     rewrite: (path) => path.replace('/api/', '/')
    //   }
    // }
  },
  css: {
    preprocessorOptions: {
      less: {
        modifyVars: {
          // 更改主题在这里
          'primary-color': '#52c41a',
          'link-color': '#1DA57A',
          'border-radius-base': '2px'
        },
        javascriptEnabled: true
      }
    }
  }
})

```

### 集成路由

安装支持 Vue3 的路由工具 vue-router@4

```bash
npm i vue-router@4
```

创建 src/router/index.ts 文件
在 src 下创建 router 目录，然后在 router 目录里新建 index.ts 文件：

```
 └── src/
     ├── router/
         ├── index.ts  // 路由配置文件

```

```tsx
import {
  createRouter,
  createWebHashHistory,
  RouteRecordRaw
} from 'vue-router'
import Home from '@/views/home.vue'
import Vuex from '@/views/vuex.vue'

const routes: Array<RouteRecordRaw> = [
  {
    path: '/',
    name: 'Home',
    component: Home
  },
  {
    path: '/vuex',
    name: 'Vuex',
    component: Vuex
  },
  {
    path: '/axios',
    name: 'Axios',
    component: () => import('@/views/axios.vue') // 懒加载组件
  }
]

const router = createRouter({
  history: createWebHashHistory(),
  routes
})

export default router
```

`main.ts` 文件中挂载路由配置

```tsx
import { createApp } from 'vue'
import App from './App.vue'

import router from './router/index'

createApp(App).use(router).mount('#app')
```



### 集成状态管理工具

安装支持 Vue3 的状态管理工具 vuex@next

```bash
npm i vuex@next
```

创建 `src/store/index.ts` 文件

在 `src` 下创建 `store` 目录，然后在 `store` 目录里新建 `index.ts` 文件：

```tsx
import { createStore } from 'vuex'
const defaultState = {
  count: 0
}
// Create a new store instance.
export default createStore({
  state() {
    return defaultState
  },
  mutations: {
    increment(state: typeof defaultState) {
      state.count++
    }
  },
  actions: {
    increment(context) {
      context.commit('increment')
    }
  },
  getters: {
    double(state: typeof defaultState) {
      return 2 * state.count
    }
  }
})
```

在 `main.ts` 文件中挂载 Vuex 配置

```ts
import { createApp } from 'vue'
import App from './App.vue'
import store from './store/index'
createApp(App).use(store).mount('#app')
```



### 集成  Axios

```
npm i axios
```

```tsx
/*
 * @Author: Axios封装
 * @Date: 2020-12-08 10:39:03
 * @LastEditTime: 2021-11-23 17:23:08
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \blogs-s\src\api\index.ts
 */
import axios from 'axios'
import qs from 'qs'
import { message } from 'ant-design-vue'
import { reactive } from 'vue'
import store from '@/store/index'
import router from '@/router/index'
import { dataList } from '@/components/aspin/data'
import { storage } from '../storage/storage'
import { removePending, addPending } from './pending'

interface Res {
  urls: any
  roles: any
}
const obj: Res = reactive({
  urls: import.meta.env.VITE_API_DOMAIN,
  roles: storage.get(store.state.Roles)
})
axios.defaults.baseURL = obj.urls
axios.defaults.timeout = 5000
axios.defaults.withCredentials = false
axios.defaults.headers.post['Content-Type'] = 'application/json;charset=UTF-8'
// 允许跨域
axios.defaults.headers.post['Access-Control-Allow-Origin-Type'] = '*'

// 请求拦截器
axios.interceptors.request.use(
  function request(config: any) {
    removePending(config) // 在请求开始前，对之前的请求做检查取消操作
    addPending(config) // 将当前请求添加到 pending 中
    if (config.method === 'post' || config.method === 'put' || config.method === 'delete') {
      // qs序列化
      // eslint-disable-next-line no-param-reassign
      config.data = qs.parse(config.data)
    }
    // 若是有做鉴权token , 就给头部带上token
    if (storage.get(store.state.Roles)) {
      // eslint-disable-next-line no-param-reassign
      config.headers.Authorization = storage.get(store.state.Roles) as string
    }
    return config
  },
  (error) => {
    message.error(error.data.error.message)
    return Promise.reject(error.data.error.message)
  }
)

// 响应拦截器
axios.interceptors.response.use(
  function response(config) {
    dataList.show = true
    removePending(config) // 在请求结束后，移除本次请求
    if (config.status === 200 || config.status === 204) {
      setTimeout(() => {
        dataList.show = false
      }, 1000)

      return Promise.resolve(config)
    }
    return Promise.reject(config)
  },
  // eslint-disable-next-line consistent-return
  (error: any) => {
    if (error.response.status) {
      switch (error.response.status) {
        case 400:
          message.error(`发出的请求有错误，服务器没有进行新建或修改数据的操作==>${error.response.status}`)
          break

        // 401: 未登录
        // 未登录则跳转登录页面，并携带当前页面的路径
        // 在登录成功后返回当前页面，这一步需要在登录页操作。
        case 401: // 重定向
          message.error(`token:登录失效==>${error.response.status}:${store.state.Roles}`)
          storage.remove(store.state.Roles)
          storage.get(store.state.Roles)
          router.replace({
            path: '/Login'
          })
          break
        // 403 token过期
        // 登录过期对用户进行提示
        // 清除本地token和清空vuex中token对象
        // 跳转登录页面
        case 403:
          message.error(`用户得到授权，但是访问是被禁止的==>${error.response.status}`)
          break
        case 404:
          message.error(`网络请求不存在==>${error.response.status}`)
          break
        case 406:
          message.error(`请求的格式不可得==>${error.response.status}`)
          break
        case 410:
          message.error(`请求的资源被永久删除，且不会再得到的==>${error.response.status}`)
          break
        case 422:
          message.error(`当创建一个对象时，发生一个验证错误==>${error.response.status}`)
          break
        case 500:
          message.error(`服务器发生错误，请检查服务器==>${error.response.status}`)
          break
        case 502:
          message.error(`网关错误==>${error.response.status}`)
          break
        case 503:
          message.error(`服务不可用，服务器暂时过载或维护==>${error.response.status}`)
          break
        case 504:
          message.error(`网关超时==>${error.response.status}`)
          break
        default:
          message.error(`其他错误错误==>${error.response.status}`)
      }
      return Promise.reject(error.response)
    }
    store.commit('changeNetwork', false)
  }
)
export default axios

```



### 集成 CSS 预编译器

```
npm i stylus -D
# or
npm i sass -D
npm i less -D
```

```
css: {
    preprocessorOptions: {
      less: {
        javascriptEnabled: true
      },
      scss: {
        // 避免出现: build时的 @charset 必须在第一行的警告
        charset: false,
        additionalData: '@import "./src/design/methodCss.scss";'
      }
    }
  }
```



### 代码规范

使用 **EditorConfig + Prettier + ESLint** 组合来实现代码规范化

这样做带来好处：

- 解决团队之间代码不规范导致的可读性差和可维护性差的问题。
- 解决团队成员不同编辑器导致的编码规范不统一问题。
- 提前发现代码风格问题，给出对应规范提示，及时修复。
- 减少代码审查过程中反反复复的修改过程，节约时间。
- 自动格式化，统一编码风格，从此和脏乱差的代码说再见。



#### 集成 EditorConfig 配置

在项目根目录下增加 `.editorconfig` 文件：

```bash
# Editor configuration, see http://editorconfig.org

# 表示是最顶层的 EditorConfig 配置文件
root = true

[*] # 表示所有文件适用
charset = utf-8 # 设置文件字符集为 utf-8
indent_style = space # 缩进风格（tab | space）
indent_size = 2 # 缩进大小
end_of_line = lf # 控制换行类型(lf | cr | crlf)
trim_trailing_whitespace = true # 去除行首的任意空白字符
insert_final_newline = true # 始终在文件末尾插入一个新行

[*.md] # 表示仅 md 文件适用以下规则
max_line_length = off
trim_trailing_whitespace = false
复制代码
```

注意：

- VSCode 使用 EditorConfig 需要去插件市场下载插件 **EditorConfig for VS Code** 。




#### 集成 Prettier 配置

Prettier 是一款强大的代码格式化工具，支持 JavaScript、TypeScript、CSS、SCSS、Less、JSX、Angular、Vue、GraphQL、JSON、Markdown 等语言，基本上前端能用到的文件格式它都可以搞定，是当下最流行的代码格式化工具。

1. 安装 Prettier

   ```bash
   npm i prettier -D
   复制代码
   ```

2. 创建 Prettier 配置文件

   Prettier 支持多种格式的[配置文件](https://link.juejin.cn?target=https%3A%2F%2Fprettier.io%2Fdocs%2Fen%2Fconfiguration.html)，比如 `.json`、`.yml`、`.yaml`、`.js`等。

   在本项目根目录下创建 `.prettierrc` 文件。

3. 配置 `.prettierrc`

   在本项目中，我们进行如下简单配置，关于更多配置项信息，请前往官网查看 [Prettier-Options](https://link.juejin.cn?target=https%3A%2F%2Fprettier.io%2Fdocs%2Fen%2Foptions.html) 。

   ```json
   {
     "useTabs": false,
     "tabWidth": 2,
     "printWidth": 100,
     "singleQuote": true,
     "trailingComma": "none",
     "bracketSpacing": true,
     "semi": false
   }
   复制代码
   ```

4. Prettier 安装且配置好之后，就能使用命令来格式化代码

   ```bash
   # 格式化所有文件（. 表示所有文件）
   npx prettier --write .
   复制代码
   ```

注意：

- VSCode 编辑器使用 Prettier 配置需要下载插件 **Prettier - Code formatter** 。






#### 集成 ESLint 配置

[ESLint](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Feslint%2Feslint) 是一款用于查找并报告代码中问题的工具，并且支持部分问题自动修复。其核心是通过对代码解析得到的 AST（Abstract Syntax Tree 抽象语法树）进行模式匹配，来分析代码达到检查代码质量和风格问题的能力。

正如前面我们提到的因团队成员之间编程能力和编码习惯不同所造成的代码质量问题，我们使用 ESLint 来解决，一边写代码一边查找问题，如果发现错误，就给出规则提示，并且自动修复，长期下去，可以促使团队成员往同一种编码风格靠拢。

安装 ESLint

可以全局或者本地安装，作者推荐本地安装（只在当前项目中安装）。

```bash
npm i eslint -D
```

配置 ESLint

ESLint 安装成功后，执行 `npx eslint --init`，然后按照终端操作提示完成一系列设置来创建配置文件。

- How would you like to use ESLint? （你想如何使用 ESLint?）

  选择 **To check syntax, find problems, and enforce code style（检查语法、发现问题并强制执行代码风格）**

- What type of modules does your project use?（你的项目使用哪种类型的模块?）

  选择 **JavaScript modules (import/export)**

- Which framework does your project use? （你的项目使用哪种框架?）

  选择 **Vue.js**

- Does your project use TypeScript?（你的项目是否使用 TypeScript？）

  选择 **Yes**

- Where does your code run?（你的代码在哪里运行?）

  选择 **Browser 和 Node**（按空格键进行选择，选完按回车键确定）

- How would you like to define a style for your project?（你想怎样为你的项目定义风格？）

  选择 **Use a popular style guide（使用一种流行的风格指南）**

- Which style guide do you want to follow?（你想遵循哪一种风格指南?）

  选择 **Airbnb: [github.com/airbnb/java…](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fairbnb%2Fjavascript)**

  ESLint 为我们列出了三种社区流行的 JavaScript 风格指南，分别是 Airbnb、Standard、Google。

  

  **这里作者不建议大家去自由配置 ESLint 规则，相信我，这三份 JavaScript 代码风格指南值得我们反复学习，掌握后，编程能力能上一大台阶。**

  

- What format do you want your config file to be in?（你希望你的配置文件是什么格式?）

  选择 **JavaScript**

- Would you like to install them now with npm?（你想现在就用 NPM 安装它们吗?）

  ESLint 会自动去查找缺失的依赖，我们这里选择 **Yes**，使用 NPM 下载安装这些依赖包。

  注意：如果自动安装依赖失败，那么需要手动安装

  ```bash
  npm i @typescript-eslint/eslint-plugin @typescript-eslint/parser eslint-config-airbnb-base eslint-plugin-import eslint-plugin-vue -D
  ```

ESLint 配置文件 `.eslintrc.js`

会在项目根目录下自动生成 `.eslintrc.js` 配置文件：

```js
module.exports = {
  env: {
    browser: true,
    es2021: true
  },
  extends: ['plugin:vue/essential', 'airbnb-base', 'plugin:prettier/recommended'],
  parserOptions: {
    ecmaVersion: 13,
    parser: '@typescript-eslint/parser',
    sourceType: 'module'
  },
  plugins: ['vue', '@typescript-eslint'],
  rules: {
    'no-console': 'off',
    'import/no-unresolved': 'off',
    'import/extensions': 'off',
    'import/no-absolute-path': 'off',
    'import/no-extraneous-dependencies': 'off',
    'vue/no-v-model-argument': 'off'
  },
  globals: {
    defineProps: true,
    defineEmits: true
  }
}

```

根据项目实际情况，如果我们有额外的 ESLint 规则，也在此文件中追加。

注意：

- VSCode 使用 ESLint 配置文件需要去插件市场下载插件 **ESLint** 。

  

虽然，现在编辑器已经给出错误提示和修复方案，但需要我们一个一个去点击修复，还是挺麻烦的。很简单，我们只需设置编辑器保存文件时自动执行 `eslint --fix` 命令进行代码风格修复。

- VSCode 在 `settings.json` 设置文件中，增加以下代码：

  ```js
   "editor.codeActionsOnSave": {
      "source.fixAll.eslint": true
   }
  ```

#### 解决 Prettier 和 ESLint 的冲突

通常大家会在项目中根据实际情况添加一些额外的 ESLint 和 Prettier 配置规则，难免会存在规则冲突情况。

本项目中的 ESLint 配置中使用了 Airbnb JavaScript 风格指南校验，其规则之一是*代码结束后面要加分号*，而我们在 Prettier 配置文件中加了*代码结束后面不加分号*的配置项，这样就有冲突了，会出现用 Prettier 格式化后的代码，ESLint 检测到格式有问题的，从而抛出错误提示。

解决两者冲突问题，需要用到 **eslint-plugin-prettier** 和 **eslint-config-prettier**。

- `eslint-plugin-prettier` 将 Prettier 的规则设置到 ESLint 的规则中。
- `eslint-config-prettier` 关闭 ESLint 中与 Prettier 中会发生冲突的规则。

最后形成优先级：`Prettier 配置规则` > `ESLint 配置规则`。

- 安装插件

  ```bash
  npm i eslint-plugin-prettier eslint-config-prettier -D
  复制代码
  ```

- 在 `.eslintrc.js` 添加 prettier 插件

  ```js
  module.exports = {
    ...
    extends: [
      'plugin:vue/essential',
      'airbnb-base',
      'plugin:prettier/recommended' // 添加 prettier 插件
    ],
    ...
  }
  ```

这样，我们在执行 `eslint --fix` 命令时，ESLint 就会按照 Prettier 的配置规则来格式化代码，轻松解决二者冲突问题。

### es规则

#### Unexpected unnamed function

不允许使用匿名函数

#### assignment to property of function parameter ‘xxx‘

使用ignorePropertyModificationsFor这个属性，他可以为这个规则添加一个白名单，即指定的入参名称不予限制。

```js
{
  .eslintrc
  ...
  "rules": {
    "no-param-reassign": [
      "error",
      {
        "props": true,
        "ignorePropertyModificationsFor": [
          "e", // for e.returnvalue
          "ctx", // for Koa routing
          "req", // for Express requests
          "request", // for Express requests
          "res", // for Express responses
          "response", // for Express responses
          "state" // for vuex state
        ]
      }
    ]
  }
}
```

#### Expected to return a value at the end of arrow function.(consistent-return)

eslint的规则consistent-return默认要求箭头函数如果有写return的话，那么在最后必须要有一个return，并且不能是return; 尝试了一下return null;和return undefined;都是可以的。所以你在最后补一个return true就可以了。

#### iterators/generators require regenerator-runtime, which is too heavyweight for this guide to allow them. Separately, loops should be avoided in favor of array iterations. (no-restricted-syntax)

基本上就是不推荐用`for-in` `for-of`。推荐用`.forEach`，感觉有点变态的化，可以禁用这个rule，改写`.eslintrc.js`，增加：

```javascript
{
  rules: {
    "no-restricted-syntax": 0,
  },
};
```



#### Prefer default export.

```
"rules": {
     "import/prefer-default-export": "off",
     ...
}
```

#### no-return-await

在`async function`里面，`return await`没用。由于`async function`总是包含返回值`Promise.resolve`，除非在总体 Promise 解析或拒绝之前添加额外时间，否则实际上`return await`不会执行任何操作。这种模式几乎肯定是因为程序员无视`async function`的返回语义。

**规则细节**

这个规则旨在防止由于缺乏对`async function`语义的理解而导致的可能的常见性能危害。

以下模式被视为警告：

```javascript
async function foo() {
  return await bar();
}
```

以下模式不是警告：

```javascript
async function foo() {
  return bar();
}

async function foo() {
  await bar();
  return;
}

async function foo() {
  const x = await bar();
  return x;
}
```

**何时不使用它**

如果你想用`await`来表示一个值很高的值，即使它没有必要; 或者如果您不希望避免性能优势`return await`，则可以关闭此规则。

#### no-multi-assign

 禁止连续赋值 (no-multi-assign)

Chaining the assignment of variables can lead to unexpected results and be difficult to read.

对变量连续赋值可能会导致意想不到的结果，而且难以阅读。

#### Missing radix parameter radix

：缺少基数参数基数

#### The template root requires exactly one element

1. 找到eslint配置文件，我这里是 .eslintrc.js
2. 把 extends 中的 ‘plugin:vue/essential’，改成’plugin:vue/vue3-essential’

### 转自掘金

作者：XPoet

链接：https://juejin.cn/post/6951649464637636622

来源：稀土掘金
