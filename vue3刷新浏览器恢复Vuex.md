# vue3刷新浏览器恢复Vuex

由于Vuex的数据是存储在**内存**中的，当浏览器刷新时，Vue实例会重新加载，原本存在的Vuex数据将会丢失。这其中可能包含一些必要的认证信息，使得站点需要重新登录，

### 封装sessionStorage

sessionStorage 用于临时保存同一窗口(或标签页)的数据，在页面刷新时数据不会删除，但在关闭窗口或标签页将会删除这些数据。

```tsx
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

### 在登录时存储Vuex

```tsx
     store.state.Roles = state.user
     sessionStorage.set('state', store.state.Roles)
```

### App.vue中配置

App.vue是组件树的顶端，在这里配置恢复与缓存策略可以对所有页面生效。

```tsx
import { sessionStorage } from '@/utils/storage/storage'
import { useStore } from 'vuex'
const store = useStore()
store.state.Roles = sessionStorage.get('state')
```

### 参考

参考:https://www.cnblogs.com/zimskyzeng/p/14011532.html

