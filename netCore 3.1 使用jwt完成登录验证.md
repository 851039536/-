# netCore 3.1 使用jwt完成登录验证

### nuget安装

Microsoft.AspNetCore.Authentication.JwtBearer 3.1.0 版本

### 配置appsettings.json

```c#
  "Authentication": { //jwt
    "JwtBearer": {
      "IsEnabled": "true",
      "SecurityKey": "Demo_C421AAEE0D114E9C1",
      "Issuer": "Demo",
      "Audience": "Demo",
      "Expiration": 2 //token过期时间 （单位：分钟）
    }
  }
```

### 添加扩展类和jwt配置类

#### AuthConfigure.cs

```c#
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.IdentityModel.Tokens;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Snblog.Jwt
{
   public static class AuthConfigure
    {
        public static void ConfigureJwt(this IServiceCollection services, IConfiguration configuration)
        {
            if (bool.Parse(configuration["Authentication:JwtBearer:IsEnabled"]))
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = "JwtBearer";
                    options.DefaultChallengeScheme = "JwtBearer";
                }).AddJwtBearer("JwtBearer", options =>
                {
                    options.Audience = configuration["Authentication:JwtBearer:Audience"];
 
                    options.TokenValidationParameters = new TokenValidationParameters
                    {
                        // The signing key must match!
                        ValidateIssuerSigningKey = true,
                        IssuerSigningKey = new SymmetricSecurityKey(Encoding.ASCII.GetBytes(configuration["Authentication:JwtBearer:SecurityKey"])),
 
                        // Validate the JWT Issuer (iss) claim
                        ValidateIssuer = true,
                        ValidIssuer = configuration["Authentication:JwtBearer:Issuer"],
 
                        // Validate the JWT Audience (aud) claim
                        ValidateAudience = true,
                        ValidAudience = configuration["Authentication:JwtBearer:Audience"],
 
                        // Validate the token expiry
                        ValidateLifetime = true,
 
                        // If you want to allow a certain amount of clock drift, set that here
                        ClockSkew = TimeSpan.Zero
                    };
                });
            }
        }
    }
}
```

#### JwtConfig.cs

```c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace Snblog.Jwt
{
    public class JwtConfig
    {
        /// <summary>
        /// 密钥
        /// </summary>
        public string SecurityKey { get; set; }
        /// <summary>
        /// 所属者
        /// </summary>
        public string Issuer { get; set; }
 
        public string Audience { get; set; }
 
        /// <summary>
        /// 过期时间
        /// </summary>
        public int Expiration { get; set; }
    }
}

```

### **配置Startup**

```c#
public void ConfigureServices(IServiceCollection services)
{
    //配置jwt
    services.ConfigureJwt(Configuration);
    //注入JWT配置文件
    services.Configure<JwtConfig>(Configuration.GetSection("Authentication:JwtBearer"));
    services.AddControllers();
}
 
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    app.UseHttpsRedirection();
    app.UseRouting();
    app.UseAuthentication(); //一定要在这个位置（app.UseAuthorization()上面）
    app.UseAuthorization();
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllerRoute(
        name: "default",
        pattern: "{controller=Home}/{action=Index}/{id?}");
    });
}
```

### 接口上加特性：[Authorize]

如

```c#

    [Authorize]
    [Route("api/[controller]")]
    [ApiController]

    public class SnArticleController : ControllerBase
    {
```

### 登录配置

```c#
        private readonly JwtConfig jwtModel = null;


        public SnUserController(ISnUserService service, snblogContext coreDbContext, IOptions<JwtConfig> _jwtModel)
        {
            _service = service;
            _coreDbContext = coreDbContext;
            user = coreDbContext.SnUser;
            jwtModel = _jwtModel.Value;
        }

        [HttpGet("Login")]
        public IActionResult Login(string users, string pwd)
        {
            if (string.IsNullOrEmpty(users) && string.IsNullOrEmpty(pwd))
            {
                return Ok("用户密码不能为空");
            }
            var data = from u in user
                       where u.UserName == users && u.UserPwd == pwd
                       select u.UserName;
            if (data.Count() == 0)
            {
                return Ok("登录失败");
            }
            else
            {
                //return Ok("登录成功");


            var claims = new List<Claim>();
            claims.AddRange(new[]
            {
                new Claim("UserName", "111"),
                new Claim(JwtRegisteredClaimNames.Sub,"111"),
                new Claim(JwtRegisteredClaimNames.Jti, Guid.NewGuid().ToString()),
                new Claim(JwtRegisteredClaimNames.Iat, DateTimeOffset.Now.ToUnixTimeSeconds().ToString(), ClaimValueTypes.Integer64)
            });
 
            DateTime now = DateTime.UtcNow;
            var jwtSecurityToken = new JwtSecurityToken(
                issuer: jwtModel.Issuer,
                audience: jwtModel.Audience,
                claims: claims,
                notBefore: now,
                expires: now.Add(TimeSpan.FromMinutes(jwtModel.Expiration)),
                signingCredentials: new SigningCredentials(new SymmetricSecurityKey(Encoding.ASCII.GetBytes(jwtModel.SecurityKey)), SecurityAlgorithms.HmacSha256)
            );
            
            string token = new JwtSecurityTokenHandler().WriteToken(jwtSecurityToken);
 
            return  Ok(token);

            }

        }
```

