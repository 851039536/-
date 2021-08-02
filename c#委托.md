# c#委托

### 必须满足4个条件：

-  声明委托类型；
-  必须有一个方法包含了要执行的代码；
-   必须创建一个委托实例；
-  必须调用（ invoke）委托实例。  

委托声明

```c#
  public delegate void MyDel();//声明一个自定义委托
```

> C#要求委托必须具有完全相同的参数类型。  

调用

```c#
 MyDel say1 = testName;
 MyDel say2 = new MyDel(testName);
 say1();
 say2();
```

