# Vue2页面刷新

### router刷新

这种方法页面会一瞬间的白屏

```
this.$router.go(0)
```

### location

这种也是一样，画面一闪

```
location.reload()
```

以上整个浏览器进行了重新加载，闪烁，体验不好

### provide/inject

允许一个祖先组件向其所有子孙后代注入一个依赖，**不论组件层次有多深**，并在起上下游关系成立的时间里始终生效。

provide：选项应该是一个对象或返回一个对象的函数。该对象包含可注入其子孙的属性。

inject：一个字符串数组，或一个对象，对象的 key 是本地的绑定名



搭配provide、inject使用,首先在主页面 app.vue

```html
 <keep-alive include="GjTable">
      <router-view v-if="isRouterAlive"></router-view
  ></keep-alive>
```

js

```js
export default {
 provide () {
    return {
      reload: this.reload
    },
  data () {
    return {
      isRouterAlive: true
    }
  },
 methods: {
    reload () {
      this.isRouterAlive = false
      this.$nextTick(function () {
        this.isRouterAlive = true
      })
    }
  }
}
```



在页面注入app.vue组件提供（provide）的 reload 依赖，在逻辑完成之后（删除或添加...）,直接this.reload()调用，即可刷新当前页面

```js
inject: ['reload'],
 //调用刷新： 
 // 此页面加了缓存需要重新加载数据 
 this.getgjtype('vue')  
 this.reload() // 刷新页面 
```



 