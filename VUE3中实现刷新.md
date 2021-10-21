#  VUE3中实现刷新

provide ： 向子组件以及子孙组件传递数据。接收两个参数，第一个参数是 key，即数据的名称；第二个参数为 value，即数据的值
inject ： 接收父组件或祖先组件传递过来的数据。接收一个参数 key，即父组件或祖先组件传递的数据名称

通过依赖注入（provide和inject）实现自定义页面刷新事件
原理： 给app.vue中router-view绑定v-if事件，在函数中控制v-if的值在短时间内由true到false再到true,从而使页面达到刷新效果

### 刷新实现

app.vue:

```js
<template>
        <!-- 子路由 -->
        <router-view v-if="state.showRouter"></router-view>
        <!-- end 子路由 -->
</template>

<script lang="ts">
import { defineComponent, nextTick, provide, reactive, ref } from 'vue'
export default defineComponent({
  setup() {
    const state = reactive({
      showRouter: true,
    })
    function reload() {
      state.showRouter = false
      nextTick(() => {
        state.showRouter = true
      })
    }
    provide('reload', reload)
    return {
      state,
    }
  },
})
```

### 刷新页面

需要用到刷新事件的子组件：

ArticleTable.vue

```js
import { inject } from 'vue'
//注入刷新事件,这里括号中的参数要对应上前面provide中的第一个参数
const reload: any = inject('reload')
const confirm = async (data: any) => {
  await article.DeleteAsync(data.articleId).then(() => {
    reload()
  })
}
```

### 参考

https://blog.csdn.net/weixin_42088314/article/details/116400364

