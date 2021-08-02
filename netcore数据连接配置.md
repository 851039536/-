# netcore数据连接配置

## 第一种

### appsettings.json

```json
{

  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost;database=typecho;uid=root;pwd=woshishui;"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}

```



### ConfigureServices

```c#
￼ services.AddDbContext<typechoContext>(options=>options.UseMySql(Configuration.GetConnectionString("DefaultConnection")));


```



## 第二种

### ConfigureServices

```c#
      // other service configurations go here
            // replace "YourDbContext" with the class name of your DbContext
services.AddDbContextPool<typechoContext>(options => options
            //     // replace with your connection string          .UseMySql("Server=localhost;Database=typecho;User=root;Password=woshishui;", mySqlOptions => mySqlOptions
            //         // replace with your Server Version and Type
    .ServerVersion(new ServerVersion(new Version(8, 0, 19), ServerType.MySql))
            //     ));
```





