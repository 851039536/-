# c#方法

> 一个方法是把一些相关的语句组织在一起，用来执行一个任务的语句块。
>



## 方法签名

通过指定在 `class` 或 `struct` 中声明方法：

- 可选的访问级别，如 `public` 或 `private`。 默认值为 `private`。
- 可选的修饰符，如 `abstract` 或 `sealed`。
- 返回值，或 `void`（如果该方法不具有）。
- 方法名。
- 任何方法参数。 方法参数在括号内，并且用逗号分隔。 空括号指示方法不需要任何参数。

## C# 中定义方法

当定义一个方法时，从根本上说是在声明它的结构的元素。在 C# 中，定义方法的语法如下：

```c#
<Access Specifier> <Return Type> <Method Name>(Parameter List)
{
   Method Body
}
```

方法的各个元素：

- **Access Specifier**：访问修饰符，这个决定了变量或方法对于另一个类的可见性。
- **Return type**：返回类型，一个方法可以返回一个值。返回类型是方法返回的值的数据类型。如果方法不返回任何值，则返回类型为 **void**。
- **Method name**：方法名称，是一个唯一的标识符，且是大小写敏感的。它不能与类中声明的其他标识符相同。
- **Parameter list**：参数列表，使用圆括号括起来，该参数是用来传递和接收方法的数据。参数列表是指方法的参数类型、顺序和数量。参数是可选的，也就是说，一个方法可能不包含参数。
- **Method body**：方法主体，包含了完成任务所需的指令集。

 下面的类包含三个方法：

```c#
abstract class Motorcycle
{
    public void StartEngine() { }
    protected void AddGas(int gallons) {  }
    public virtual int Drive(int miles, int speed) {  return 1; }
    public abstract double GetTopSpeed(); 
}
```

## 方法访问(调用)

在对象上调用方法类似于访问字段。 在对象名称之后，依次添加句点、方法名称和括号。 参数在括号内列出，并用逗号隔开。 因此，可以按以下示例中的方式调用 Motorcycle  类的方法：

```c#
class TestMotorcycle : Motorcycle
{
    public override double GetTopSpeed()
    {
        return 108.4;
    }
    static void Main()
    {
        TestMotorcycle moto = new TestMotorcycle();
        moto.StartEngine();
        moto.AddGas(15);
        moto.Drive(5, 20);
        double speed = moto.GetTopSpeed();
        Console.WriteLine("My top speed is {0}", speed);            
    }
}
```

## 参数传递

当调用带有参数的方法时，您需要向方法传递参数。在 C# 中，有三种向方法传递参数的方式：

| 方式     | 描述                                                         |
| :------- | :----------------------------------------------------------- |
| 值参数   | 这种方式复制参数的实际值给函数的形式参数，实参和形参使用的是两个不同内存中的值。在这种情况下，当形参的值发生改变时，不会影响实参的值，从而保证了实参数据的安全。 |
| 引用参数 | 这种方式复制参数的内存位置的引用给形式参数。这意味着，当形参的值发生改变时，同时也改变实参的值。 |
| 输出参数 | 这种方式可以返回多个值。                                     |

## 返回值

> 方法可以向调用方返回值。 如果返回类型（方法名称前列出的类型）不是 void，则方法可以使用 return 关键字来返回值。 如果语句中  return  关键字的后面是与返回类型匹配的值，则该语句将该值返回给方法调用方。 return 关键字还会停止方法的执行。 如果返回类型为 void，则可使用没有值的  return 语句来停止方法的执行。 如果没有 return  关键字，方法执行到代码块末尾时即会停止。 具有非 void 返回类型的方法才能使用  return 关键字返回值。 



## 异步方法

通过使用异步功能，你可以调用异步方法而无需使用显式回调，也不需要跨多个方法或 lambda 表达式来手动拆分代码。



异步方法可以具有 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task-1)、[Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task)、 或 `void` 返回类型。 `void` 返回类型主要用于定义需要 `void` 返回类型的事件处理程序。 无法等待返回 `void` 的异步方法，并且返回 void 方法的调用方无法捕获该方法引发的异常。 从 C# 7.0 开始，异步方法可以有[任何类似任务的返回类型](https://docs.microsoft.com/zh-cn/dotnet/csharp/whats-new/csharp-7#generalized-async-return-types)。

在下面的示例中，`DelayAsync` 是一个异步方法，包含返回整数的 return 语句。 由于它是异步方法，其方法声明必须具有返回类型 `Task`。 因为返回类型是 `Task`，`await` 中 `DoSomethingAsync` 表达式的计算将如以下 `int result = await delayTask` 语句所示得出整数。



```c#
using System;
using System.Diagnostics;
using System.Threading.Tasks;

public class Example
{
    // This Click event is marked with the async modifier.
    public static void Main()
    {
       DoSomethingAsync().Wait();
    }
    private static async Task DoSomethingAsync()
    {
        int result = await DelayAsync();
        Console.WriteLine("Result: " + result);
    }
    private static async Task<int> DelayAsync()
    {
        await Task.Delay(100);
        return 5;
    }
    // Output:
    //  Result: 5
}
// The example displays the following output:
//        Result: 5
```

异步方法不能声明任何 [in](https://docs.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/in-parameter-modifier)、[ref](https://docs.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/ref) 或 [out](https://docs.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/out-parameter-modifier) 参数，但是可以调用具有这类参数的方法。