# netcore 笔记

### .net core 执行过程

浏览器 - iis|Apache|Nginx - net core -自带web应用kestrel - 中间件Configure http管道 - api 

### RESTful风格的编程

Controllers  四个方法，并且每个方法也有各自的特性，分别是HttpGet,HttpPost,HttpPut,HttpDelete

提醒：2.1以后，新建的controller 所继承的基类的 **ControllerBase**，导致在接口的返回值中，不能使用 return Json();方法，你可以使用 return Ok(xxx)，效果是一样的，

如果你一定要使用 Json，那就修改基类，继承 Controller 吧。



### [HttpGet("{id}"),Name="Get"] ，这个有时候会带 Name

```c#
[HttpGet("{id}", Name = "GetTodo")]
public IActionResult GetById(long id)
```

`"{id}"` 是 `todo` 项 的 ID 的占位符变量。 调用 `GetById` 时，它会将 URL 中“{id}”的值分配给方法的 `id` 参数。`Name = "GetTodo"` 创建一个命名的路由，使你能够 HTTP 响应中链接到此路由



### netcore 进程托管

进程内：iis模式

进程外：kestrel 



### JWT 白皮书

> 维基百科定义，JWT（读作 [/dʒɒt/]），即JSON Web Tokens，是一种基于JSON的、用于在网络上声明某种主张的令牌（token）。JWT通常由三部分组成: 头信息（header）, 消息体（payload）和签名（signature）。它是一种用于双方之间传递安全信息的表述性声明规范。JWT作为一个开放的标准（RFC 7519），定义了一种简洁的、自包含的方法，从而使通信双方实现以JSON对象的形式安全的传递信息。

**api保护：**

- 设计原则
- 加密算法
- 保护方式

**为什么保护：**

- 防泄漏
- 防攻击（防伪装攻击  防篡改攻击 防重放攻击）
- 收益化



