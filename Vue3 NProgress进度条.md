# Vue3 NProgress进度条

### 安装插件：

```
yarn add  nprogress
```

###  app.vue中引入插件。

```
import NProgress from 'nprogress'
import 'nprogress/nprogress.css'
```

### 监听路由跳转

进入页面执行插件动画。

路由跳转中

```js
router.beforeEach((to, from, next) => {
  // 开启进度条
  NProgress.start()
  next()
})
```

跳转结束

```js
router.afterEach(() => {
  // 关闭进度条
  NProgress.done()
})
```