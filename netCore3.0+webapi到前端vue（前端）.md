# netCore3.0+webapi到前端vue（前端）

前篇已经完成后端配置并获取到api连接 https://www.cnblogs.com/ouyangkai/p/11504279.html

前端项目用的是VS code编译器 vue完成 

### 前端引入

 

```js
  <script src="lib/vue.js"></script>
  <script src="lib/vue-resource.js"></script>
  <script src="lib/axios.min.js"></script>
  <link rel="stylesheet" href="lib/bootstrap.css">
```

 

### app.vue

```js
<body>
  <div id="app">
    <div class="panel panel-primary">
      <div class="panel-heading">
        <h3 class="panel-title">Panel title</h3>
      </div>
      <div class="panel-body">
        <div class="row">
          <div class="col-lg-6">
            <div class="input-group">
              <input type="text" class="form-control" placeholder="Search for...">
              <span class="input-group-btn">
                <button class="btn btn-default" type="button">Go!</button>
              </span>
            </div><!-- /input-group -->
          </div><!-- /.col-lg-6 -->
        </div><!-- /.row -->
      </div>
    </div>
    <table class="table table-bordered table-hover table-striped">
      <thead>
        <tr>
          <th>标题</th>
          <th>类别</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in list" :key="item.id" @click.prevent="quTest(item.id)">
          <td>{{item.method}}</td>
          <td>{{item.type}}</td>
        </tr>
      </tbody>
    </table>
  </div>
  <script>
    var vm = new Vue({
      el: '#app',
      data: {
        list: [],
        id: ''
      },
      created() {
        this.getlist()
      },
      methods: {
        getlist() {
          let _this = this; //注意，这里是在函数体内部，不是在methods中
          axios.get('https://localhost:44323/api/values').then(res => {
            _this.list = res.data;  //注意这里前面用**_this**来保证数据是绑定到Vue实例上的
          })
        },
        quTest(id) {
          console.log(id)
        }
      }
    })
  </script>
</body>
```

注意以上是用 axios.get方式获取后端api链接获取数据，并利用vue渲染到前端页面显示

 

用vscode运行前端页面出现以下错误

![img](https://img2018.cnblogs.com/blog/1122066/201909/1122066-20190911103310778-701863195.png)

### Access-Control-Allow-Origin
Access-Control-Allow-Origin是HTML5中定义的一种服务器端返回Response header，用来解决资源（比如字体）的跨域权限问题。
它定义了该资源允许被哪个域引用，或者被所有域引用（google字体使用*表示字体资源允许被所有域引用）。

我们遇到前后端交互出现跨域问题

解决方案如下：

在后端项目 Startup.cs 进行跨域配置

```js
 public void ConfigureServices(IServiceCollection services)
        {
            //连接 mysql 数据库，添加数据库上下文
            services.AddDbContext<DbModel>(options =>
            options.UseMySQL(Configuration.GetConnectionString("DefaultConnection"))); 
            services.AddControllers();
            services.AddCors(options =>
                {
                    //全局起作用
                    options.AddDefaultPolicy(
                        builder =>
                        {
                            builder.AllowAnyHeader().AllowAnyMethod().AllowAnyOrigin();
                        });
                                     
                    options.AddPolicy("AnotherPolicy",
                        builder =>
                        {
                            builder.WithOrigins("http://www.contoso.com")
                                .AllowAnyHeader()
                                .AllowAnyMethod();
                        });

                });
          
        }
  public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }
            //使用 Cors
            app.UseCors();         
        }
```

 



### 再次运行

![img](https://img2018.cnblogs.com/blog/1122066/201909/1122066-20190911104043782-1381425488.png)

 

至此前端用后端api获取到数据渲染到页面完成！

 

