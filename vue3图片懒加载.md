# vue3图片懒加载

懒加载就是延时加载，即当需要用到的时候再去加载

1. 减少首页首次加载的数量，减少服务器的压力
2. 网络请求比较慢的时候, 提前给图片添加一个像素比较低的占位图片，或显示大片空白,体验更好一点。

### 安装vue3-lazy

```
$ npm install vue3-lazy -S
```

main.ts

```js
import App from './App.vue'
import lazyPlugin from 'vue3-lazy'
// use
const app = createApp(App);
app.use(lazyPlugin, {
  loading: require('@/assets/img/sp.png'), // 加载时默认图片
  error: require('@/assets/img/sp.png')// 图片失败时默认图片
})
```

### 使用

在组件中，图片标签上使用 v-lazy

```csharp
//图片
<img v-lazy="info.urlImg" /> 
```

