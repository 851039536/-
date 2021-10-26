# netCore3.0+webapi到前端vue（后端） 

### 创建api项目

![img](https://img2018.cnblogs.com/blog/1122066/201909/1122066-20190911082754260-1705621391.png)

 

 

创建完成启动F5！！

如图![img](https://img2018.cnblogs.com/blog/1122066/201909/1122066-20190911083206882-1317568501.png)

 

数据库我用的是mysql 用efcore框架进行数据操作 

> 开发环境：Win10 + VS2019
>         Mysql服务器版本：8.0.16

 

### 下载并安装插件(必备)

```
MySQL-Connector-net-6.9.12
MySQL for Visual Studio 2.0.5
```

用Nuget方式安装MySql.Data.Entity-6.9.12（MySql.Data.EntityFrameworkCore.Design！！两种都试过没问题，前面一种会报提示不兼容），MySql.Data-6.9.12  MySql.Data.EntityFrameworkCore
注意！！！ 安装的2个dll版本号必须一致以及对应MySQL-Connector-net版本相同

![img](https://img2018.cnblogs.com/blog/1122066/201909/1122066-20190911083834584-1531074870.png)

 

### 根目录新建Models文件

创建实体类 gj

```js
public class gj 
{
    // <summary>
    /// 主键
    /// </summary>
    public int id { get;
    set;
}
/// <summary>
/// 标题
/// </summary>
public string method 
{
    get;
    set;
}
/// <summary>
/// 内容
/// </summary>
public string text 
{
    get;
    set;
}
/// <summary>
/// 状态 1正常 0删除
/// </summary>
public string type 
{
    get;
    set;
}
}
public class DbModel:DbContext 
{
public DbSet<gj> gj 
{
    set;
    get;
}
//protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
//    => optionsBuilder.UseMySQL(@"Server=localhost;database=testapp;uid=root;pwd=woshishui");
public DbModel(DbContextOptions<DbModel> options) : base(options) 
{
}
}
```

 

### appsettings.json 

配置数据连接

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=xxxx;database=testapp;uid=root;pwd=xxxx;"
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

 

### Startup.cs

```js
 public void ConfigureServices(IServiceCollection services)
        {
            //连接 mysql 数据库，添加数据库上下文
            services.AddDbContext<DbModel>(options =>
                options.UseMySQL(Configuration.GetConnectionString("DefaultConnection")));

            services.AddControllers();
          
        }
```

 

### Controllers 

ValuesController.cs修改代码如下

```js
 private readonly DbModel _coreDbContext;

        public ValuesController(DbModel coreDbContext)
        {
            _coreDbContext = coreDbContext;
        }

        // GET api/values
        [HttpGet]   
        public List<gj> Get()
        {
            return _coreDbContext.Set<gj>().ToList();
          
        }
```

 

### 完成配置运行项目

测试如下

![img](https://img2018.cnblogs.com/blog/1122066/201909/1122066-20190911085528704-1010518489.png)

 

至此就完成了后端api项目

 前端配置链接 https://www.cnblogs.com/ouyangkai/p/11504928.html