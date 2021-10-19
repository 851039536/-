# vue3封装storage实现token验证

### 验证思路

1 登录成功返回 token

2 拿到token，将token存储到localStorage，并跳转路由页面

3 跳转路由，就判断 localStroage 中有无 token

4 调后端接口，都要在请求头中加token

5 前端拿到状态码为401，就清除token信息并跳转到登录页面

### storage封装

localStorage特性，这个特性主要是用来作为本地存储来使用的，解决了cookie存储空间不足的问题(cookie中每条cookie的存储空间为4k)，localStorage中一般浏览器支持的是5M大小，这个在不同的浏览器中localStorage会有所不同。

```tsx

/**
 * 封装操作localstorage本地存储的方法
 */
export const storage = {
  //存储
  set(key: string, value: any) {
    localStorage.setItem(key, JSON.stringify(value))
  },
  //取出数据
  get<T>(key: string) {
    const value = localStorage.getItem(key)
    if (value && value != "undefined" && value != "null") {
      return <T>JSON.parse(value)
    }
  },
  // 删除数据
  remove(key: string) {
    localStorage.removeItem(key)
  }
};

/**
 * 封装操作sessionStorage本地存储的方法
 */
export const sessionStorage = {
  //存储
  set(key: string, value: any) {
    window.sessionStorage.setItem(key, JSON.stringify(value))
  },
  //取出数据
  get<T>(key: string) {
    const value = window.sessionStorage.getItem(key)
    if (value && value != "undefined" && value != "null") {
      return JSON.parse(value)
    }
    return null
  },
  // 删除数据
  remove(key: string) {
    window.sessionStorage.removeItem(key)
  }
}
```

### login.vue

登录成功后进行token保存

```vue

<script setup lang="ts">
import { Routers } from '@/hooks/routers'
import { user } from '@/api/index'
import { reactive } from 'vue'
import { useStore } from 'vuex'
import { storage } from '@/utils/storage/storage'
const stores = useStore()
const state = reactive({
  user: 'kai',
  pwd: 'kai',
  result: [],
})

async function login() {
  user.Login(state.user, state.pwd).then((res) => {
    state.result = res.data.split(',')
    if (state.result[0] === '1') {
      storage.set('token', 'Bearer ' + state.result[1])
      Routers('/Admin-index/ArticleTable')
    }
  })
}
</script>

```

### axios.ts

```tsx
// 添加请求拦截器
axios.interceptors.request.use(function (config) {
  if (
    config.method === "post" ||
    config.method === "put" ||
    config.method === "delete"
  ) {
    // qs序列化
    config.data = qs.parse(config.data);
  }
  // 鉴权token , 给头部带上token
  if (storage.get('token')) {
    config.headers.Authorization = storage.get('token');
    console.log('%c [  config.headers.Authorization ]', 'font-size:13px; background:pink; color:#bf2c9f;', config.headers.Authorization)
  }
  return config;
}, error => {
  return Promise.reject(error.data.error.message);
})
```

### *全局前置守卫*

```tsx
// 导航守卫
// 使用 router.beforeEach 注册一个全局前置守卫，判断用户是否登陆
router.beforeEach((to, from, next) => {
  if (to.path === "/Login") {
    next();
  } else {

    const token = storage.get('token')
    if (token === undefined || token === "") {
      next("/Login");
    } else {
      next();
    }
  }
});
```

