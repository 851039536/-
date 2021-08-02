# axios 封装

> 中文文档

[Axios中文文档](https://www.kancloud.cn/yunye/axios/234845)



### **1.新建api文件夹**

> api/index.js

```js
import axios from 'axios';
// import qs from 'qs'

axios.defaults.baseURL = 'http://129.204.92.64:8081/',
axios.defaults.timeout = 5000;
axios.interceptors.request.use(function (config) {

    // alert("加载中");
    // if (config.methods.toLowerCase() == "post") {
    //     config.data = qs.string(config.data)
    // }
    return config;
}, function (error) {
    return Promise.reject(error);
})

axios.interceptors.response.use(function (config) {

    return config;
}, function (error) {
    return Promise.reject(error)
})

export default axios
```



### **2.建api挂载到vue原型上**

> main.js

```js
import ajax from './api/index'
Vue.prototype.$api = ajax; // 将api挂载到vue的原型上
```



### **3.vue页面调用**

> xxx.vue

```js
//查询文章总数
                this.$api({
                    url: '/api/SnArticle/GetArticleCount'
                }).then(res => {
                    this.ArticleCount = res.data;

                }).catch((e) => {
                    console.log(e + '获取数据失败');
                });
```

### **4.并发使用**

> all

#### 第一种方式

```js
this.$api.all([
    this.$api.get('/api/SnLabels/GetLabels'),
    this.$api.get('/api/SnSort/GetSort'),
    this.$api.get('/api/SnArticle/GetfyTest?label=00&pageIndex=1&pageSize=10&isDesc=true'),
]).then(res =>
   {
        this.Labels=res[0].data;
        this.Sort = res[1].data;
       this.article = res[2].data;
        // console.log(res[1].data[0].userBrief)
    }
).catch(err=>{
    console.log(err)
});
```

#### 第二种方式

```js
 this.$api.all([
                    //查询标签
                    this.$api.get('/api/SnLabels/GetLabels'),
                    //查询分类
                    this.$api.get('/api/SnSort/GetSort'),
                    //查询最新发布前十文章
                    this.$api.get('/api/SnArticle/GetfyTest?label=00&pageIndex=1&pageSize=10&isDesc=true'),
                    // 查询当前用户的说说
                    this.$api.get('/api/SnUserTalk/GetUserTalkFirst?UserId=4&isdesc=true'),

                ]).then(this.$api.spread((res1,res2,res3,res4)=>
                    {
                        this.Labels=res1.data;
                        this.Sort = res2.data;
                        this.article = res3.data;
                        this.UserTalk =res4.data

                    })
                ).catch(err=>{
                    console.log(err)
                });
```

