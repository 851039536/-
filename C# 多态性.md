# C# 多态性

多态性常被视为自封装和继承之后，面向对象的编程的第三个支柱。 Polymorphism（多态性）是一个希腊词，指“多种形态”，

**多态：**一个接口多个功能。

**静态多态性：**编译时发生函数响应（调用）；

**动态多态性：**运行时发生函数响应。

**静态绑定（早期绑定）：**编译时函数和对象的连接机制。 两种技术实现静态多态性：函数重载/运算符重载。

**函数重载：**在同一范围内对相同函数名有多个定义，可以是参数类型或参数个数的不同，但不许只有返回值类型不同。

**运算符重载：**

关键字 abstract 声明抽象类：用于接口部分类的实现（派生类继承抽象类时，实现完成）。抽象类包含抽象方法，抽象方法可被派生类实现。

## 多态实现

实现多态三种方式：

## 抽象类

C# 允许您使用关键字 **abstract** 创建抽象类，用于提供接口的部分类的实现。当一个派生类继承自该抽象类时，实现即完成。**抽象类**包含抽象方法，抽象方法可被派生类实现。派生类具有更专业的功能。

请注意，下面是有关抽象类的一些规则：

- 抽象类不能实例化。
- 不能在一个抽象类外部声明一个抽象方法。
- 通过在类定义前面放置关键字 **sealed**，可以将类声明为**密封类**。当一个类被声明为 **sealed** 时，它不能被继承。抽象类不能被声明为 sealed。

```c#
public abstract class A
{
    public abstract void DoWork(int i);
}

public class B : A
{
    public override void DoWork(int i)
    {
        // New implementation.
    }
}

//main
A a= new B();
a.Dowirk(1);

```

## 接口类

使用接口可以在类中包括来自多个源的行为。 由于C#语言不支持多重继承，所以该功能很重要。  此外，如果要模拟结构的继承，也必须使用接口，因为它们无法实际从另一个结构或类继承。

一个类能同时实现多个接口，还能在实现接口的同时再继承其他类，并且接口之间也可以继承。

### 接口定义

```c#
interface  接口名称
{
    接口成员；
}

interface IEquatable<T>
{
    bool Equals(T obj);
}

```

接口是一种规范。

为了多态，接口不能被实例化。

接口不能添加访问修饰符，默认公开（piblic）。

接口只能有，方法，属性，索引器，事件，不能有字段和构造函数。

接口与接口直接可以被继承，且可以多继承。

接口不能继承类，类可以继承接口

实现接口的子类必须实现该接口的全部成员

一个类可以同时继承一个类并实现多个接口

### 显示实现

实现目的：解决方法重命名问题

什么时候实现：当继承接口中的方法和参数一模一样时

```c#
interface ILeft
{
    int P { get;}
}
interface IRight
{
    int P();
}
```

为了同时实现两个接口，类必须对属性 P 和/或方法 P 使用显式实现以避免编译器错误。

```c#
class Middle : ILeft, IRight
{
    public int P() { return 0; }
    int ILeft.P { get { return 0; } }
}
```

## 虚方法

类中定义的允许在派生类中重写的方法，使用virtual关键字定义

```c#
 // Virtual method
    public virtual void Draw()
    {
        Console.WriteLine("Performing base class drawing tasks");
    }


class Circle : Shape
{
    public override void Draw()
    {
        // Code to draw a circle...
        Console.WriteLine("Drawing a circle");
        base.Draw();
    }
}


```

