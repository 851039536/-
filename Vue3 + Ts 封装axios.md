# Vue3 + Ts 封装axios

### 封装思路

index.ts
我们需要在src的根目录下创建一个axios文件夹，其中创建一个index.ts文件，这个文件主要用来封装axios的配置（实例化请求配置、请求拦截器、相应拦截器）及相应的方法（登录跳转、消息提示、错误处理等）

base.ts
这个文件主要用于项目扩展的情况下 不同模块需要调用不同接口（请求的base地址 baseURL ）而前期做的准备，便于后期的维护

request.ts
主要用于封装基于axios配置的get/post/put/delete等使用方法。

api.ts
在后面的 main.ts 中引入该模块，包括所有接口数据信息写入该文件中。

### index.ts

封装如下。考虑到单一职责，index这块只封装axios

```js
// index.ts
import axios, { AxiosRequestConfig, Method } from "axios";
import router from "@/router";
import store from "@/store";
import { message } from 'ant-design-vue'
import { storage } from "../storage/storage";
import { dataList } from "@/components/aspin/data";

/** 
 * 跳转登录页
 * 携带当前页面路由，以期在登录页面完成登录后返回当前页面
 */
const toLogin = () => {
  router.replace({
    name: 'LoginPage',
  });
}

/** 
 * 请求失败后的错误统一处理 
 * @param {Number} status 请求失败的状态码
 */
const errorHandle = (status: number, other: string) => {
  // 状态码判断
  switch (status) {

    case 302: message.error('接口重定向了！');
      break;
    case 400:
      message.error("发出的请求有错误，服务器没有进行新建或修改数据的操作==>" + status)
      break;
    // 401: 未登录
    // 未登录则跳转登录页面，并携带当前页面的路径
    // 在登录成功后返回当前页面，这一步需要在登录页操作。                
    case 401: //重定向
      message.error("token:登录失效==>" + status + ":" + store.state.Roles)
      storage.remove(store.state.Roles)
      storage.get(store.state.Roles)
      router.replace({
        path: '/Login',
      });
      break;
    // 403 token过期
    // 清除token并跳转登录页
    case 403:
      message.error("登录过期,用户得到授权，但是访问是被禁止的==>" + status)
      // store.commit('token', null);
      setTimeout(() => {
        router.replace({
          path: '/Login',
        });
      }, 1000);
      break;
    case 404:
      message.error("网络请求不存在==>" + status)
      break;
    case 406:
      message.error("请求的格式不可得==>" + status)
      break;
    case 408: message.error(" 请求超时！")
      break;
    case 410:
      message.error("请求的资源被永久删除，且不会再得到的==>" + status)
      break;
    case 422:
      message.error("当创建一个对象时，发生一个验证错误==>" + status)
      break;
    case 500:
      message.error("服务器发生错误，请检查服务器==>" + status)
      break;
    case 502:
      message.error("网关错误==>" + status)
      break;
    case 503:
      message.error("服务不可用，服务器暂时过载或维护==>" + status)
      break;
    case 504:
      message.error("网关超时==>" + status)
      break;
    default:
      message.error("其他错误错误==>" + status)
  }
}

// 定义接口
interface PendingType {
  url?: string;
  method?: Method;
  params: any;
  data: any;
  cancel: any;
}
// 取消重复请求
const pending: Array<PendingType> = [];
const CancelToken = axios.CancelToken;

// 移除重复请求
const removePending = (config: AxiosRequestConfig) => {
  for (const key in pending) {
    const item: number = +key;
    const list: PendingType = pending[key];
    // 当前请求在数组中存在时执行函数体
    if (list.url === config.url && list.method === config.method && JSON.stringify(list.params) === JSON.stringify(config.params) && JSON.stringify(list.data) === JSON.stringify(config.data)) {
      // 执行取消操作
      list.cancel('操作太频繁，请稍后再试');
      // 从数组中移除记录
      pending.splice(item, 1);
    }
  }
};

/* 实例化请求配置 */
const instance = axios.create({
  headers: {
    //php 的 post 传输请求头一定要这个 不然报错 接收不到值
    "Content-Type": "application/json;charset=UTF-8",
    "Access-Control-Allow-Origin-Type": '*'
  },
  // 请求时长
  timeout: 1000 * 30,
  // 请求的base地址 TODO:这块以后根据不同的模块调不同的api
  baseURL: process.env.VUE_APP_API_URL,
  //     ? "测试"
  //     : "正式",
  // 表示跨域请求时是否需要使用凭证
  withCredentials: false,
})

/** 
 * 请求拦截器 
 * 每次请求前，如果存在token则在请求头中携带token 
 */
instance.interceptors.request.use(
  config => {

    removePending(config);
    config.cancelToken = new CancelToken((c) => {
      pending.push({ url: config.url, method: config.method, params: config.params, data: config.data, cancel: c });
    });
    // 登录流程控制中，根据本地是否存在token判断用户的登录情况        
    // 但是即使token存在，也有可能token是过期的，所以在每次的请求头中携带token        
    // 后台根据携带的token判断用户的登录情况，并返回给我们对应的状态码        
    // 而后我们可以在响应拦截器中，根据状态码进行一些统一的操作。        
    // const token = store.state.token;
    // localStorage.setItem('token', token);

    if (storage.get(store.state.Roles)) {
      store.state.Roles
      config.headers.Authorization = storage.get(store.state.Roles);
    }
    return config;
  },
  error => {
    message.error(error.data.error.message);
    return Promise.reject(error.data.error.message);
  }

)

// 响应拦截器
instance.interceptors.response.use(function (config) {

  dataList.show = true
  removePending(config.config);
  // 请求成功
  if (config.status === 200 || config.status === 204) {
    setTimeout(() => {
      dataList.show = false
    }, 400)
    return Promise.resolve(config);
  } else {
    return Promise.reject(config);
  }
  // 请求失败
}, function (error) {

  const { response } = error;
  if (response) {
    errorHandle(response.status, response.data.message);

    // 超时重新请求
    const config = error.config;
    // 全局的请求次数,请求的间隙
    const [RETRY_COUNT, RETRY_DELAY] = [3, 1000];

    if (config && RETRY_COUNT) {
      // 设置用于跟踪重试计数的变量
      config.__retryCount = config.__retryCount || 0;
      // 检查是否已经把重试的总数用完
      if (config.__retryCount >= RETRY_COUNT) {
        return Promise.reject(response || { message: error.message });
      }
      // 增加重试计数
      config.__retryCount++;
      // 创造新的Promise来处理指数后退
      const backoff = new Promise<void>((resolve) => {
        setTimeout(() => {
          resolve();
        }, RETRY_DELAY || 1);
      });
      // instance重试请求的Promise
      return backoff.then(() => {
        return instance(config);
      });
    }

    return Promise.reject(response);
  } else {
    // 处理断网的情况
    // eg:请求超时或断网时，更新state的network状态
    // network状态在app.vue中控制着一个全局的断网提示组件的显示隐藏
    // 后续增加断网情况下做的一些操作
    store.commit('networkState', false);
  }
}
)
// 只需要考虑单一职责，这块只封装axios
export default instance
```

### base.ts

区分每个模块的 baseUrl 方便后期维护管理

```js
// base.ts
export class Base {
  /* 公共模块 */
  static env = process.env.NODE_ENV === "development"
    ? "http://localhost:8087"
    : "https://produceCommon.com(生产线地址)"
}
```

也可以直接在index.ts中设置这样就不需要base.ts

```js
const instance = axios.create({
  // 请求的base地址 TODO:这块以后根据不同的模块调不同的api
  baseURL: process.env.VUE_APP_API_URL,
})
```

需要配置根目录

.env.development

```
     NODE_ENV = 'development'
 	 # VUE_APP_API_URL = 'https://localhost:5001/'
    VUE_APP_API_URL = 'http://localhost:8087/'
```

.env.production

```
# 生产环境的请求接口
NODE_ENV = 'production'
VUE_APP_API_URL = 'http://129.xxxxx/'
```

### request.ts

封装axios的get、post方法，其余关于接口调用的方法也可写入该文件中，便于管理。

```js
// request.ts
import axios from "./index";
import qs from "qs";

export class Request {
  /**
   * get方法
   * @param {string} url 路径
   * @param {object} params 参数
   */
  static get = (url: string, params?: any) => {
    return new Promise((resolve, reject) => {
      axios.get(url, { params: params }).then(res => {
        resolve(res);
      }).catch(err => {
        reject(err);
      })
    })
  }

  static post = (url: string, params?: any) => {
    return new Promise((resolve, reject) => {
      axios.post(url, qs.stringify(params)).then(res => {
        resolve(res);
      }).catch(err => {
        reject(err);
      })
    })
  }
}
```

### api.ts

vue页面需要使用的api接口

```js
// 其中使用 install 的目的在于 ts在main.ts中 
// 不能通过Vue.prototype.$Api这个方式直接调用
//，在全局方法中会说到使用 插件的方式去挂载。
// api.ts
import { Base } from "./base";
import { Request } from "./request";

class api {
  /* api接口模块 */
  public static article = {
     // 直接在index.ts中设置不需要Base模块
    genre: () => Request.get('/api/SnArticle/GetCountAsync'),
     // 基于Base模块封装调用
    genres: () => Request.get(`${Base.env}/api/SnArticle/GetCountAsync`),
  }
}
export {
  api
}
```

### index.vue

```js
import { api } from '../../utils/api/api'
import { onMounted } from 'vue'
onMounted(async () => {
  await QueryAll()
  api.article.genre().then((res: any) => {
    console.log('genre' + res.data)
  })
  api.article.genres().then((res: any) => {
    console.log('genres' + res.data)
  })
})
```

### 参考

https://blog.csdn.net/qq_40031303/article/details/113920135