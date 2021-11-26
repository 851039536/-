# vite 2.0 动态引入加载图片

### 方法一

根据官网的提示，我找到了最简单的方法，就是在将asset 前面加上src。

```html
<img v-if="post.welcomeScreen" :src="`/src/assets/blogPhotos/${name}.jpg`" alt="" />
```

### 方法二

关于第二个方法，官网说：“实际上，Vite 并不需要在开发阶段处理这些代码！在生产构建时，Vite 才会进行必要的转换保证 URL 在打包和资源哈希后仍指向正确的地址。”

因此，以下的方法开发阶段不需要了解。

首先把给src绑定一个函数，然后把需要图片名字传给函数。



```js
<img :src="" alt="getImageUrl(name)" />
//or
<img v-lazy="getImageUrl(res.img)" />
```

```js
function getImageUrl(name) {
    return new URL(`../assets/blogPhotos/${name}.jpg`, import.meta.url).href;
//or
function getImageUrl(name) {
    return new URL(`/src/assets/img/${name}`, import.meta.url).href
  }
```

### 參考

https://blog.csdn.net/weixin_44717047/article/details/119846671?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1.no_search_link&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1.no_search_link

