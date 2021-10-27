# vue3 axios安装及使用

### 安装

使用npm安装

```
$ npm install axios
```



### 封装axios

```js
/*
 * @Author: Axios封装
 * @Date: 2020-12-08 10:39:03
 * @LastEditTime: 2021-10-22 11:34:08
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \blogs-s\src\api\index.ts
 */
import axios from 'axios';
import qs from "qs";
import store from "@/store/index";
import router from '@/router/index';
import { dataList } from '@/components/aspin/data';
import { message } from 'ant-design-vue';
import { storage } from '../storage/storage';

//数据请求字符
axios.defaults.baseURL = process.env.VUE_APP_API_URL,
  // 如果请求话费了超过 `timeout` 的时间，请求将被中断
  axios.defaults.timeout = 5000;
// 表示跨域请求时是否需要使用凭证
axios.defaults.withCredentials = false;
// axios.defaults.headers.common['token'] =  AUTH_TOKEN
axios.defaults.headers.post['Content-Type'] = 'application/json;charset=UTF-8';
// 允许跨域
axios.defaults.headers.post["Access-Control-Allow-Origin-Type"] = "*";

// 请求拦截器
axios.interceptors.request.use(function (config) {
  if (
    config.method === "post" ||
    config.method === "put" ||
    config.method === "delete"
  ) {
    // qs序列化
    config.data = qs.parse(config.data);
  }
  // 若是有做鉴权token , 就给头部带上token
  if (storage.get(store.state.Roles)) {
    store.state.Roles
    config.headers.Authorization = storage.get(store.state.Roles);
  }
  return config;
}, error => {
  message.error(error.data.error.message);
  return Promise.reject(error.data.error.message);
})

// 响应拦截器
axios.interceptors.response.use(function (config) {


  dataList.show = true
  if (config.status === 200 || config.status === 204) {
    setTimeout(() => {
      dataList.show = false
    }, 400)
    return Promise.resolve(config);
  } else {
    return Promise.reject(config);
  }
},
  function (error) {

    if (error.response.status) {
      switch (error.response.status) {
        case 400:
          message.error("发出的请求有错误，服务器没有进行新建或修改数据的操作==>" + error.response.status)
          break;

        // 401: 未登录
        // 未登录则跳转登录页面，并携带当前页面的路径
        // 在登录成功后返回当前页面，这一步需要在登录页操作。                
        case 401: //重定向
          message.error("token:登录失效==>" + error.response.status + ":" + store.state.Roles)
          storage.remove(store.state.Roles)
          storage.get(store.state.Roles)
          router.replace({
            path: '/Login',
          });
          break;
        // 403 token过期
        // 登录过期对用户进行提示
        // 清除本地token和清空vuex中token对象
        // 跳转登录页面                
        case 403:
          message.error("用户得到授权，但是访问是被禁止的==>" + error.response.status)
          break;
        case 404:
          message.error("网络请求不存在==>" + error.response.status)
          break;
        case 406:
          message.error("请求的格式不可得==>" + error.response.status)
          break;
        case 410:
          message.error("请求的资源被永久删除，且不会再得到的==>" + error.response.status)
          break;
        case 422:
          message.error("当创建一个对象时，发生一个验证错误==>" + error.response.status)
          break;
        case 500:
          message.error("服务器发生错误，请检查服务器==>" + error.response.status)
          break;
        case 502:
          message.error("网关错误==>" + error.response.status)
          break;
        case 503:
          message.error("服务不可用，服务器暂时过载或维护==>" + error.response.status)
          break;
        case 504:
          message.error("网关超时==>" + error.response.status)
          break;
        default:
          message.error("其他错误错误==>" + error.response.status)
      }
      return Promise.reject(error.response);
    } else {
      // 处理断网的情况
      // eg:请求超时或断网时，更新state的network状态
      // network状态在app.vue中控制着一个全局的断网提示组件的显示隐藏
      // 关于断网组件中的刷新重新获取数据，会在断网组件中说明
      store.commit('changeNetwork', false);
    }
  }
)
export default axios
```



### main.ts:

#### 全局使用

```js
import axios from './utils/http/axios'
全局使用
// 全局ctx(this) 上挂载 $axios
app.config.globalProperties.$api = axios
```

 #### 封装使用

```js
/*
 * @Author: your name
 * @Date: 2021-05-08 11:33:56
 * @LastEditTime: 2021-10-22 10:36:05
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \blogs-s\src\api\article.js
 */
import request from '@/utils/http/axios'
import { IntArticle } from "@/api/data/interData";

export class article {

  /**
   * @description: 查询总条数
   */
  static async GetCountAsync() {
    return await request({
      url: "/api/SnArticle/GetCountAsync",
      method: 'get',
    })
  }

  /**
   * @description: 查询分类总条数
   * @param {number} id
   */
  static async ConutSort(id: number) {
    return await request({
      url: "/api/SnArticle/GetConutSortAsync?type=" + id + "&cache=true",
      method: 'get',
    })
  }
  /**
   * @description: 模糊查询
   * @param {string} name
   */
  static async GetContainsAsync(name: string) {
    return await request({
      url: "/api/SnArticle/GetContainsAsync?name=" + name + "&cache=true",
      method: 'get',
    })
  }
  /**
   * @description: 按标签模糊查询
   * @param {string} name
   */
  static async GetTypeContainsAsync(tag: string, name: string, chache: boolean) {
    return await request({
      url: "/api/SnArticle/GetTypeContainsAsync?type=" + tag + "&name=" + name + "&cache=" + chache,
      method: 'get',
    })
  }

  /**
   * @description: 主键查询
   * @param {number} id
   * @param {boolean} cache
   * @return {*}
   */
  static async GetByIdAsync(id: any, cache: boolean): Promise<any> {
    return await request({
      url: "/api/SnArticle/GetByIdAsync?id=" + id + "&cache=" + cache,
      method: 'get',
    })
  }
  /**
   * @description: 按标签条件查询
   * @param {number} id
   * @param {boolean} cache
   * @return {*}
   */
  static async GetTagtextAsync(id: number, cache: boolean): Promise<any> {
    return await request({
      url: "/api/SnArticle/GetTagAsync?labelId=" + id + "&isDesc=true&cache=" + cache,
      method: 'get',
    })
  }
  /**
   * @description: 标签分页查询
   * @param {number} page
   * @param {number} pagesize
   * @return {*}
   */
  static async GetFyTitleAsync(page: number, pagesize: number, isDesc: boolean, cache: boolean): Promise<any> {
    return await request({
      url:
        "/api/SnArticle/GetFyTitleAsync?" +
        "&pageIndex=" +
        page +
        "&pageSize=" +
        pagesize +
        "&isDesc=" + isDesc + "&cache=" + cache,
      method: 'get',
    })
  }

  /**
   * @description: 分类分页查询
   * @param {number} page
   * @param {number} pagesize
   */
  static async GetFySortTitleAsync(page: number, pagesize: number, cache: boolean): Promise<any> {
    return await request({
      url: "/api/SnArticle/GetfySortTestAsync?type=7&pageIndex=" + page + "&pageSize=" + pagesize + "&isDesc=true&cache=" + cache,
      method: 'get',
    })
  }

  /**
   * @description: 更新
   * @param {any} resultData
   * @param {string} type
   */
  static async UpdatePortionAsync(resultData: any, type: string): Promise<any> {
    return await
      request({
        // 更新
        url: "/api/SnArticle/UpdatePortionAsync?type=" + type,
        method: "put",
        data: resultData,
      })
  }

  /**
   * @description: 新增数据
   * @param {any} resultData
   */
  static async AddAsync(resultData: IntArticle) {
    return await
      request({
        url: "/api/SnArticle/AddAsync",
        method: "post",
        data: resultData,
      })
  }
  /**
   * @description: 更新数据
   * @param {IntArticle} resultData
   */
  static async UpdateAsync(resultData: IntArticle) {
    return await
      request({
        url: "/api/SnArticle/UpdateAsync",
        method: "put",
        data: resultData,
      })
  }
  /**
   * @description: 删除
   * @param {number} id
   * @return {*}
   */
  static async DeleteAsync(id: number) {
    return await
      request({
        url: "/api/SnArticle/DeleteAsync?id=" + id,
        method: "delete",
      })
  }
}

```

 

### axios 并发请求

处理并发请求的助手函数

 axios.all(iterable)

 axios.spread(callback)

```js
//示例:
import { article } from '@/api/index';
import { blogsList } from "./components/data";
import axios from '@/utils/http/axios'
class methods {
  static async GetFySortTitle() {
    await article.GetFySortTitleAsync(blogsList.page, blogsList.pagesize, true).then((res: any) => {
      blogsList.dataResult = res.data;
    });
  }
  static async ConutSort() {
    await article.ConutSort(7).then((result: any) => {
      blogsList.count = result.data;
    });
  }
}
async function QueryAll() {
  // axios.all([await methods.ConutSort(), await methods.GetFySortTitle()]).then(axios.spread((Fy, co: any) => {
  //   blogsList.count = co.data;
  //   blogsList.dataResult = Fy.data;
  // }))
  axios.all([await methods.ConutSort(), await methods.GetFySortTitle()])
}

export {
  methods,
  QueryAll
}
```





 

 