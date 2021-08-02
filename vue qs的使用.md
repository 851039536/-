# vue qs的使用

### 什么是qs?

qs是一个流行的查询参数序列化和解析库。可以将一个普通的object序列化成一个查询字符串，或者反过来将一个查询字符串解析成一个object,帮助我们查询字符串解析和序列化字符串。

### vue中qs安装使用



安装`npm install qs`

导入`import qs from 'qs'`

序列化 

```js
  config.data = qs.parse(config.data);
```



### 实例

```ts
 // 阅读数
      const UpRead = (info: any): void => {
        if (info == null) {
          console.log(info);
          return;
        } else {
          console.log(info.read);
          info.read++;
          console.log(state.id + "-" + info.read);
          proxy
            .$api({
              // 更新
              url: "/api/SnArticle/UpdatePortionAsync?type=read",
              method: "put",
              data: info
            })
            .then((res: any) => {
              if (res.status === 200) {
                // console.log("1");
              } else {
                alert("更新失败");
              }
            })
            .catch(console.error.bind(console)); 
        }
      };
```

