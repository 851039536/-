# Web API中使用Swagger进行版本控制

### 添加API枚举类型

```c#
 /// <summary>
        /// 版本控制
        /// </summary>
        public enum ApiVersion
        {
            /// <summary>
            /// v1版本
            /// </summary>
            V1 = 1,
            /// <summary>
            /// v2版本
            /// </summary>
            V2 = 2
        }
```

### **注册Swagger服务**

```c#
 //遍历版本信息
                  typeof(ApiVersion).GetEnumNames().ToList().ForEach(version =>
                  {
                      c.SwaggerDoc(version, new OpenApiInfo
                      {
                          //Version = "v1", //版本号
                          Title = "SN博客 API", //标题
                          Description = "EFCore数据操作 ASP.NET Core Web API", //描述
                          TermsOfService = new Uri("https://example.com/terms"), //服务条款
                          Contact = new OpenApiContact
                          {
                              Name = "Shayne Boyer", //联系人
                              Email = string.Empty,  //邮箱
                              Url = new Uri("https://twitter.com/spboyer"),//网站
                          },
                          License = new OpenApiLicense
                          {
                              Name = "Use under LICX", //协议
                              Url = new Uri("https://example.com/license"), //协议地址
                          }
                      });
                  });
```

### **启用Swagger**

```c#
  app.UseSwaggerUI(c =>
            {

                typeof(ApiVersion).GetEnumNames().ToList().ForEach(version =>
               {
                   c.IndexStream = () => GetType().GetTypeInfo()
                        .Assembly.GetManifestResourceStream("Snblog.index.html");
                   ////设置首页为Swagger
                   c.RoutePrefix = string.Empty;
                   //自定义页面 集成性能分析
                   c.SwaggerEndpoint($"/swagger/{version}/swagger.json", version);
                   ////设置为none可折叠所有方法
                   c.DocExpansion(DocExpansion.None);
                   ////设置为-1 可不显示models
                   c.DefaultModelsExpandDepth(-1);
               });
            });
```

### **在控制器中使用ApiExplorerSettings标记**

```c#
[ApiExplorerSettings(GroupName = "V1")]
```

