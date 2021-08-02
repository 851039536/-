# EFCore数据操作

```c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;
using Typecho.Enties.Models;
using Typecho.IService;
using Typecho.Repository;
using Typecho.Service;
using TypechoCore1.Jwt;
using TypechoCore1.Jwt.Filter;
using static TypechoCore1.Jwt.ITokenHelper;

//默认的约定集将应用于程序集中的所有操作：
[assembly: ApiConventionType(typeof(DefaultApiConventions))]
namespace TypechoCore1.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class TestController : Controller
    {
          private readonly typechoContext _coreDbContext;
          private readonly ITokenHelper tokenHelper = null;
         // ITypechoTestService service = new TypechoService();
          private readonly ITypechoTestService service; //IOC依赖注入
      

        public TestController(typechoContext coreDbContext,ITokenHelper _tokenHelper,ITypechoTestService service1)
        {
            _coreDbContext = coreDbContext;
             tokenHelper = _tokenHelper;
            service=service1;
        }
        /// <summary>
        /// 验证Token
        /// </summary>
        /// <param name="tokenStr">token</param>
        /// <returns></returns>
        [HttpGet("ValiToken")]
        public ReturnModel ValiToken(string tokenStr)
        {
            var ret = new ReturnModel
            {
                TnToken = new TnToken()
            };
            bool isvilidate = tokenHelper.ValiToken(tokenStr);
            if(isvilidate)
            {
                ret.Code = 200;
                ret.Msg = "Token验证成功";
                ret.TnToken.TokenStr = tokenStr;
            }
            else
            {
                ret.Code = 500;
                ret.Msg = "Token验证失败";
                ret.TnToken.TokenStr = tokenStr;
            }
            return ret;
        }
        /// <summary>
        /// 验证Token 带返回状态
        /// </summary>
        /// <param name="tokenStr"></param>
        /// <returns></returns>
        [HttpGet("ValiTokenState")]
        public ReturnModel ValiTokenState(string tokenStr)
        {
            var ret = new ReturnModel
            {
                TnToken = new TnToken()
            };
            string loginID = "";
            TokenType tokenType = tokenHelper.ValiTokenState(tokenStr, a => a["iss"] == "WYY" && a["aud"] == "EveryTestOne", action => { loginID = action["loginID"]; });
            if (tokenType == TokenType.Fail)
            {
                ret.Code = 202;
                ret.Msg = "token验证失败";
                return ret;
            }
            if (tokenType == TokenType.Expired)
            {
                ret.Code = 205;
                ret.Msg = "token已经过期";
                return ret;
            }

            //..............其他逻辑
            var data = new List<Dictionary<string, string>>();
            var bb = new Dictionary<string, string>
            {
                { "Wyy", "123456" }
            };
            data.Add(bb);
            ret.Code = 200;
            ret.Msg = "访问成功!";
            ret.Data =data ;
            return ret;
        }

        /// <summary>
        /// 登录测试
        /// </summary>
        /// <param name="user"></param>
        /// <returns></returns>
         [HttpPost("Login")]
        public IActionResult Login([FromBody]LoginInput user)
        {
            var ret = new ReturnModel();
            try
            {
                if (string.IsNullOrWhiteSpace(user.Username) || string.IsNullOrWhiteSpace(user.Password))
                {
                    ret.Code = 201;
                    ret.Msg = "用户名密码不能为空";
                    return NotFound();
                }
                //登录操作 我就没写了 || 假设登录成功
                if (1 == 1)
                {
                    Dictionary<string, string> keyValuePairs = new Dictionary<string, string>
                    {
                        { "loginID", user.Username }
                    };
                    ret.Code = 200;
                    ret.Msg = "登录成功";
                    ret.TnToken= tokenHelper.CreateToken(keyValuePairs);
                }
            }
            catch(Exception ex)
            {
                ret.Code = 500;
                ret.Msg = "登录失败:"+ex.Message;
            }
            return Ok(ret);
        }

        /// <summary>
        /// 异步查询
        /// [ApiExplorerSettings(IgnoreApi = true)] 隐藏接口
        /// </summary>
        /// <returns></returns>
        [ServiceFilter(typeof(TokenFilter))]
        [HttpGet("AsyGetTest")]
        public async Task<IActionResult> AsyGetTest(string token)
        {
          var courses = _coreDbContext.typecho_test;
          return Ok(await courses.ToListAsync());
        }

         /// <summary>
        /// 同步查询
        /// </summary>
        /// <returns></returns>
        [HttpGet("GetTest")]
        public IActionResult GetTest()
        {
          return Ok(service.GetTest());
         }

        /// <summary>
        /// 条件查询
        /// </summary>
        /// <param name = "typecho_test">model</param>
        /// <returns></returns>
        [HttpGet("AsyGetTestName")]
        public async Task<ActionResult<typecho_test>> AsyGetTestName(int id,string name )
        {
            ActionResult<typecho_test> courses;
            try
            {

            
            if (id != 0)
            {
             courses = await Task.Run(()=>_coreDbContext.typecho_test.Single(i=>i.id == id));
             
            }
            else
            {
             courses = await Task.Run(()=>_coreDbContext.typecho_test.Single(i=>i.name == name));
            
            }
            return Ok(courses);

                }
            catch (Exception ex)
            {
                return Ok(ex.Message);
            }
        }

         /// <summary>
        /// 过滤查询
        /// </summary>
        /// <returns></returns>
        [HttpGet("AsyGetTestG")]
        public async Task<IActionResult> AsyGetTestG(string name )
        {
              var courses = _coreDbContext.typecho_test
                  .Where(w => w.name.Contains(name));
            return Ok( await courses.FirstAsync());
        }

        /// <summary>
        /// 模糊查询
        /// </summary>
        /// <returns></returns>
        [HttpGet("AsyGetTestLink")]
        public async Task<IActionResult> AsyGetTestLink(string name )
        {
              var courses = _coreDbContext.typecho_test
                  .Where(w=>EF.Functions.Like(w.name,"%"));
            return Ok( await courses.ToListAsync());
        }


        /// <summary>
        /// 异步删除数据
        /// </summary>
        /// <param name="id"></param>
        /// <returns></returns>
        [HttpDelete("AsyDetTestId")]
        public async Task<IActionResult> AsyDetTestId(int id)
        {
            var todoItem = await _coreDbContext.typecho_test.FindAsync(id);
            if (todoItem == null)
            {
                return NotFound();
            }
            _coreDbContext.typecho_test.Remove(todoItem);
            await _coreDbContext.SaveChangesAsync();
            return NoContent();
        }


        /// <summary>
        /// 同步删除数据
        /// </summary>
        /// <param name="id"></param>
        /// <returns></returns>
        [HttpDelete("DetTestId")]
        public  IActionResult DetTestId(int id)
        {
            var todoItem = _coreDbContext.typecho_test.Find(id);
            if (todoItem == null)
            {
                return NotFound();
            }
            _coreDbContext.typecho_test.Remove(todoItem);
            _coreDbContext.SaveChanges();
            return NoContent();
        }

        
        /// <summary>
        /// 异步添加数据
        /// </summary>
        /// <returns></returns>
        [HttpPost("AsyIntTest")]
        public async Task<ActionResult<typecho_test>> AsyIntTest(typecho_test test)
        {
            _coreDbContext.typecho_test.Add(test);
            return Ok(await _coreDbContext.SaveChangesAsync());
        }
          
        /// <summary>
        /// 同步添加数据
        /// </summary>
        /// <returns></returns>
        [HttpPost("IntTest")]
        public  ActionResult<typecho_test> IntTest(typecho_test test)
        {
            _coreDbContext.typecho_test.Add(test);
            return Ok(_coreDbContext.SaveChanges());
        }

        /// <summary>
        /// 异步更新数据
        /// </summary>
        /// <param name="id"></param>
        /// <param name="test"></param>
        /// <returns></returns>
        [HttpPut("AysUpTest")]
        public async Task<IActionResult> AysUpTest(int id, typecho_test test)
        {
            if (id != test.id)
            {
                return BadRequest();
            }
            _coreDbContext.Entry(test).State = EntityState.Modified;
            await _coreDbContext.SaveChangesAsync();
            return NoContent();
        }

        /// <summary>
        /// 同步更新数据
        /// </summary>
        /// <param name="id"></param>
        /// <param name="test"></param>
        /// <returns></returns>
        [HttpPut("UpTest")]
        public  IActionResult UpTest(int id, typecho_test test)
        {
            if (id != test.id)
            {
                return BadRequest();
            }
            _coreDbContext.Entry(test).State = EntityState.Modified;
            _coreDbContext.SaveChanges();
            return NoContent();
        }
    }
}
```

