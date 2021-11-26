# vite ant Design vue按需加载



### main.ts

```
import { createApp } from 'vue'
import { setupAntd } from './antd-ui'
import App from './App.vue'

const app = createApp(App)
setupAntd(app)
app.mount('#app')


```

### antd-ui.ts

```js
import {
  Button, Pagination, PageHeader, Select, Table, Layout, Menu, Form, Input, Spin,
  BackTop, Modal, message, Space, Breadcrumb, Popconfirm, Avatar, Skeleton
} from 'ant-design-vue';
const plugins = [Button, Pagination, PageHeader, Select, Table, Layout, Menu, Form, Input, Spin,
  BackTop, Modal, message, Space, Breadcrumb, Popconfirm, Avatar, Skeleton];

export const setupAntd = (app: any, options = {}) => {
  app.config.globalProperties.$mes = message;
  plugins.forEach((plugin) => {
    app.use(plugin);
  });
};
```

### vite.config.ts
#### 安装 3个插件

```
npm install less less-loader vite-plugin-style-import -D
```


由于 vite 本身已按需导入了组件库，因此仅样式不是按需导入的，因此只需按需导入样式即可。

```
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import styleImport from 'vite-plugin-style-import'
```

// 如果编辑器提示 path 模块找不到，则可以安装一下

 **@types/node -> npm i @types/node -D**

```js
 import { resolve } from 'path'
// https://vitejs.dev/config/
export default defineConfig({
  resolve: {
    alias: {
      '@': resolve(__dirname, 'src') // 设置 `@` 指向 `src` 目录
    }
  },
  plugins: [
    vue(),
    styleImport({
      libs: [
        {
          libraryName: 'ant-design-vue',
          esModule: true,
          resolveStyle: (name) => {
            return `ant-design-vue/es/${name}/style/index`
          }
        }
      ]
    }),
  ],

  css: {
    preprocessorOptions: {
      less: {
        modifyVars: { // 更改主题在这里
          'primary-color': '#52c41a',
          'link-color': '#1DA57A',
          'border-radius-base': '2px'
        },
        javascriptEnabled: true
      }
    }
  }
  }
})
```

### 参考

https://blog.csdn.net/weixin_46602773/article/details/116048113