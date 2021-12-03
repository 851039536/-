vite项目组件库按需引入



### Installation

```
npm i unplugin-vue-components -D
```

### vite.config.ts

```js
import Components from 'unplugin-vue-components/vite'

import { AntDesignVueResolver } from 'unplugin-vue-components/resolvers'


export default defineConfig({
  plugins: [
    Components({
      dts: true, //ts支持
      dirs: ['src/views', 'src/components'], // 自定义路径按需导入
      resolvers: [AntDesignVueResolver()] // antd直接使用组件,无需在任何地方导入组件
    })
  ],
})
```

### 命名冲突

[unplugin-vue-components] 的 naming conflicts

```js
Components({
      resolvers: [AntDesignVueResolver()],
      // 加上下面这一行作为配置项即可
      directoryAsNamespace: true,
    }),
```

