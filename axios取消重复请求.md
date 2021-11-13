# axios取消重复请求

实际开发中，我们需要对用户发起的重复请求进行拦截处理，比如用户快速点击提交按钮。

重复的 get 请求，会导致页面更新多次，发生页面抖动的现象；

重复的 post 请求，会导致在服务端生成两次记录



### 封装取消请求逻辑


 取消请求主要场景：

请求方式`method`，请求路径`url`，请求参数`（get为params，post为data）`都相同时，可以视为同一个请求发送了多次，需要取消之前的请求

路由切换时，需要取消上个路由中未完成的请求

#### pending

```tsx
import axios from "axios"

// 声明一个 Map 用于存储每个请求的标识 和 取消函数
const pending = new Map()
/**
 * 添加请求
 * @param {Object} config 
 */
export const addPending = (config: any) => {
  const url = [
    config.method,
    config.url,
    JSON.stringify(config.params),
    JSON.stringify(config.data)
  ].join('&')
  config.cancelToken = config.cancelToken || new axios.CancelToken(cancel => {
    if (!pending.has(url)) { // 如果 pending 中不存在当前请求，则添加进去
      pending.set(url, cancel)
      // console.log('[ 添加请求 ]=>', url)
    }
  })
}
/**
 * 移除请求
 * @param {Object} config 
 */
export const removePending = (config: any) => {
  const url = [
    config.method,
    config.url,
    JSON.stringify(config.params),
    JSON.stringify(config.data)
  ].join('&')
  if (pending.has(url)) { // 如果在 pending 中存在当前请求标识，需要取消当前请求，并且移除
    const cancel = pending.get(url)
    cancel(url)
    pending.delete(url)
    console.log('[ 移除请求 ]=>', url)
  }
}
/**
 * 清空 pending 中的请求（在路由跳转时调用）
 */
export const clearPending = () => {
  for (const [url, cancel] of pending) {
    cancel(url)
    console.log('[ 清空请求 ]=>', url)
  }
  pending.clear()
}

```



### 拦截器中使用



```tsx
axios.interceptors.request.use(config => {
  removePending(options) // 在请求开始前，对之前的请求做检查取消操作
  addPending(options) // 将当前请求添加到 pending 中
  // other code before request
  return config
}, error => {
  return Promise.reject(error)
})

axios.interceptors.response.use(response => {
  removePending(response) // 在请求结束后，移除本次请求
  return response
}, error => {
  if (axios.isCancel(error)) {
    console.log('repeated request: ' + error.message)
  } else {
    // handle error code
  }
  return Promise.reject(error)
})
```

### `clearPending()`方法添加到路由钩子函数中

```jsx
router.beforeEach((to, from, next) => {
  clearPending()
  next()
})
```

### 参考

https://www.jianshu.com/p/3545ae3e575f

