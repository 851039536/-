# c#学习记录

1.面向对象-深入理解
        2.委托,Lambda -深入理解
        3.Task、asyns、await 会用



## 类型转换

| 序号 | 方法 & 描述                                                  |
| :--- | :----------------------------------------------------------- |
| 1    | **ToBoolean** 如果可能的话，把类型转换为布尔型。             |
| 2    | **ToByte** 把类型转换为字节类型。                            |
| 3    | **ToChar** 如果可能的话，把类型转换为单个 Unicode 字符类型。 |
| 4    | **ToDateTime** 把类型（整数或字符串类型）转换为 日期-时间 结构。 |
| 5    | **ToDecimal** 把浮点型或整数类型转换为十进制类型。           |
| 6    | **ToDouble** 把类型转换为双精度浮点型。                      |
| 7    | **ToInt16** 把类型转换为 16 位整数类型。                     |
| 8    | **ToInt32** 把类型转换为 32 位整数类型。                     |
| 9    | **ToInt64** 把类型转换为 64 位整数类型。                     |
| 10   | **ToSbyte** 把类型转换为有符号字节类型。                     |
| 11   | **ToSingle** 把类型转换为小浮点数类型。                      |
| 12   | **ToString** 把类型转换为字符串类型。                        |
| 13   | **ToType** 把类型转换为指定类型。                            |
| 14   | **ToUInt16** 把类型转换为 16 位无符号整数类型。              |
| 15   | **ToUInt32** 把类型转换为 32 位无符号整数类型。              |
| 16   | **ToUInt64** 把类型转换为 64 位无符号整数类型。              |

### 类型转换 int.TryParse() 方法

public static bool TryParse(string s, out Int32 result);

如果转换成功则返回true。否则返回false

int.TryParse(string s,out int i) 的参数： s是要转换的字符串，i 是转换的结果。

执行成功返回true，输出转换成功的值；执行失败返回0



### 将string类型转换枚举类型

枚举 字段 = （枚举）Enum.Pares(typeof(枚举)，转换的字段)；

实例：`QQ state = (QQ) Enum.Pares(typeof(QQ )，str);`

### 使用 System.Enum 方法发现和操作枚举值



```c#
 enum Days { Sunday, Monday, Tuesday, Wednesday, Thursday, Friday, Saturday };

string s = Enum.GetName(typeof(Days), 4);
            Console.WriteLine(s);

            Console.WriteLine("The values of the Days Enum are:");
            foreach (int i in Enum.GetValues(typeof(Days)))
                Console.WriteLine(i);

            Console.WriteLine("The names of the Days Enum are:");
            foreach (string str in Enum.GetNames(typeof(Days)))
                Console.WriteLine(str);
            Console.ReadKey();
```



## 数组类型

> 在 C# 中，数组实际上是对象，数组是一种数据结构，它包含若干相同类型的变量。

###  数组概述

数组具有以下属性：

- 数组可以是 [一维](ms-xhelp:///?method=page&id=2cec1196-1de0-49d2-baf2-c607c33310e8&vendor=Microsoft&topicVersion=110&topicLocale=ZH-CN)、  [多维](ms-xhelp:///?method=page&id=020ce02e-7dff-4273-8e53-bf0b33747232&vendor=Microsoft&topicVersion=110&topicLocale=ZH-CN)或  [交错](ms-xhelp:///?method=page&id=537c65a6-0e0a-4a00-a2b8-086f38519c70&vendor=Microsoft&topicVersion=110&topicLocale=ZH-CN)的。
- 数值数组元素的默认值设置为零，而引用元素的默认值设置为 null。
- 交错数组是数组的数组，因此其元素是引用类型并初始化为 null。
- 数组的索引从零开始：具有 n 个元素的数组的索引是从  0 到 n-1。
- 数组元素可以是任何类型，包括数组类型。
- 数组类型是从抽象基类型 [Array](ms-xhelp:///?method=page&id=t%3asystem.array&vendor=Microsoft&topicVersion=110&topicLocale=ZH-CN)  派生的 [引用类型](ms-xhelp:///?method=page&id=801cf030-6e2d-4a0d-9daf-1431b0c31f47&vendor=Microsoft&topicVersion=110&topicLocale=ZH-CN)。  由于此类型实现了 [IEnumerable](ms-xhelp:///?method=page&id=t%3asystem.collections.ienumerable&vendor=Microsoft&topicVersion=110&topicLocale=ZH-CN)  和 [IEnumerable<  T>  ](ms-xhelp:///?method=page&id=t%3asystem.collections.generic.ienumerable`1&vendor=Microsoft&topicVersion=110&topicLocale=ZH-CN)，因此可以对 C# 中的所有数组使用[foreach](ms-xhelp:///?method=page&id=5a9c5ddc-5fd3-457a-9bb6-9abffcd874ec&vendor=Microsoft&topicVersion=110&topicLocale=ZH-CN)  迭代。

### 一维数组

```c#
int[] array = new int[5];
string[] stringArray = new string[6];
```

> 此数组包含从 array[0] 到 array[4] 的元素。 [new](ms-xhelp:///?method=page&id=e4136516-f058-45a8-84e5-da00a5cf9b6a&vendor=Microsoft&topicVersion=110&topicLocale=ZH-CN)  运算符用于创建数组并将数组元素初始化为它们的默认值。 在此例中，所有数组元素都初始化为零。

#### 数组初始化

```c#
int[] array1 = new int[] { 1, 3, 5, 7, 9 };
string[] weekDays = { "Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat" };
```

### 多维数组

数组可以具有多个维度。 例如，下列声明创建一个四行两列的二维数组。

```c#
int[,] array = new int[4, 2];
```

声明创建一个三维（4、2 和 3）数组。

```c#
int[, ,] array1 = new int[4, 2, 3];
```



#### 数组初始化

```c#
// Two-dimensional array.
int[,] array2D = new int[,] { { 1, 2 }, { 3, 4 }, { 5, 6 }, { 7, 8 } };
// The same array with dimensions specified.
int[,] array2Da = new int[4, 2] { { 1, 2 }, { 3, 4 }, { 5, 6 }, { 7, 8 } };
// A similar array with string elements.
string[,] array2Db = new string[3, 2] { { "one", "two" }, { "three", "four" },
                                        { "five", "six" } };

// Three-dimensional array.
int[, ,] array3D = new int[,,] { { { 1, 2, 3 }, { 4, 5, 6 } }, 
                                 { { 7, 8, 9 }, { 10, 11, 12 } } };
// The same array with dimensions specified.
int[, ,] array3Da = new int[2, 2, 3] { { { 1, 2, 3 }, { 4, 5, 6 } }, 
                                       { { 7, 8, 9 }, { 10, 11, 12 } } };

// Accessing array elements.
System.Console.WriteLine(array2D[0, 0]);
System.Console.WriteLine(array2D[0, 1]);
System.Console.WriteLine(array2D[1, 0]);
System.Console.WriteLine(array2D[1, 1]);
System.Console.WriteLine(array2D[3, 0]);
System.Console.WriteLine(array2Db[1, 0]);
System.Console.WriteLine(array3Da[1, 0, 1]);
System.Console.WriteLine(array3D[1, 1, 2]);

// Output:
// 1
// 2
// 3
// 4
// 7
// three
// 8
// 12
```

也可以初始化数组但不指定级别。

```c#
int[,] array4 = { { 1, 2 }, { 3, 4 }, { 5, 6 }, { 7, 8 } };
```

如果选择声明一个数组变量但不将其初始化，必须使用 new 运算符将一个数组分配给此变量。 以下示例显示 new  的用法。

```c#
int[,] array5;
array5 = new int[,] { { 1, 2 }, { 3, 4 }, { 5, 6 }, { 7, 8 } };   // OK
//array5 = {{1,2}, {3,4}, {5,6}, {7,8}};   // Error
```

将值分配给特定的数组元素。

```c#
array5[2, 1] = 25;
```

将数组元素初始化为默认值（交错数组除外）：

```c#
int[,] array6 = new int[10, 10];
```



### 交错数组

> 交错数组是元素为数组的数组。 交错数组元素的维度和大小可以不同。 交错数组有时称为“数组的数组”。

```c#
int[][] jaggedArray = new int[3][];
```

必须初始化 jaggedArray  的元素后才可以使用它。

```c#
jaggedArray[0] = new int[5];
jaggedArray[1] = new int[4];
jaggedArray[2] = new int[2];
```

每个元素都是一个一维整数数组。 第一个元素是由 5 个整数组成的数组，第二个是由 4 个整数组成的数组，而第三个是由 2  个整数组成的数组。

```c#
jaggedArray[0] = new int[] { 1, 3, 5, 7, 9 };
jaggedArray[1] = new int[] { 0, 2, 4, 6 };
jaggedArray[2] = new int[] { 11, 22 };
```

声明数组时将其初始化

```c#
int[][] jaggedArray2 = new int[][] 
{
    new int[] {1,3,5,7,9},
    new int[] {0,2,4,6},
    new int[] {11,22}
};
```

访问个别数组元素：

```c#
// Assign 77 to the second element ([1]) of the first array ([0]):
jaggedArray3[0][1] = 77;

// Assign 88 to the second element ([1]) of the third array ([2]):
jaggedArray3[2][1] = 88;
```

### 数组使用 foreach

创建一个名为 numbers 的数组，并用 foreach 语句循环访问该数组：

```c#
int[] numbers = { 4, 5, 6, 1, 2, 3, -2, -1, 0 };
foreach (int i in numbers)
{
    System.Console.Write("{0} ", i);
}
// Output: 4 5 6 1 2 3 -2 -1 0
```

多维数组，可以使用相同方法来循环访问元素

```c#
int[,] numbers2D = new int[3, 2] { { 9, 99 }, { 3, 33 }, { 5, 55 } };
// Or use the short form:
// int[,] numbers2D = { { 9, 99 }, { 3, 33 }, { 5, 55 } };

foreach (int i in numbers2D)
{
    System.Console.Write("{0} ", i);
}
// Output: 9 99 3 33 5 55
```

### 一维数组传递给方法

```c#
int[] theArray = { 1, 3, 5, 7, 9 };
PrintArray(theArray);
```

print 方法的部分实现。

```c#
void PrintArray(int[] arr)
{
    // Method code.
}
```

初始化和传递新数组

```c#
PrintArray(new int[] { 1, 3, 5, 7, 9 });
```

官方示例

```c#
class ArrayClass
{
    static void PrintArray(string[] arr)
    {
        for (int i = 0; i < arr.Length; i++)
        {
            System.Console.Write(arr[i] + "{0}", i < arr.Length - 1 ? " " : "");
        }
        System.Console.WriteLine();
    }

    static void ChangeArray(string[] arr)
    {
        // The following attempt to reverse the array does not persist when
        // the method returns, because arr is a value parameter.
        arr = (arr.Reverse()).ToArray();
        // The following statement displays Sat as the first element in the array.
        System.Console.WriteLine("arr[0] is {0} in ChangeArray.", arr[0]);
    }

    static void ChangeArrayElements(string[] arr)
    {
        // The following assignments change the value of individual array 
        // elements. 
        arr[0] = "Sat";
        arr[1] = "Fri";
        arr[2] = "Thu";
        // The following statement again displays Sat as the first element
        // in the array arr, inside the called method.
        System.Console.WriteLine("arr[0] is {0} in ChangeArrayElements.", arr[0]);
    }

    static void Main()
    {
        // Declare and initialize an array.
        string[] weekDays = { "Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat" };

        // Pass the array as an argument to PrintArray.
        PrintArray(weekDays);

        // ChangeArray tries to change the array by assigning something new
        // to the array in the method. 
        ChangeArray(weekDays);

        // Print the array again, to verify that it has not been changed.
        System.Console.WriteLine("Array weekDays after the call to ChangeArray:");
        PrintArray(weekDays);
        System.Console.WriteLine();

        // ChangeArrayElements assigns new values to individual array
        // elements.
        ChangeArrayElements(weekDays);

        // The changes to individual elements persist after the method returns.
        // Print the array, to verify that it has been changed.
        System.Console.WriteLine("Array weekDays after the call to ChangeArrayElements:");
        PrintArray(weekDays);
    }
}
// Output: 
// Sun Mon Tue Wed Thu Fri Sat
// arr[0] is Sat in ChangeArray.
// Array weekDays after the call to ChangeArray:
// Sun Mon Tue Wed Thu Fri Sat
// 
// arr[0] is Sat in ChangeArrayElements.
// Array weekDays after the call to ChangeArrayElements:
// Sat Fri Thu Wed Thu Fri Sat
```

### 多维数组传递给方法

```c#
int[,] theArray = { { 1, 2 }, { 2, 3 }, { 3, 4 } };
Print2DArray(theArray);
```

该方法接受一个二维数组作为其参数。

```c#
void Print2DArray(int[,] arr)
{
    // Method code.
}
```

初始化和传递新数组

```c#
Print2DArray(new int[,] { { 1, 2 }, { 3, 4 }, { 5, 6 }, { 7, 8 } });
```

官方示例

```c#
class ArrayClass2D
{
    static void Print2DArray(int[,] arr)
    {
        // Display the array elements.
        for (int i = 0; i < arr.GetLength(0); i++)
        {
            for (int j = 0; j < arr.GetLength(1); j++)
            {
                System.Console.WriteLine("Element({0},{1})={2}", i, j, arr[i, j]);
            }
        }
    }
    static void Main()
    {
        // Pass the array as an argument.
        Print2DArray(new int[,] { { 1, 2 }, { 3, 4 }, { 5, 6 }, { 7, 8 } });

        // Keep the console window open in debug mode.
        System.Console.WriteLine("Press any key to exit.");
        System.Console.ReadKey();
    }
}
    /* Output:
        Element(0,0)=1
        Element(0,1)=2
        Element(1,0)=3
        Element(1,1)=4
        Element(2,0)=5
        Element(2,1)=6
        Element(3,0)=7
        Element(3,1)=8
    */
```

### 使用 ref 和 out 传递数组

使用数组类型的 out 参数前必须先为其赋值，即必须由被调用方为其赋值。

```c#
static void TestMethod1(out int[] arr)
{
    arr = new int[10];   // definite assignment of arr
}
```

与所有的 [ref](ms-xhelp:///?method=page&id=b8a5e59c-907d-4065-b41d-95bf4273c0bd&vendor=Microsoft&topicVersion=110&topicLocale=ZH-CN)  参数一样，数组类型的 ref 参数必须由调用方明确赋值。 因此不需要由接受方明确赋值。 可以将数组类型的  ref 参数更改为调用的结果。

```c#
static void TestMethod2(ref int[] arr)
{
    arr = new int[10];   // arr initialized to a different array
}
```

示例

在调用方（Main 方法）中声明数组 theArray，并在  FillArray 方法中初始化此数组。 然后将数组元素返回调用方并显示。

```c#
class TestOut
{
    static void FillArray(out int[] arr)
    {
        // Initialize the array:
        arr = new int[5] { 1, 2, 3, 4, 5 };
    }

    static void Main()
    {
        int[] theArray; // Initialization is not required

        // Pass the array to the callee using out:
        FillArray(out theArray);

        // Display the array elements:
        System.Console.WriteLine("Array elements are:");
        for (int i = 0; i < theArray.Length; i++)
        {
            System.Console.Write(theArray[i] + " ");
        }

        // Keep the console window open in debug mode.
        System.Console.WriteLine("Press any key to exit.");
        System.Console.ReadKey();
    }
}
    /* Output:
        Array elements are:
        1 2 3 4 5        
    */
```

在调用方（Main 方法）中初始化数组 theArray，并通过使用 ref  参数将其传递给 FillArray 方法。 在  FillArray 方法中更新某些数组元素。 然后将数组元素返回调用方并显示

```c#
class TestRef
{
    static void FillArray(ref int[] arr)
    {
        // Create the array on demand:
        if (arr == null)
        {
            arr = new int[10];
        }
        // Fill the array:
        arr[0] = 1111;
        arr[4] = 5555;
    }

    static void Main()
    {
        // Initialize the array:
        int[] theArray = { 1, 2, 3, 4, 5 };

        // Pass the array using ref:
        FillArray(ref theArray);

        // Display the updated array:
        System.Console.WriteLine("Array elements are:");
        for (int i = 0; i < theArray.Length; i++)
        {
            System.Console.Write(theArray[i] + " ");
        }

        // Keep the console window open in debug mode.
        System.Console.WriteLine("Press any key to exit.");
        System.Console.ReadKey();
    }
}
    /* Output:
        Array elements are:
        1111 2 3 4 5555
    */
```

### 隐式类型的数组

如何创建隐式类型的数组：

```c#
class ImplicitlyTypedArraySample
{
    static void Main()
    {
        var a = new[] { 1, 10, 100, 1000 }; // int[]
        var b = new[] { "hello", null, "world" }; // string[]

        // single-dimension jagged array
        var c = new[]   
{  
    new[]{1,2,3,4},
    new[]{5,6,7,8}
};

        // jagged array of strings
        var d = new[]   
{
    new[]{"Luca", "Mads", "Luke", "Dinesh"},
    new[]{"Karen", "Suma", "Frances"}
};
    }
}
```

创建包含数组的匿名类型时，必须在该类型的对象初始值设定项中对数组进行隐式类型化。 在下面的示例中，contacts  是一个隐式类型的匿名类型数组，其中每个匿名类型都包含一个名为 PhoneNumbers 的数组。  请注意，对象初始值设定项内部未使用 var 关键字。

```c#
var contacts = new[] 
{
    new {
            Name = " Eugene Zabokritski",
            PhoneNumbers = new[] { "206-555-0108", "425-555-0001" }
        },
    new {
            Name = " Hanying Feng",
            PhoneNumbers = new[] { "650-555-0199" }
        }
};
```

## 冒泡排序

冒泡排序：就是将一个数组中的元素按照从大到小或者从小到大的排序进行排列。

```c#
int[] nums={9,8,7,6,5,4,3,2,1,0}; 0 1 2 3 4 5 6 7 8 9
第一趟比较：8 7 6 5 4 3 2 1 0 9 交换了9次   i=0 j=nums.Length-1-i
第二趟比较：7 6 5 4 3 2 1 0 8 9 交换了8次   i=1 j=nums.Length-1-i
第三趟比较：6 5 4 3 2 1 0 7 8 9 交换了7次   i=2 j=nums.Length-1-i
第四趟比较：5 4 3 2 1 0 6 7 8 9 交换了6次   i=3 j=nums.Length-1-i
第五趟比较：4 3 2 1 0 5 6 7 8 9 交换了5次
第六趟比较：3 2 1 0 4 5 6 7 8 9 交换了4次
第七趟比较：2 1 0 3 4 5 6 7 8 9 交换了3次
第八趟比较：1 0 2 3 4 5 6 7 8 9 交换了2次
第九趟比较：0 1 2 3 4 5 6 7 8 9 交换了1次
```

[排序实例](https://www.cnblogs.com/qy1234/p/11768501.html)

