# lambda表达式

### 参考

[[C# lambda表达式（简单易懂）](https://www.cnblogs.com/VikingsTHT/p/5072185.html)]:

[[C# Lambda表达式学习笔记](https://www.cnblogs.com/atomy/p/12080368.html)]



### lambda简介

   lambda运算符：所有的lambda表达式都是用新的lambda运算符 " `=>` ",可以叫他，“`转到`”或者 “`成为`”。运算符将表达式分为两部分，左边指定输入参数，右边是lambda的主体。lambda 表达式是一个可用于创建委托或表达式树类型的匿名函数

​    lambda表达式：

​        1.一个参数：`param=>expr`

​        2.多个参数：`(x, y) => x == y`

​        3.显式指定类型：`(int x, string s) => s.Length > x`

​        4.使用空括号指定零个输入参数：`() => SomeMethod()`

**示例1**

> 例如，lambda 表达式 x => x * x 指定名为  x 的参数并返回 x 的值计算的。 如下面的示例所示，可以将此表达式传递给委托类型：

```c#
delegate int del(int i);
static void Main(string[] args)
{
    del myDelegate = x => x * x;
    int j = myDelegate(5); //j = 25
}      
```

**示例2 多参数**

```c#
 //委托  逛超市
        delegate int GuangChaoshi(int a,int b);
        static void Main(string[] args)
        {            
            GuangChaoshi gwl = (p,z) => z-(p + 10);
            Console.WriteLine(gwl(10,100) + "");   //打印80，z对应参数b，p对应参数a
            Console.ReadKey();
        }
```

**示例2.1 lambda主体运算复杂**

```c#
/// <summary>
        /// 委托  逛超市
        /// </summary>
        /// <param name="a">花费</param>
        /// <param name="b">付钱</param>
        /// <returns>找零</returns>
        delegate int GuangChaoshi(int a,int b);
        static void Main(string[] args)
        {
            GuangChaoshi gwl = (p, z) =>
            {
                int zuidixiaofei = 10;
                if (p < zuidixiaofei)
                {
                    return 100;
                }
                else
                {
                    return z - p - 10;
                }
           
            };
            Console.WriteLine(gwl(10,100) + "");   //打印80，z对应参数b，p对应参数a
            Console.ReadKey();
        }
```

### Func<T>委托 使用表达式



**示例1**

```c#
 Func<int, string> gwl = p => p + 10 + "--返回类型为string";            
            Console.WriteLine(gwl(10) + "");   //打印‘20--返回类型为string’，z对应参数b，p对应参数a
            Console.ReadKey();
```



**示例2**

```c#
 static void Main(string[] args)
        {
            Func<int, int, bool> gwl = (p, j) =>
                {
                    if (p + j == 10)
                    {
                        return true;
                    }
                    return false;
                };
            Console.WriteLine(gwl(5,5) + "");   //打印‘True’，z对应参数b，p对应参数a
            Console.ReadKey();
        }
```

> 说明：从这个例子，我们能看到，p为int类型，j为int类型，返回值为bool类型。



**示例3**

> 将委托实例化为 Func<int,bool> myFunc，其中 int 是输入参数，bool  是返回值。 始终在最后一个类型参数中指定返回值。 **Func<int, string,  bool>** 定义包含两个输入参数（int 和  string）且返回类型为 bool 的委托。 在调用下面的 **Func** 委托时，该委托将返回 true 或 false 以指示输入参数是否等于  5：

```
Func<int, bool> myFunc = x => x == 5;
bool result = myFunc(4); // returns false of course
```

示例4

> 当参数类型为 **Expression<Func>** 时，您也可以提供 Lambda 表达式，例如在  System.Linq.Queryable 内定义的标准查询运算符中。 如果指定 **Expression<Func>** 参数，Lambda  将编译为表达式树。
>
> 此处显示了一个标准查询运算符， [Count](ms-xhelp:///?method=page&id=overload%3asystem.linq.enumerable.count&vendor=Microsoft&topicVersion=110&topicLocale=ZH-CN)  方法：

```c#
int[] numbers = { 5, 4, 1, 3, 9, 8, 6, 7, 2, 0 };
int oddNumbers = numbers.Count(n => n % 2 == 1);
```



### lambda表达式树



**示例1**

```c#
 /// <summary>
        /// 表达树
        /// </summary>
        public void ExpressioTest()
        {  
            //创建一个表达式树中的参数，作为一个节点
            Expression f = Expression.Constant(2);
            Expression s = Expression.Constant(3);
            Expression test = Expression.Add(f, s);
            Console.WriteLine(test);
        }

```

**示例2**

> 演示如何让 Visual C#  编译器创建一个表示  lambda 表达式 num => num < 5 (C#) 或 Function(num) num < 5 (Visual Basic) 的表达式树。

```c#
Expression<Func<int, bool>> lambda = num => num < 5;
```



**示例3** 编译表达式

> [Expression<  TDelegate>  ](ms-xhelp:///?method=page&id=t%3asystem.linq.expressions.expression`1&vendor=Microsoft&topicVersion=110&topicLocale=ZH-CN) 类型提供 [Compile](ms-xhelp:///?method=page&id=overload%3asystem.linq.expressions.expression`1.compile&vendor=Microsoft&topicVersion=110&topicLocale=ZH-CN)  方法，该方法将表达式树表示的代码编译成一个可执行委托。

```c#
            // 创建表达式树
            Expression<Func<int, bool>> expr = num => num < 5;
            // 将表达式树编译为委托。
            Func<int, bool> result = expr.Compile();
            // 调用委托并将结果写入控制台。
            Console.WriteLine(result(4));
            // Prints True.

            //还可以使用简化的语法
            //编译并运行表达式树。.
            //下面的行可以替换前面的两个语句。
            Console.WriteLine(expr.Compile()(4));
            Console.ReadKey();
            // Also prints True.
```

