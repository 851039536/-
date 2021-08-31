# netcore使用AngleSharp爬取数据

### 通过NuGet获取AngleSharp

```c#
Install-Package AngleSharp
```

### 创建AngleSharpController.cs

```c#
using Microsoft.AspNetCore.Mvc;
using Snblog.Service.AngleSharp;
using System.Threading.Tasks;

//默认的约定集将应用于程序集中的所有操作：
[assembly: ApiConventionType(typeof(DefaultApiConventions))]
namespace Snblog.ControllersAngleSharp
{
    [Route("api/[controller]")]
    [ApiExplorerSettings(GroupName = "AngleSharp")] //版本控制
    [ApiController]
    public class AngleSharpController : ControllerBase
    {
        private readonly HotNewsAngleSharp _angle; //IOC依赖注入
        #region 构造函数
        public AngleSharpController(HotNewsAngleSharp angle)
        {
            _angle = angle;
        }
        #endregion
        /// <summary>
        /// 读取博客园最新文章
        /// </summary>
        /// <returns></returns>
        [HttpGet("Cnblogs")]
        public async Task<IActionResult> Cnblogs() => Ok(await _angle.Cnblogs());
          /// <summary>
        /// 读取项目名称
        /// </summary>
        /// <returns></returns>
        [HttpGet("GiteeItem")]
        public async Task<IActionResult> GiteeItem() => Ok(await _angle.GiteeItem());
    }
}

```

### Startup.cs注入

```c#
services.AddScoped<HotNewsAngleSharp, HotNewsAngleSharp>();
```



### 测试调用

**AngleSharpController.cs**

```c#
using Microsoft.AspNetCore.Mvc;
using Snblog.Service.AngleSharp;
using System.Threading.Tasks;

//默认的约定集将应用于程序集中的所有操作：
[assembly: ApiConventionType(typeof(DefaultApiConventions))]
namespace Snblog.ControllersAngleSharp
{
    [Route("api/[controller]")]
    [ApiExplorerSettings(GroupName = "AngleSharp")] //版本控制
    [ApiController]
    public class AngleSharpController : ControllerBase
    {
        private readonly HotNewsAngleSharp _angle; //IOC依赖注入
        #region 构造函数
        public AngleSharpController(HotNewsAngleSharp angle)
        {
            _angle = angle;
        }
        #endregion
        /// <summary>
        /// 读取博客园最新文章
        /// </summary>
        /// <returns></returns>
        [HttpGet("Cnblogs")]
        public async Task<IActionResult> Cnblogs() => Ok(await _angle.Cnblogs());
          /// <summary>
        /// 读取项目名称
        /// </summary>
        /// <returns></returns>
        [HttpGet("GiteeItem")]
        public async Task<IActionResult> GiteeItem() => Ok(await _angle.GiteeItem());
    }
}

```

### 例子



```c#
获取p元素的NodeList
var matches = document.querySelectorAll("p");

返回所有div元素列表 其中class包含 note或alert
var matches = document.querySelectorAll("div.note, div.alert");

在这里，我们得到一个<p>元素的列表，其直接父元素是一个class为"highlighted"的div，并且位于ID为"test"的容器内。
var container = document.querySelector("#test");
var matches = container.querySelectorAll("div.highlighted > p");

此示例使用属性选择器返回文档中属性名为"data-src"的iframe元素列表：
var matches = document.querySelectorAll("iframe[data-src]");

这里，属性选择器用于返回ID为"userlist"的列表中包含值为"1"的"data-active"属性的元素
var container = document.querySelector("#userlist");
var matches = container.querySelectorAll("li[data-active='1']");
```

