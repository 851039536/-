# C#访问修饰符

在 C#语言中类定义后就要确定类中包含的内容，类中包含的内容被称为类中的成员。

类中的成员包括字段、属性、方法。每个类成员在定义时需要指定访问修饰符、修饰符。

可使用访问修饰符指定以下六个可访问性级别：

- [`public`](https://docs.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/public)：访问不受限制。
- [`protected`](https://docs.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/protected)：访问限于包含类或派生自包含类的类型。
- [`internal`](https://docs.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/internal)：访问限于当前程序集。
- [`protected internal`](https://docs.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/protected-internal)：访问限于当前程序集或派生自包含类的类型。
- [`private`](https://docs.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/private)：访问限于包含类型。
- [`private protected`](https://docs.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/private-protected)：访问限于包含类或派生自当前程序集中包含类的类型。