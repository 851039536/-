# vue-cli 4修改端口

### 文件路径

在node_modules>@vue>cli-service>optons.js

### 打开文件

```js
 devServer: {

    open: process.platform === 'darwin',
    host: '0.0.0.0',
    port: 80,
    https: false,
    hotOnly: false,
    proxy: null, // string | Object
    before: app => {}

  }
```

