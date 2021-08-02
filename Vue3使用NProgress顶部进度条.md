# Vue3使用NProgress顶部进度条



### 1.安装

```c#
 $ npm install --save nprogress 
 $ yarn add nprogress
```

### 2.使用

**App.vue**

```vue
<script lang="ts">
import NProgress from "nprogress";
import "nprogress/nprogress.css";
import { useRouter } from "vue-router";
export default {
  setup() {
    const router = useRouter();
    router.beforeEach((to, from, next) => {
      NProgress.start();
      next();
    });
    router.afterEach(() => {
      NProgress.done();
    });
  },
};
</script>
```

### import NProgress from "nprogress";

**shims-vue.d.ts**

```js
declare module 'nprogress';
```

