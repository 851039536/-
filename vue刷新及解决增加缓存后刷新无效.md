# vue刷新及解决增加缓存后刷新无效



### this.$router.go(0)

`this.$router.go(0)`
这种方法页面会一瞬间的白屏，体验不是很好，虽然只是一行代码的事

### location.reload()

`location.reload()`
这种也是一样，画面一闪，效果总不是很好

###  provide、inject

3、搭配`provide、inject`使用

首先在主页面 `app.vue`

#### Home.vue

```csharp
    <keep-alive>
      <!--      //当前进入的路由 meta里面 keepAlive为true时走这里-->
      <router-view v-if="$route.meta.keepAlive" class="home-router"></router-view>
    </keep-alive>
    <!--    当前进入的路由 meta里面 keepAlive为false时走这里 下面 if 判断进行了取反处理-->
    <router-view v-if="!$route.meta.keepAlive"></router-view>
```

#### App.vue

```vue
<template>
  <div id="app">
    <!-- <router-view v-if="isRouterAlive"></router-view> -->
    <home v-if="isRouterAlive"></home>
  </div>
</template>

<script>
  import Home from './views/Home.vue';
  export default {
    name: "app",
    components: {
      Home
    },
    provide() {
      //父组件中通过provide来提供变量，在子组件中通过inject来注入变量。
      return {
        reload: this.reload
      };
    },
    data() {
      return {
        isRouterAlive: true //控制视图是否显示的变量
      };
    },
    methods: {
      reload() {
        this.isRouterAlive = false; //先关闭，
        this.$nextTick(function () {
          this.isRouterAlive = true; //再打开
        });
      }
    }
  };
</script>


```

#### 在需要刷新的页面导入` inject: ['reload'],`

调用刷新： 

```js
 export default {
    inject: ["reload"],
    }
   `this.reload() `// 刷新页面
```

### 回退页面 keeplive 缓存问题

在router/index.js中添加路由元信息，设置需要缓存的页面

```js
    meta: {
      title:'trade.tradeTitle',
      keepAlive: true, // 此组件需要被缓存
      isBack:false, //用于判断上一个页面是哪个
    }
```



#### 钩子函数的执行顺序

**不使用keep-alive**

```vue
beforeRouteEnter --> created --> mounted --> destroyed
```

**使用keep-alive**

```
beforeRouteEnter --> created --> mounted --> activated --> deactivated
```

再次进入缓存的页面，只会触发beforeRouteEnter -->activated --> deactivated 。created和mounted不会再执行。我们可以利用不同的钩子函数，做不同的事。务必理解上述钩子函数的执行时机和执行顺序，本教程的核心就依赖于此钩子函数
activated和deactivated是使用keep-alive后

#### beforeRouteEnter

在缓存的页面通过**beforeRouteEnter**钩子函数中判断是从哪个页面过来的

```js
    beforeRouteLeave(to, from, next) {
      // 路由导航钩子，此时还不能获取组件实例 `this`，所以无法在data中定义变量（利用vm除外）
      // 所以，利用路由元信息中的meta字段设置变量，方便在各个位置获取。这就是为什么在meta中定义isBack
      if (from.path === '/SnNavigationUp') {
        //判断是从哪个路由过来的，
        //刷新数据
        to.meta.isBack = false;
      } else {
        //不刷新
        to.meta.isBack = true;
      }
      next();
    },
```

#### data

data中定义变量**isFirstEnter**用来判断是否第一次进入，或是否刷新了页面，默认false

```c#
 data() {
    return {
      isFirstEnter:false,
    };
  },
```

#### created

created中把isFirstEnter变为true，说明是第一次进入或刷新了页面

```js
      this.isFirstEnter = true;
      // 只有第一次进入或者刷新页面后才会执行此钩子函数
      // 使用keep-alive后（2+次）进入不会再执行此钩子函数
      this.$nextTick(() => {
        this.getall();
      });
```

#### activated

activated中增加判断条件

```js
   activated() {
      if (this.$route.meta.isBack || !this.isFirstEnter) {
        // 如果isBack是false，表明需要获取新数据，否则就不再请求，直接使用缓存的数据
        // 如果isFirstEnter是true，表明是第一次进入此页面或用户刷新了页面，需获取新数据
        this.$nextTick(() => {
          this.getall();
        });
      } else {
        this.$route.meta.isBack = false
        this.isFirstEnter = false;
      }
    },
```







直接写上也可以解决以上问题

```js
activated() {
          this.getall();
    },
```



### 参考 https://blog.csdn.net/weixin_41164499/article/details/90718155

