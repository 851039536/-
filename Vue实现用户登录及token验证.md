# Vue实现用户登录及token验证



参考：[https://www.cnblogs.com/web-record/p/9876916.html]:



### 验证思路

1 登录成功返回 token

2 拿到token，将token存储到localStorage和vuex中，并跳转路由页面

3 跳转路由，就判断 localStroage 中有无 token

4 调后端接口，都要在请求头中加token

5 前端拿到状态码为401，就清除token信息并跳转到登录页面

### store

```js
import Vue from "vue";
import Vuex from "vuex";
Vue.use(Vuex);

const store = new Vuex.Store({
  state: {
    // 存储token
    Authorization: localStorage.getItem("Authorization")
      ? localStorage.getItem("Authorization")
      : ""
  },

  mutations: {
    // 修改token，并将token存入localStorage
    changeLogin(state, user) {
      state.Authorization = user.Authorization;
      localStorage.setItem("Authorization", user.Authorization);
    }
  }
});
export default store;

```

### login.vue

```js
methods: {
    ...mapMutations(["changeLogin"]),
    doLogin() {
      //一点击登录按钮，这个方法就会执行
      // alert(JSON.stringify(this.user)); //可以直接把this.user对象传给后端进行校验用户名和密码
      this.$api
        .all([
          //总数
          this.$api.get(
            "/api/SnUser/Login?users=" +
              this.user.username +
              "&pwd=" +
              this.user.password
          )
        ])
        .then(
          this.$api.spread(res1 => {
            this.result = res1.data;
            this.result1 = this.result.split(",");
            if (this.result1[0] === "1") {
              this.userToken = "Bearer " + this.result1[1];
              // 将用户token保存到vuex中
              this.changeLogin({ Authorization: this.userToken });
              this.$router.replace("/SnNavigation");
            }
          })
        )
        .catch(err => {
          console.log(err);
        });
    }
  }
```

### routert添加导航守卫

```c#
router.beforeEach((to, from, next) => {
  if (to.path === "/SnLogin") {
    next();
  } else {
    let token = localStorage.getItem("Authorization");

    if (token === "null" || token === "") {
      next("/SnLogin");
    } else {
      next();
    }
  }
});
```

### axios请求头加token

```js
 // 若是有做鉴权token , 就给头部带上token
    if (localStorage.getItem("Authorization")) {
      config.headers.Authorization = localStorage.getItem("Authorization");
      console.log("token" + config.headers.Authorization);
    }
```

