# EFCore——Code First生成数据库与表

Code First——领域设计模式中非常有用。使用 Code First 模式，专注于领域设计，创建领域类，然后生成数据库。

文章目录





## 1.创建数据模型类

一般就是数据库里面有哪些表，就创建哪些模型， POCO 类就够了。

```c#
public partial class SmsPush
    {
        [Key]
        public int Id { get; set; }
        [MaxLength(128)]
        public string AppName { get; set; }

        [DataType(DataType.Text)]
        public string TargetValue { get; set; }

        [DataType(DataType.Text)]
        public string Content { get; set; }

        [DataType(DataType.DateTime)]
        public DateTime CreateTime { get; set; }
    }


```

## 2.安装 nuget 包

按您使用的数据选择包

- sqlserver：`Install-Package Microsoft.EntityFrameworkCore.SqlServer`
- mysql：`Install-Package MySql.Data.EntityFrameworkCore`

## 3.创建数据库上下文类

需要继承`DbContext`

```c#
public class MvcMovieContext : DbContext
    {
        public MvcMovieContext (DbContextOptions<MvcMovieContext> options)
            : base(options)
        {
        }

        public DbSet<Movie> Movie { get; set; }
    }
```



## 4.注册数据库上下文

在应用启动过程中，EF core 数据库上下文服务必须通过依赖注入注册。需要服务，通过构造函数注入即可。

- **sqlserver**

```c#
services.AddDbContext<MvcMovieContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MvcMovieContext")));
```



- **mysql**

```c#
services.AddDbContext<PushContext>(options =>
            {
                options.UseMySQL(Configuration.GetConnectionString("PushContext"));
            });
```



## 5.添加数据连接字符串

上面的`Configuration.GetConnectionString`,是从`appsettings.json`中读取的。所以需要添加相关配置。由于使用的是 mysql，所以相关配置如下

```c#
{
  "ConnectionStrings": {
    "PushContext": "Persist Security Info=False;database=push-center;server=localhost;Connect Timeout=30;user id=root; pwd=111111"
  }
}
```

mysql 链接字符串，参考 mysql 官方：https://dev.mysql.com/doc/dev/connector-net/8.0/html/P_MySql_Data_MySqlClient_MySqlConnection_ConnectionString.htm

## 6.安装 Mysql

由于博主是在 windows 下开发，所以这里介绍最简单的 windows 下安装 mysql 的方法及后续相关操作。这里我们推荐使用巧克力安装 mysql，一款越用越香的 windows 包管理器，如果不知道巧克力，请参考博文【Nginx】Nginx 部署实战——静态文件+反向代理+均衡负载+https+websocket,有关于巧克力的安装，可以类比 centos 的黄狗(yum).

\

```
#安装mysql
choco install mysql
\#遇到让你选择是否运行脚本，Y/A 按着走
```



- 有可能会遇到在https://dev.mysql.com/get/Downloads/MySQL-8.0/mysql-8.0.20-winx64.zip下载巨慢的情况（在公司下载很快，晚上在家下载很慢，不知道为啥，想办法吧），可以在巧克力执行`powershell`之前，也就是提示选择他要执行 `C:\ProgramData\chocolatey\lib\mysql\tools\chocolateyInstall`脚本，去编辑这个脚本，替换为国内资源镜像

\

```
#第三行
$url = 'https://dev.mysql.com/get/Downloads/MySQL-8.0/mysql-8.0.20-winx64.zip'
\#替换为搜狐镜像 类似还有
\#网易 http://mirrors.163.com/
\#中科大 http://mirrors.ustc.edu.cn/
\#清华 https://mirrors.tuna.tsinghua.edu.cn/
$url = 'http://mirrors.sohu.com/mysql/MySQL-8.0/mysql-8.0.20-winx64.zip'
```

不出意外，`mysql`就安装好了，路径`C:\tools`,甚至相关 windows 服务也装好了。根据之前的经验【One by one 系列】一步步部署.Net core 应用-CentOs 介绍的 mysql 安装后，初始化密码

\

```
# 修改密码
\# 初次安装mysql，root账户没有密码。直接登录
mysql -u root
mysql>show databases;
mysql>set password **for** 'root'@'localhost' =password('设置你的密码');
Query OK, 0 rows affected (0.00 sec)
\#不需要重启数据库即可生效
```

**上面是针对 5.6 以下的版本有效，通过巧克力默认安装的是最新版本，是 8.0 版本,修改密码的操作需要更换如下 sql 语句。**

```
mysql -u root

\#初始化root账户的密码
mysql>ALTER USER root@localhost IDENTIFIED BY '123456';
```



## 7.初始迁移-Initial migration

运用 efcore 迁移功能来创建数据库。migration 是可用于创建和更新数据库以匹配数据模型的一组工具

\

```
#第一步
Add-Migration InitialCreate

\#第二步
Update-Database
```



- `Add-Migration InitialCreate`:生成 `Migrations/{timestamp}_InitialCreate.cs` 迁移文件 。`InitialCreate` 参数是迁移名称。可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。因为这是首次迁移，所以生成的类包含用于创建数据库架构的代码。

- ```
  Update-Database
  ```

  :将数据库更新到

  上一个命令创建的最新迁移

  。此命令运行在 Migrations/{time-stamp}_InitialCreate.cs 文件中 

  

  up 方法，用于创建数据库的。

  - ps：`Up` 方法创建 表。`Down` 方法可还原 `Up` 迁移所做的架构更改。

- 如果是数据库更新，会生成如下警告(可以忽略)：

```
No type was specified **for** the decimal column 'Price' on entity type 'Movie'. **This** will cause values to be silently truncated **if** they **do** not fit **in** the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.
```

更多内容参考：https://docs.microsoft.com/zh-cn/ef/core/miscellaneous/cli/powershell

## 8.依赖注入数据库上下文



## 9

```c#
public class MoviesController : Controller
{
    private readonly MvcMovieContext _context;

    public MoviesController(MvcMovieContext context)
    {
        _context = context;
    }
}
```

.迁移回退

上面 migration 命令，主要依赖`Microsoft.EntityFrameworkCore.Tools`包，如果此包缺失，请安装。

\

```
#先检查下

Get-Help about_EntityFrameworkCore

PM> Get-Help about_EntityFrameworkCore

​                     _/\__

​               ---==/    \\

​         ___  ___   |.    \|\

​        | __|| __|  |  )   \\\

​        | _| | _|   \_/ |  //|\\

​        |___||_|       /   \\\/\\

TOPIC

​    about_EntityFrameworkCore

SHORT DESCRIPTION

​    Provides information about the Entity Framework Core Package Manager Console Tools.

LONG DESCRIPTION

​    **This** topic describes the Entity Framework Core Package Manager Console Tools. See https://docs.efproject.net for

​    information on Entity Framework Core.

​    The following Entity Framework Core commands are available.

​        Cmdlet                      Description

​        --------------------------  ---------------------------------------------------

​        Add-Migration               Adds a **new** migration.

​        Drop-Database               Drops the database.

​        Get-DbContext               Gets information about a DbContext type.

​        Remove-Migration            Removes the last migration.

​        Scaffold-DbContext          Scaffolds a DbContext and entity types **for** a database.

​        Script-DbContext            Generates a SQL script from the current DbContext.

​        Script-Migration            Generates a SQL script from migrations.

​        Update-Database             Updates the database to a specified migration.

SEE ALSO

​    Add-Migration

​    Drop-Database

​    Get-DbContext

​    Remove-Migration

​    Scaffold-DbContext

​    Script-DbContext

​    Script-Migration

​    Update-Database

\#上面是包没问题的象征，如果有问题，再执行如下命令

Install-Package Microsoft.EntityFrameworkCore.Tools
```



To undo this action, use Remove-Migration.

\

```
# POCO类修改=>修改数据表

\# 给本次迁移取一个名字：UpdateEditions，初始化取的名字:InitialCreate

Add-Migration UpdateEditions

\# 更新

Update-Database

\# 突然发现可能有影响，那就回滚吧，删除上一次迁移（回滚针对迁移进行的代码更改）

Remove-Migration
```



Remove-Migration

| 参数   | 说明                                 |
| ------ | ------------------------------------ |
| -Force | 恢复迁移（回滚应用于数据库的更改）。 |



原文链接：https://mp.weixin.qq.com/s/gXXh7Yqy4MTEFf488gad9A



