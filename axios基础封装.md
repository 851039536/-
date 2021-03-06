# axios基础封装

### request.js

新建文件夹 network 在文件新建 request.js

```js
import axios from 'axios'
export function request (config) {
  // 1创建axios实例
  const intstance = axios.create({
    baseURL: 'https://localhost:44314',
    timeout: 5000
    // contentType: 'application/json;charset=UTF-8',
    // dataType: 'json'
  })
  // 1.axios拦截器
  intstance.interceptors.request.use(config => {
    return config
  }, er => {})
  // 1.axios响应拦截拦截
  intstance.interceptors.response.use(res => {
    return res
  }, er => {
  })
  return intstance(config)
}
export default request //导出
```

### main.挂载配置

```js
import request from '@/network/request'

new Vue({
  el: '#app',
  router,
  store,
  request, // 封装的axios
  components: { App },
  template: '<App/>'

})
```

### vue组件调用

在需要使用axios的vue组件调用

```js
import request from '@/network/request'

  request({ // 查询所有
        url: '/api/Gj'
      }).then(res => {
        this.tableData = res.data
      })

 request({ // 条件查询
        url: '/api/Gjtype/' + gjtype
      }).then(res => {
        this.tableData = res.data
      })


 request({ // 添加
        url: '/api/Gj',
        method: 'POST',
        data: {
          'id': 0,
          'method': this.method,
          'text': this.content,
          'type': this.value
        }
      }).then(res => {
        console.log(res)
        if (res.status === 201) {
          this.open1()
          console.log('成功')
        } else {
          this.open4()
          console.log('失败')
        }
      }).catch(console.error.bind(console)) // 异常


request({// 更新
        url: '/api/Gj/' + this.id,
        method: 'put',
        data: {
          'id': this.newinfo.id,
          'method': this.newinfo.method,
          'text': this.newinfo.text,
          'type': this.newinfo.type
       }
      }).then(res => {
        if (res.status === 204) {
          this.open1()
          console.log('成功')
        } else {
          this.open4()
          console.log('失败')
        }
      }).catch(console.error.bind(console)) // 异常
```

 