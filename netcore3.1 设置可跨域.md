# netcore3.1 设置可跨域

### 1、Startup类里先定义一个全局变量：

```c#
  readonly string MyAllowSpecificOrigins = "_myAllowSpecificOrigins";//名字随便起
```

### 2、ConfigureServices方法里写如下代码：




```c#
         services.AddCors(options =>
            {
               options.AddPolicy(MyAllowSpecificOrigins,
                    
       builder => builder.AllowAnyOrigin()
                
                .WithMethods("GET", "POST", "HEAD", "PUT", "DELETE", "OPTIONS")
 
                );
 
        });
```
### 3、Configure方法里添加中间件：

 

```c#
 app.UseCors(MyAllowSpecificOrigins);
```


CORS 中间件必须配置为在对 UseRouting 和 UseEndpoints的调用之间执行。 配置不正确将导致中间件停止正常运行。


原文链接：https://blog.csdn.net/puzi0315/article/details/104326328/