### c# 类

> 当你定义一个类时，你定义了一个数据类型的蓝图。这实际上并没有定义任何的数据，但它定义了类的名称意味着什么，也就是说，类的对象由什么组成及在这个对象上可执行什么操作。对象是类的实例。构成类的方法和变量成为类的成员。

### 声明类

```c#
public class Customer
{
    
}
```

------

### 创建对象

通过使用 **new** 关键字（后跟对象将基于的类的名称）可以创建对象。

```c#
Customer object1 = new Customer();
```

### 类继承

继承是通过使用“派生”来实现的，而派生意味着类是使用“基类”声明的，它的数据和行为从基类继承。  通过在派生的类名后面追加冒号和基类名称，可以指定基类

```c#
public class Manager : Employee
{
   
}
```

**注意** 当类声明基类时，它继承基类除构造函数以外的所有成员。



### 构造函数

类的 **构造函数** 是类的一个特殊的成员函数，当创建类的新对象时执行。

构造函数的名称与类的名称完全相同，它没有任何返回类型。 

```c#
public Line()
   {
     Console.WriteLine("对象已创建");
   }
```

**默认的构造函数**没有任何参数。但是如果你需要一个带有参数的构造函数可以有参数，这种构造函数叫做**参数化构造函数**。

```c#
 public Line(double len) // 参数化构造函数*
   {
     Console.WriteLine("对象已创建，length = {0}", len);
     length = len;
   }
```

### 析构函数

类的 **析构函数** 是类的一个特殊的成员函数，当类的对象超出范围时执行。

析构函数的名称是在类的名称前加上一个波浪形（~）作为前缀，它不返回值，也不带任何参数。

析构函数用于在结束程序（比如关闭文件、释放内存等）之前释放资源。析构函数不能继承或重载。

```c#
 public Line() // 构造函数*
   {
     Console.WriteLine("对象已创建");
   }
   ~Line() *//析构函数*
   {
     Console.WriteLine("对象已删除");
   }
```

### 密封类

通过在类定义前面放置关键字 sealed，可以将类声明为 [密封](ms-xhelp:///?method=page&id=8e4ed5d3-10be-47db-9488-0da2008e6f3f&vendor=Microsoft&topicVersion=140&topicLocale=ZH-CN)类

```c#
public sealed class D
{
    // Class members here.
}
```

> 密封类不能用作基类。 因此，它也不能是抽象类。 密封类禁止派生。



### 部分类

- 处理大型项目时，使一个类分布于多个独立文件中可以让多位程序员同时对该类进行处理。
- 若要拆分类定义，请使用 [partial](ms-xhelp:///?method=page&id=27320743-a22e-4c7b-b0b3-53afe3607334&vendor=Microsoft&topicVersion=140&topicLocale=ZH-CN)  关键字修饰符

```c#
public partial class Employee
{
    public void DoWork()
    {
    }
}

public partial class Employee
{
    public void GoToLunch()
    {
    }
}
```

> 如果将任意部分声明为抽象的，则整个类型都被视为抽象的。如果将任意部分声明为密封的，则整个类型都被视为密封的。如果任意部分声明基类型，则整个类型都将继承该类。