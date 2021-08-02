#  Web API 约定

## 默认的约定

集将应用于 ContactsConventionController 中的所有操作：

```c#
[ApiController]
[ApiConventionType(typeof(DefaultApiConventions))]
[Route("api/[controller]")]
public class ContactsConventionController : ControllerBase
{
```

默认的约定集将应用于程序集中的所有操作：

```c#
[assembly: ApiConventionType(typeof(DefaultApiConventions))]
namespace ApiConventions
{
    public class Startup
    {
```

## 创建 Web API 约定

如果默认 API 约定不能满足需要，请创建自己的约定。 约定是：

- 带有方法的静态类型。
- 能够对操作定义[响应类型](https://docs.microsoft.com/zh-cn/aspnet/core/web-api/advanced/conventions?view=aspnetcore-3.1#response-types)和[命名要求](https://docs.microsoft.com/zh-cn/aspnet/core/web-api/advanced/conventions?view=aspnetcore-3.1#naming-requirements)。

### 响应类型

这些方法使用 `[ProducesResponseType]` 或 `[ProducesDefaultResponseType]` 属性进行批注。 例如：

C#复制

```csharp
public static class MyAppConventions
{
    [ProducesResponseType(StatusCodes.Status200OK)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    public static void Find(int id)
    {
    }
}
```

如果没有更具体的元数据属性，则将此约定应用于程序集可强制实现以下内容：

- 该约定方法应用于所有名为 `Find` 的操作。
- `id` 操作上存在名为 `Find` 的参数。

### 命名要求

`[ApiConventionNameMatch]` 和 `[ApiConventionTypeMatch]` 属性可应用于约定方法，确定它们所要应用的操作。 例如：

C#复制

```csharp
[ProducesResponseType(StatusCodes.Status200OK)]
[ProducesResponseType(StatusCodes.Status404NotFound)]
[ApiConventionNameMatch(ApiConventionNameMatchBehavior.Prefix)]
public static void Find(
    [ApiConventionNameMatch(ApiConventionNameMatchBehavior.Suffix)]
    int id)
{ }
```

在上面的示例中：

- 应用于该方法的 `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` 选项表示该约定可匹配前缀是“Find”的任何操作。 匹配的操作可以是 `Find`、`FindPet` 和 `FindById`。
- 应用于该参数的 `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` 表示该约定可匹配带有唯一以标识符作为后缀结尾的参数的方法。 示例包括 `id` 或 `petId` 等参数。 与此类似，可将 `ApiConventionTypeMatch` 应用于类型，以约束参数类型。 `params[]` 参数指示无需显式匹配的剩余参数。