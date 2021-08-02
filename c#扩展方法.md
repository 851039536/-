# c#扩展方法

### 教程参考

[[C#扩展方法知多少](https://www.cnblogs.com/landeanfen/p/4632467.html)]

### 声明扩展方法

> 并不是任何方法都能作为扩展方法使用——它必须具有以下特征：
> 它必须在一个非嵌套的、非泛型的静态类中（所以必须是一个静态方法）；
>  它至少要有一个参数；
>  第一个参数必须附加this关键字作为前缀；
>  第一个参数不能有其他任何修饰符（比如out或ref）；
>  第一个参数的类型不能是指针类型。  

**使用扩展方法有几个值得注意的地方：**

（1）扩展方法不能和调用的方法放到同一个类中

（2）第一个参数必须要，并且必须是this，这是扩展方法的标识。如果方法里面还要传入其他参数，可以在后面追加参数

（3）最好保证扩展方法和调用方法在同一个命名空间下



#### 示例1

```c#
 //必须是静态类才可以添加扩展方法
static class Tests
    {
          public static string Test(this string test ,string name)
        {
            return name;
        }
    }
    class Program
    {

        static void Main(string[] args)
        {
           string name = null;
           var test = name.Test("name");
            Console.WriteLine(test);
            Console.ReadKey();

        }
      }
```

### **一般对象的扩展方法**



```c#
 public class Person
    {
        public string Name { set; get; }
        public int Age { set; get; }
    }
```

```c#
//必须是静态类才可以添加扩展方法
//Person的扩展方法，根据年龄判断是否是成年人
        public static bool GetBIsChild(this Person oPerson)
        {
            if (oPerson.Age >= 18)
                return false;
            else
                return true;
        }
```

Main方法里面调用：

```c#
var oPerson1 = new Person();
oPerson1.Age = 20;
var bIsChild = oPerson1.GetBIsChild();        
```

###  泛型对象的扩展方法

```c#

public static class DataContractExtensions
{
  //必须是静态类才可以添加扩展方法
  //测试方法
  public static T Test<T>(this T instance) where T : Test2
  {
       T Res = default(T);
       try
       {
           Res.AttrTest = instance.AttrTest.Substring(0,2);
           //其他复杂逻辑...
      }
      catch
      { }
      return Res;
  }

}

public class Test2
{
　　public string AttrTest { set; get; }
}
```

### 在空引用上调用方法

```c#
//必须是静态类才可以添加扩展方法
static class Tests
    {
       public static bool IsNull(this object x)
        {
            return x == null;
        }


    }
    class Program
    {

        static void Main(string[] args)
        {
           object y =null;
            Console.WriteLine(y.IsNull());
            y= new object();
            Console.WriteLine(y.IsNull());
            Console.ReadKey();

        }
    }    
```

> 输出先是True，然后是False。如果IsNull是一个普通的实例方法， Main
> 的第2行就会抛出一个异常。但是，这里的null是IsNull的实参。在扩展方法问世前， y.Isnull()
> 这样的写法虽然可读性更好，却不合法，只能采用NullUtil.IsNull(y)这样的写法  

### 集合扩展方法

```c#
 public static void ShowItems<T>(this IEnumerable<T> _al)
        {
            foreach (var item in _al)
            {
                Console.WriteLine(item);
            }
        }
```

```c#
 static void Main(string[] args)
        {
            //调用集合的扩展方法
            str.ShowItems<char>();
        }
```

