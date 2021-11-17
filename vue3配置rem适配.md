# vue3配置rem适配



postcss-pxtorem 是一款 postcss 插件，用于将单位转化为 rem

amfe-flexible 用于设置 rem 基准值 npm i postcss-pxtorem@5.1.1 

使用 amfe-flexible 动态设置 REM 基准值（html 标签的字体大小）

### 安装

```json
安装postcss-pxtorem和amfe-flexible的时候，要注意版本， 否者可能会出现如下
Error: PostCSS plugin tailwindcss requires PostCSS 8 这个因为版本出问题了，下载之后，

npm install postcss-pxtorem -D 
npm install amfe-flexible -S -D 
是 --save-dev 的简写 –save-dev || -D //开发依赖（辅助） --save || -S // 运行依赖（发布）

```

### main.js

在main.js导入amfe-flexible

```
import 'amfe-flexible';
```

### 配置

配置postcss-pxtorem

```js
module.exports = {
  plugins: {
    tailwindcss: {},
    // 兼容浏览器，添加前缀
    autoprefixer: {},
    "postcss-pxtorem": {
      rootValue: 37.5, //结果为：设计稿元素尺寸/16，比如元素宽320px,最终页面会换算成 20rem
      propList: ["*"], //是一个存储哪些将被转换的属性列表，这里设置为['*']全部，假设需要仅对边框进行设置，可以写['*', '!border*']
      unitPrecision: 5, //保留rem小数点多少位
      //selectorBlackList: ['.radius'],  //则是一个对css选择器进行过滤的数组，比如你设置为['fs']，那例如fs-xl类名，里面有关px的样式将不被转换，这里也支持正则写法。
      replace: true, //这个真不知到干嘛用的。有知道的告诉我一下
      mediaQuery: false, //媒体查询( @media screen 之类的)中不生效
      minPixelValue: 12, //px小于12的不会被转换
    },
  },
}
```

