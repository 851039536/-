# netcore3.1API+efcore快速搭建

### 框架结构

#### 实体层  Aer.Enties 

> Models-->实体类
>

#### 业务层  After.IService   

> IAltestitemController.cs
>

####  业务层   After.Service

> AlltestitemService.cs
>

#### 接口层  AfterCore

> Controllers-> AlltestitemController.cs



### 一 EF从数据库生成实体类到Enties

#### 1.执行以下语句安装依赖包

```c#
Install-Package MySql.Data.EntityFrameworkCore -Pre
Install-Package Pomelo.EntityFrameworkCore.MySql
Install-Package Microsoft.EntityFrameworkCore.Tools
Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design
```

#### 2.在程序包包管理器控制台

```c#
Scaffold-DbContext "server=localhost;userid=root;pwd=1;port=3306;database=syerp;sslmode=none;" Pomelo.EntityFrameworkCore.MySql -OutputDir Models -Force
```



### 二 业务层

> 添加IAlltestitemService.cs接口

```c#
using AfterCore.Models;
using System.Collections.Generic;
using System.Threading.Tasks;

namespace After.IService
{
   public interface IAlltestitemService
    {
        /// <summary>
        /// 查询所有
        /// </summary>
        /// <returns></returns>
        Task<List<Alltestitem>> GetAllAsync();

        /// <summary>
        /// 查询总数
        /// </summary>
        /// <returns></returns>
        Task<int> CountAsync();
    }
}

```

> After.Service接口实现

AlltestitemService.cs

```c#
using After.IService;
using AfterCore.Models;
using Microsoft.EntityFrameworkCore;
using System.Collections.Generic;
using System.Threading.Tasks;

namespace After.Service
{
    public class AlltestitemService : IAlltestitemService
    {
        private readonly testContext _testDbContext;//DB
        public AlltestitemService(testContext testContexts)
        {
            _testDbContext = testContexts;
        }

        public async Task<int> CountAsync()
        {
            return await _testDbContext.Alltestitem.CountAsync();
        }

        public async Task<List<Alltestitem>> GetAllAsync()
        {
            return await _testDbContext.Alltestitem.ToListAsync();

        }
    }
}

```

### 三 注册DbContext



#### appsettings.json

```c#
"ConnectionStrings": {
    "DefaultConnection": "Server=;database=test;uid=root;pwd=m;"
  },
```

#### Startup.cs

```c#
  //注册DbContext  ConfigureServices
            services.AddDbContext<testContext>(options => options.UseMySql(Configuration.GetConnectionString("DefaultConnection")));
services.AddControllers();
 services.AddScoped<IAlltestitemService, AlltestitemService>();//ioc
```



### 四 添加Swagger



#### 安装依赖包

```c#
Swashbuckle.AspNetCore
```

#### ConfigureServices(IServiceCollection services)

```c#
//注册swagger服务
            services.AddSwaggerGen(c =>
              {
                  c.SwaggerDoc("v1", new OpenApiInfo
                  {
                      Version = "v1",
                      Title = "SN博客 API",
                      Description = "EFCore数据操作 ASP.NET Core Web API",
                      TermsOfService = new Uri("https://example.com/terms"),
                      Contact = new OpenApiContact
                      {
                          Name = "Shayne Boyer",
                          Email = string.Empty,
                          Url = new Uri("https://twitter.com/spboyer"),
                      },
                      License = new OpenApiLicense
                      {
                          Name = "Use under LICX",
                          Url = new Uri("https://example.com/license"),
                      }
                  });

                  // 为 Swagger 设置xml文档注释路径
                  var basePath2 = AppContext.BaseDirectory;// xml路径
                  //var xmlModelPath = Path.Combine(basePath2, "Snblog.Enties.xml");//Model层的xml文件名
                  var corePath = Path.Combine(basePath2, "AfterCore.xml");//API层的xml文件名
                  //c.IncludeXmlComments(xmlModelPath);
                  c.IncludeXmlComments(corePath, true);
                  //添加对控制器的标签(描述)
                  c.CustomSchemaIds(type => type.FullName);// 可以解决相同类名会报错的问题

                  // c.OperationFilter<AddResponseHeadersFilter>();
                  // c.OperationFilter<AppendAuthorizeToSummaryOperationFilter>();
                  //
                  // c.OperationFilter<SecurityRequirementsOperationFilter>();

              });
```

####  Configure(IApplicationBuilder app, IWebHostEnvironment env)

```c#
 #region Swagger
            //可以将Swagger的UI页面配置在Configure的开发环境之中
            app.UseSwagger();
            //和Swagger UI
            app.UseSwaggerUI(c =>
            {
                c.SwaggerEndpoint("/swagger/v1/swagger.json", "SN博客API");
                c.RoutePrefix = string.Empty;
            });
            #endregion
```



### 五 显示层AfterCore

Controllers->AlltestitemController.cs

```c#
using After.IService;
using Microsoft.AspNetCore.Mvc;
using System.Threading.Tasks;
//默认的约定集将应用于程序集中的所有操作：
[assembly: ApiConventionType(typeof(DefaultApiConventions))]
namespace AfterCore.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class AlltestitemController : ControllerBase
    {
          private readonly IAlltestitemService _service; //IOC依赖注入

         public AlltestitemController(IAlltestitemService service)
        {
            _service = service;
        
        }
        /// <summary>
        /// 查询所有
        /// </summary>
        /// <returns></returns>
        // [ApiExplorerSettings(IgnoreApi = true)] //隐藏接口 或者直接对这个方法 private，也可以直接使用obsolete属性
        [HttpGet("GetAllAsync")]
        public async Task<IActionResult> GetAllAsync()
        {
            return Ok(await _service.GetAllAsync());
        }

         /// <summary>
        /// 查询总数
        /// </summary>
        /// <returns></returns>
        // [ApiExplorerSettings(IgnoreApi = true)] //隐藏接口 或者直接对这个方法 private，也可以直接使用obsolete属性
        [HttpGet("CountAsync")]
        public async Task<IActionResult> CountAsync()
        {
            return Ok(await _service.CountAsync());
        }
    }
}

```



#### 运行

![image-20201103171808889](C:\Users\CH190006\AppData\Roaming\Typora\typora-user-images\image-20201103171808889.png)