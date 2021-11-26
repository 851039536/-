webPack转vite2

### wp2vite

一个转换工具

### 安装

```
npm install -g wp2vite
or
yarn global add wp2vite
```

### 项目目录下启动wp2vite

```js
// 执行wp2vite的命令行
wp2vite 
// 安装依赖
npm install

// 启动项目
npm run dev // 如果原先你的项目有dev script，请执行下面的命令
or
npm run vite-start
```

### 配置vite.config.ts

```js
import {
  defineConfig,
} from 'vite'
import vue from '@vitejs/plugin-vue'
import styleImport from 'vite-plugin-style-import'
import {
  resolve
} from 'path'
// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue(),
    styleImport({
      libs: [{
        libraryName: 'ant-design-vue',
        esModule: true,
        resolveStyle: (name) => {
          return `ant-design-vue/es/${name}/style/index`
        }
      }]
    }),
  ],
  resolve: {
    alias: {
      '@': resolve('./src'),
    }
  },
  build: {
    // 去除console
    terserOptions: {
      compress: {
        drop_console: true,
        drop_debugger: true
      }
    }
  },
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
  },
  base: './', // 打包路径
  server: {
    host: '0.0.0.0',
    port: 4000, // 服务端口号
    open: false, // 服务启动时是否自动打开浏览器
    https: false,
    cors: true, // 允许跨域
  }
})
```

### .env.development

```
NODE_ENV = development
VITE_API_DOMAIN = 'https://localhost:5001/'
```

### .env.production

```
NODE_ENV = production
VITE_API_DOMAIN =  'http://localhost:8087/'
```



### 异常错误

**global is not defined**

index.html页面加入 global

```HTML
<!DOCTYPE html>
<html lang="en">
<body>
        <script>
   			global = globalThis
		</script>
</body>
</html>
```

**require is not defined**

```js
loading: require(logo)
改
lazyPlugin.install(app, {
  loading: '@/assets/img/blog/2.jpg',
  error: '@/assets/img/blog/1.jpg'
})

<img v-lazy="require('/src/assets/img/' + res.img)" />
改
 <img v-lazy="'/src/assets/img/' + res.img" />
```

