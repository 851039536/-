# Swagger配置



## 添加并配置 Swagger 中间件

将 Swagger 生成器添加到 Startup.ConfigureServices 方法中的 服务集合中：

ConfigureServices

```c#
//注册Swagger生成器，定义一个和多个Swagger 文档
            services.AddSwaggerGen(c =>
                {
                    c.SwaggerDoc("1.0", new OpenApiInfo
                    {
                        Title = " KaiOuYang", //标题  
                        Version = "1.0.1"  ,   //版本
                        Description = "A simple example ASP.NET Core Web API",
         
      
                    });
                 
                    var xmlFile = $"{Assembly.GetExecutingAssembly().GetName().Name}.xml";
                    var xmlPath = Path.Combine(AppContext.BaseDirectory, xmlFile);
                    c.IncludeXmlComments(xmlPath,true);
                });
```



在 `Startup.Configure` 方法中，启用中间件为生成的 JSON 文档和 Swagger UI 提供服务：

```c#
//启用中间件服务生成Swagger作为JSON终结点
            app.UseSwagger();
            //启用中间件服务对swagger-ui，指定Swagger JSON终结点
            app.UseSwaggerUI(c =>
                {
                    c.SwaggerEndpoint("/swagger/1.0/swagger.json", "TE后台管理");
                    c.RoutePrefix = string.Empty;
                });
```

## 启用XML 注释

可使用以下方法启用 XML 注释：

- 右键单击“解决方案资源管理器”中的项目，然后选择“属性”
- 查看“生成”选项卡的“输出”部分下的“XML 文档文件”框

启用 XML 注释后会为未记录的公共类型和成员提供调试信息。如果出现很多警告信息 例如，以下消息指示违反警告代码 1591：

`warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'`



## Swagger UI 显示上述注释代码的 

```c#
/// <summary>
 /// 这是一个带参数的get请求
 /// </summary>
 /// <remarks>
 /// 例子:
 /// Get api/Values/1
 /// </remarks>
 /// <param name="id">主键</param>
 /// <returns>测试字符串</returns>          
```



## 描述响应类型

```c#
 /// <summary>
 /// 这是一个带参数的get请求
 /// </summary>
 /// <remarks>
 /// 例子:
 /// Get api/Values/1
 /// </remarks>
 /// <param name="id">主键</param>
 /// <returns>测试字符串</returns> 
 /// <response code="201">返回value字符串</response>
/// <response code="400">如果id为空</response>  
 // GET api/values/2
[HttpGet("{id}")]
[ProducesResponseType(201)]
[ProducesResponseType(400)]
public ActionResult<string> Get(int id)
{
     return $"你请求的 id 是 {id}";
}
```

