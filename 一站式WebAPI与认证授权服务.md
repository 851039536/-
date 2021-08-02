#  一站式WebAPI与认证授权服务

保护WEBAPI有哪些方法？

微软官方文档推荐了好几个：

- Azure Active Directory
- Azure Active Directory B2C (Azure AD B2C)]
- IdentityServer4

前面两个看着就觉得搞不太明白，第三个倒是非常常见，相关的文章也很多。不过这个东西是独立部署的，太重了，如果我就想写一个简单一点的API，把认证给包括的，是不是有好办法？

# 准备

假设你的WEBAPI使用JWT TOKEN来保存你的认证信息，并且通过JWT TOKEN进行保护。那么我们可以设计一个集成有认证授权的WEBAPI服务，一站式解决问题，代码简单且方便自行修改。

要点：

1. 使用类似[Authorize]的授权，需要基于token中`role`这个Claim来实现。
2. 密码的保存需要进行特别设计。
3. 用户对象返回需要避免password和passwordhash的传递。

项目特点：

1. RESTful设计（正常来说api的资源应该是复数userinfos，但是info应该就是不可数的，不纠结了。）
2. 集成Swagger
3. ASP.NET Core 3.1
4. nullable设计
5. EF Core
6. 用户权限控制
7. 密码安全存储
8. Token实现与API集成
9. 简单易于理解

# 用户实体类

所有认证之类的工作都在API这边实现，因此我们需要一个userinfo类来进行处理。

```c#
Copy[DataContract]
[Table("userinfo")]
public class UserInfo
{
    [DataMember]
    [Key]
    public string UserId { get; set; } = default!;
    //传输的过程中会用到密码，但是这个密码不应该被存入数据库中。
    [NotMapped]
    [DataMember]
    public string? Password { get; set; }
    //传输的过程中不会用到密码哈希值，但是哈希值需要存入数据库中。
    [IgnoreDataMember]
    public string? PasswordHash { get; set; }
    [DataMember]
    public string? Role { get; set; }

    public static string GetRole(string? role)
    {
        if (string.IsNullOrWhiteSpace(role)) return "User";
        return role.ToLower() switch
        {
            "administrator" => "Administrator",
            "supervisor" => "Supervisor",
            _ => "User"
        };
    }
}
```

> - 使用json进行序列化，[DataContract]不是必须的，我一般是不喜欢写这个东西，不写的话，那么所有的public属性和字段都会被序列化；如果标记了[DataContract]，那么只有标记有[DataMember]的会被序列化，使用[IgnoreDataMember]可以阻止序列化。
> - 使用了EF Core用来持久化，标记[NotMapped]指示属性不被映射到数据库中，一般来说，数据库不应该直接保存密码。

# 令牌发放

具体实现TokenController如下。

```c#
Copy[AllowAnonymous]
[HttpPost]
public ActionResult Post(UserInfo login)
{
    ActionResult response = BadRequest("登录失败，请检查用户名和密码");
    var user = AuthenticateUser(login);

    if (user != null)
    {
        var tokenString = GenerateJSONWebToken(user);
        response = Ok(new { access_token = tokenString, role = user.Role });
    }

    return response;
}

private string GenerateJSONWebToken(UserInfo userInfo)
{
    var securityKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(_config["Jwt:Key"]));
    var credentials = new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256);

    var claims = new[] {
        new Claim(JwtRegisteredClaimNames.Sub, userInfo.UserName),
        new Claim(JwtRegisteredClaimNames.Jti, Guid.NewGuid().ToString()),
        new Claim(ClaimTypes.Role, userInfo.Role),
    };

    var token = new JwtSecurityToken(null,
        null,
        claims,
        expires: DateTime.Now.AddMinutes(120),
        signingCredentials: credentials);

    return new JwtSecurityTokenHandler().WriteToken(token);
}

private UserInfo? AuthenticateUser(UserInfo login)
{
    UserInfo? user = null;
    if (string.IsNullOrWhiteSpace(login.Password)) return user;

    using (var context = new ManageDataContext())
    {
        var result = context.UserInfos.Where(w => w.UserName.ToLower() == login.UserName.ToLower()).FirstOrDefault();
        if (result != null)
            if (PasswordStorage.VerifyPassword(login.Password, result.PasswordHash!)) user = result;
    }

    return user;
}
```

上面的类标志有`AllowAnonymous`，表示这个类是可以匿名访问的，用户先请求post请求token，然后再携带token访问其他API。

> 上面用到一个PasswordStorage的库，这个库使用了加盐哈希的形式存储了密码，实践上比较可靠。值得一提的是它的`VerifyPassword()`函数，使用的比较算法很巧妙，我贴在了文末，推荐大家阅读。

# 受保护的API

被保护的用户管理API如下，只贴了一小部分：

```c#
Copy[EnableCors("AllowAll")]
[Route("api/[controller]")]
//只有角色为Admin可以访问
[Authorize(Roles = "Admin")]
//如果需要增加种子数据，可以注释上面这行，取消注释下面这一行
//[AllowAnonymous]
[ApiController]
public class UserInfoController : ControllerBase
{
    private readonly ManageDataContext _context;
    public UserInfoController(ManageDataContext context)
    {
        _context = context;
    }

    /// <summary>
    /// 有参GET请求
    /// </summary>
    /// <param name="id">用户编号id</param>
    /// <returns></returns>
    [HttpGet("{id}")]
    [ProducesResponseType(typeof(UserInfo), Status200OK)]
    [ProducesResponseType(typeof(string), Status404NotFound)]
    public async Task<ActionResult> Get(string id)
    {
        var res = await _context.UserInfos.FindAsync(id);
        if (res != null) return Ok(res);
        else return NotFound("Cannot find key.");
    }
}
```

# 启动配置

Startup.cs注意一下顺序的问题。

```c#
Copypublic void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    //实际测试，这个UseCors如果在UseAuthentication和UseAuthorization的后面，可能会导致vue.js访问问题。
    app.UseCors("AllowAll");

    app.UseAuthentication();
    app.UseAuthorization();
}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_3_0);
    //使用AddNewtonsoftJson为了避免json的严格检查。
    services.AddControllers().AddNewtonsoftJson();
    services.AddDbContext<ManageDataContext>();
    //后面还有不贴了
}
```

在ConfigureServices里面，调用了`AddNewtonsoftJson()`。之所以没有使用到默认的`System.Text.Json`，是因为它对客户端上传的信息要求太严格，如果是integer类型的值，上传使用了string就不能正确识别对象，而`Newtonsoft.Json`没有这个问题。

> 也可以修改`System.Text.Json`的默认行为，但是总是没有那么方便了。

# 调用方法

## 请求令牌[#](https://www.cnblogs.com/podolski/p/12737463.html#1871811899)

POST请求，api/token，设置header：Content-Type为application/json。body内容如下：

```json
Copy{
  "userName": "admin",
  "password": "123"
}
```

调用即可返回access_token与role。

## 调用被保护的API[#](https://www.cnblogs.com/podolski/p/12737463.html#1662401913)

需要设置header：

- Authorization值为Bearer [获取到的token]
- Content-Type为application/json
  然后就可以自由调用自己有权访问的API了。

# 总结

零零散散写了这么些，直接贴上代码，项目是基于asp.net core 3.1与swagger的，本项目也可以作为一些小型项目的模板。

> 需要新建用户的话，可以注释掉[Authorize]或者我已经准备了一个用户admin，密码是123。
> 如果需要在windows上进行服务部署，可以参考我之前写的TopShelf的[文章](https://www.cnblogs.com/podolski/p/10054286.html)。

Github[项目地址](https://github.com/circler3/DemoWebAPI)，欢迎Fork或者Star。

## 展望[#](https://www.cnblogs.com/podolski/p/12737463.html#124683002)

1. token刷新与吊销。
2. 注册与手机/Email验证。

## 参考资料[#](https://www.cnblogs.com/podolski/p/12737463.html#2328240139)

1. [密码哈希指南](https://www.freebuf.com/articles/web/28527.html)
2. [加盐哈希指南](https://crackstation.net/hashing-security.htm#ineffective)
3. [password-hashing](https://github.com/defuse/password-hashing)

作者： 波多尔斯基

出处：https://www.cnblogs.com/podolski/p/12737463.html

