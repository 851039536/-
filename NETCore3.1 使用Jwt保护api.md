# NETCore3.1 使用Jwt保护api

### 摘要

本文演示如何向有效用户提供jwt，以及如何在webapi中使用该token通过JwtBearerMiddleware中间件对用户进行身份认证。

### 认证和授权区别？

首先我们要弄清楚认证（Authentication）和授权（Authorization）的区别，以免混淆了。认证是确认的过程中你是谁，而授权围绕是你被允许做什么，即权限。显然，在确认允许用户做什么之前，你需要知道他们是谁，因此，在需要授权时，还必须以某种方式对用户进行身份验证。 

### 什么是JWT？

根据维基百科的定义，JSON WEB Token（JWT），是一种基于JSON的、用于在网络上声明某种主张的令牌（token）。JWT通常由三部分组成：头信息（header），消息体（payload）和签名（signature）。

头信息指定了该JWT使用的签名算法:

```c#
header = '{"alg":"HS256","typ":"JWT"}'
```

`HS256`表示使用了HMAC-SHA256来生成签名。

消息体包含了JWT的意图：

```c#
payload = '{"loggedInAs":"admin","iat":1422779638}'//iat表示令牌生成的时间
```

未签名的令牌由`base64url`编码的头信息和消息体拼接而成（使用"."分隔），签名则通过私有的key计算而成：

```c#
key = 'secretkey'  
unsignedToken = encodeBase64(header) + '.' + encodeBase64(payload)  
signature = HMAC-SHA256(key, unsignedToken)
```

最后在未签名的令牌尾部拼接上`base64url`编码的签名（同样使用"."分隔）就是JWT了：

```c#
token = encodeBase64(header) + '.' + encodeBase64(payload) + '.' + encodeBase64(signature)

# token看起来像这样: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJsb2dnZWRJbkFzIjoiYWRtaW4iLCJpYXQiOjE0MjI3Nzk2Mzh9.gzSraSYS8EXBxLN_oWnFSRgCzcmJmMjLiuyu5CSpyHI
```



JWT常常被用作保护服务端的资源（resource），客户端通常将JWT通过HTTP的`Authorization` header发送给服务端，服务端使用自己保存的key计算、验证签名以判断该JWT是否可信：

```c#
Authorization: Bearer eyJhbGci*...<snip>...*yu5CSpyHI
```

### 准备工作

使用vs2019创建webapi项目，并且安装nuget包

```c#
Microsoft.AspNetCore.Authentication.JwtBearer
```

### Startup类

ConfigureServices 添加认证服务

```c#
services.AddAuthentication(options =>
            {
                options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                options.DefaultScheme = JwtBearerDefaults.AuthenticationScheme;
            }).AddJwtBearer(options =>
            {
                options.SaveToken = true;
                options.RequireHttpsMetadata = false;
                options.TokenValidationParameters = new TokenValidationParameters()
                {
                    ValidateIssuer = true,
                    ValidateAudience = true,
                    ValidAudience = "https://xx",
                    ValidIssuer = "https://xx",
                    IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("SecureKeySecureKeySecureKeySecureKeySecureKeySecureKey"))
                };
            });
```

Configure 配置认证中间件

`app.UseAuthentication();//认证中间件`

### 创建一个token

添加一个登录model命名为LoginInput

```c#
public class LoginInput
    {
        public string Username { get; set; }
        public string Password { get; set; }
    }
```

添加一个认证控制器命名为AuthenticateController

```c#
[Route("api/[controller]")]
    public class AuthenticateController : Controller
    {
        [HttpPost]
        [Route("login")]
        public IActionResult Login([FromBody]LoginInput input)
        {
            //从数据库验证用户名，密码 
            //验证通过 否则 返回Unauthorized

            //创建claim
            var authClaims = new[] {
                new Claim(JwtRegisteredClaimNames.Sub,input.Username),
                new Claim(JwtRegisteredClaimNames.Jti,Guid.NewGuid().ToString())
            };
            IdentityModelEventSource.ShowPII = true;
            //签名秘钥 可以放到json文件中
            var authSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("SecureKeySecureKeySecureKeySecureKeySecureKeySecureKey"));

            var token = new JwtSecurityToken(
                   issuer: "https://xx",
                   audience: "https://xx",
                   expires: DateTime.Now.AddHours(2),
                   claims: authClaims,
                   signingCredentials: new SigningCredentials(authSigningKey, SecurityAlgorithms.HmacSha256)
                   );

            //返回token和过期时间
            return Ok(new
            {
                token = new JwtSecurityTokenHandler().WriteToken(token),
                expiration = token.ValidTo
            });
        }
    }
```

### 添加api资源

**利用默认的控制器****WeatherForecastController**

- - 添加个Authorize标签
  - 路由调整为：[Route("api/[controller]")] 代码如下

```c#
    [Authorize]
    [ApiController]
    [Route("api/[controller]")]
    public class WeatherForecastController : ControllerBase
```

到此所有的代码都已经准好了，下面进行运行测试

### 运行项目

**使用postman进行模拟**

1. 发现返回时401未认证，下面获取token
2. 通过用户和密码获取token
3. 如果我们的凭证正确，将会返回一个token和过期日期，然后利用该令牌进行访问
4. 利用token进行请求
5. 现请求状态200！

原文：[https://www.cnblogs.com/chengtian/p/11927663.html]()

