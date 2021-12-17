原文:[net core 3.1 跨域 Cors 找不到 “Access-Control-Allow-Origin”](https://www.cnblogs.com/xiaotimor/p/12068984.html)

### 首先在ConfigureServices添加



```c#
public void ConfigureServices(IServiceCollection services)
        {
            services.AddCors(options =>
            {
                options.AddPolicy("any", builder =>
                {
                    //builder.AllowAnyOrigin() //允许任何来源的主机访问
                    builder
                    
                    .WithOrigins("http://*.*.*.*")//.SetIsOriginAllowedToAllowWildcardSubdomains()//设置允许访问的域

                    .AllowAnyMethod()

                    .AllowAnyHeader()

                    .AllowCredentials();//

                });

            });
            services.AddControllers();
        }
```



### 然后新增CorsMiddleware 类

```c#
public class CorsMiddleware
    {
        private readonly RequestDelegate _next;
        public CorsMiddleware(RequestDelegate next)
        {
            _next = next;
        }
        public async Task Invoke(HttpContext context)
        {
            if (!context.Response.Headers.ContainsKey("Access-Control-Allow-Origin"))
            {
                context.Response.Headers.Add("Access-Control-Allow-Origin", "*");
            }
            await _next(context);
        }
    }
```

### 然后在Configure 使用中间件

```c#
 app.UseMiddleware<CorsMiddleware>();
```

