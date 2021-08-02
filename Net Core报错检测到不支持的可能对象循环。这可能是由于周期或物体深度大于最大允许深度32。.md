# Net Core报错检测到不支持的可能对象循环。这可能是由于周期或物体深度大于最大允许深度32。



解决方法：

1.添加 **Microsoft.AspNetCore.Mvc.NewtonsoftJson** 包

2.Startup中添加服务，忽略循环引用

```
services.AddControllers().AddNewtonsoftJson(option =>
                //忽略循环引用
                option.SerializerSettings.ReferenceLoopHandling = ReferenceLoopHandling.Ignore
            );

```

