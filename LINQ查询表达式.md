# LINQ查询表达式

### 什么是查询表达式

> “查询表达式”是用查询语法表示的查询， 是一流的语言构造。 它就像任何其他表达式一样，并且可以用在  C# 表达式有效的任何上下文中。 查询表达式由一组用类似于 SQL 或 XQuery  的声明性语法编写的子句组成。 每个子句又包含一个或多个 C#  表达式，而这些表达式本身又可能是查询表达式或包含查询表达式。
>
> 查询表达式必须以 [from](ms-xhelp:///?method=page&id=1aefd18c-1314-47f8-99ec-9bcefb09e699&vendor=Microsoft&topicVersion=140&topicLocale=ZH-CN)  子句开头，并且必须以 [select](ms-xhelp:///?method=page&id=df01e266-5781-4aaa-80c4-67cf28ea093f&vendor=Microsoft&topicVersion=140&topicLocale=ZH-CN)  或 [group](ms-xhelp:///?method=page&id=c817242e-b12c-4baa-a57e-73ee138f34d1&vendor=Microsoft&topicVersion=140&topicLocale=ZH-CN)  子句结尾。 在第一个 from 子句和最后一个 select 或 group  子句之间，查询表达式可以包含一个或多个下列可选子句： [where](ms-xhelp:///?method=page&id=7f9bf952-7744-4f91-b676-cddb55d107c3&vendor=Microsoft&topicVersion=140&topicLocale=ZH-CN)、  [orderby](ms-xhelp:///?method=page&id=21f87f48-d69d-4e95-9a52-6fec47b37e1f&vendor=Microsoft&topicVersion=140&topicLocale=ZH-CN)、  [join](ms-xhelp:///?method=page&id=76e9df84-092c-41a6-9537-c3f1cbd7f0fb&vendor=Microsoft&topicVersion=140&topicLocale=ZH-CN)、  [let](ms-xhelp:///?method=page&id=13c9c1a4-ce57-48ef-8e1b-4c2a59b99fb4&vendor=Microsoft&topicVersion=140&topicLocale=ZH-CN)  甚至附加的 [from](ms-xhelp:///?method=page&id=1aefd18c-1314-47f8-99ec-9bcefb09e699&vendor=Microsoft&topicVersion=140&topicLocale=ZH-CN)  子句。 还可以使用 [into](ms-xhelp:///?method=page&id=81ec62c1-f0b1-4755-8a31-959876e77f65&vendor=Microsoft&topicVersion=140&topicLocale=ZH-CN)  关键字使 join 或 group 子句的结果能够充当同一查询表达式中附加查询子句的源。



### 简单的查询表达式

> 它含有一个数据源、一个筛选子句和一个排序子句，但不对源元素进行转换。  select  子句结束了该查询。

```c#
static void Main()
{
    // Data source.
    int[] scores = { 90, 71, 82, 93, 75, 82 };

    // Query Expression.
    IEnumerable<int> scoreQuery = //query variable
        from score in scores //required
        where score > 80 // optional
        orderby score descending // optional
        select score; //must end with select or group

    // Execute the query to produce the results
    foreach (int testScore in scoreQuery)
    {
        Console.WriteLine(testScore);
    }                  
}
// Outputs: 93 90 82 82
```

> 在下面的示例中，queryMajorCities 和 queryMajorCities2 都是查询变量：

```c#
//Query syntax
IEnumerable<City> queryMajorCities =
    from city in cities
    where city.Population > 100000
    select city;

// Method-based syntax
IEnumerable<City> queryMajorCities2 = cities.Where(c => c.Population > 100000);
```



> 下面的两个示例演示了不是查询变量的变量，即使每个变量都用查询进行了初始化。 它们不是查询变量的原因是它们存储了结果：

```c#
int highestScore =
    (from score in scores
     select score)
    .Max();

// or split the expression
IEnumerable<int> scoreQuery =
    from score in scores
    select score;

int highScore = scoreQuery.Max();

List<City> largeCitiesList =
    (from country in countries
     from city in country.Cities
     where city.Population > 10000
     select city)
       .ToList();

// or split the expression
IEnumerable<City> largeCitiesQuery =
    from country in countries
    from city in country.Cities
    where city.Population > 10000
    select city;

List<City> largeCitiesList2 = largeCitiesQuery.ToList();
```

### 查询变量的显式类型化和隐式类型化



> 使用 [var](ms-xhelp:///?method=page&id=0777850a-2691-4e3e-927f-0c850f5efe15&vendor=Microsoft&topicVersion=140&topicLocale=ZH-CN)  关键字指示编译器在编译时推断查询变量（或任何其他本地变量）的类型。 例如，还可以使用隐式类型化表示本主题前面部分中演示的查询示例：

```c#
// var的使用在这里和所有查询中都是可选的。
// queryCities是一个IEnumerable<City>
var queryCities =
    from city in cities
    where city.Population > 100000
    select city;
```

### 开始查询表达式



> 表达式必须以 from  子句开头。 同时指定了数据源和范围变量。 在对源序列进行遍历的过程中，范围变量表示源序列中的每个后续元素。 将根据数据源中元素的类型对范围变量进行强类型化。 下面示例中，因为 countries 是 Country 对象数组，所以范围变量也被类型化为 Country， 这样就可以使用点运算符来访问该类型的任何可用成员。

```c#
IEnumerable<Country> countryAreaQuery =
    from country in countries
    where country.Area > 500000 //sq km
    select country;
```



> 查询表达式可以包含多个 from 子句。 当源序列中的每个元素本身就是集合或包含集合时，可使用附加的 from 子句。 例如，假定您具有一个  Country 对象集合，而其中每个对象都包含一个名为 Cities 的 City 对象集合。 若要查询每个 Country 中的 City 对象，请使用两个from  子句，如下所示：

```c#
IEnumerable<City> cityQuery =
    from country in countries
    from city in country.Cities
    where city.Population > 10000
    select city;
```

###  结束查询表达式

查询表达式必须以 select  子句或 group 子句结尾。

#### group 子句

> 使用 group  子句可产生按照指定的键组织的组序列。 键可以采用任何数据类型。 例如，下面的查询创建一个组序列，该序列包含一个或多个 Country  对象，并且它的键是 char 值。

```c#
var queryCountryGroups =
    from country in countries
    group country by country.Name[0];
```

#### select 子句

> 使用 select  子句可产生所有其他类型的序列。 简单的 select 子句只是产生与数据源中包含的对象具有相同类型的对象的序列。 在此示例中，数据源包含 Country 对象。 orderby 子句只是将元素重新排序，而  select 子句则产生重新排序的 Country 对象的序列。

```c#
IEnumerable<Country> sortedQuery =
    from country in countries
    orderby country.Area
    select country;
```



>  在下面的示例中，select  子句对一个匿名类型序列进行投影，该序列仅包含原始元素中各字段的子集。

```c#
// 在这里，var是必需的，因为查询
// 生成一个匿名类型
var queryNameAndPop =
    from country in countries
    select new { Name = country.Name, Pop = country.Population };
```



#### 使用“into”进行延续

> 可以在 select 或  group 子句中使用 into 关键字来创建用于存储查询的临时标识符。 当您必须在分组或选择操作之后对查询执行附加查询操作时，需要这样做。

```c#
// percentileQuery is an IEnumerable<IGrouping<int, Country>>
var percentileQuery =
    from country in countries
    let percentile = (int) country.Population / 10000000
    group country by percentile into countryGroup
    where countryGroup.Key >= 20
    orderby countryGroup.Key
    select countryGroup;

// grouping is an IGrouping<int, Country>
foreach (var grouping in percentileQuery)
{
    Console.WriteLine(grouping.Key);
    foreach (var country in grouping)
        Console.WriteLine(country.Name + ":" + country.Population);
}
```



### 筛选、排序和联接

> 在 from 开始子句以及  select 或 group 结束子句之间，所有其他子句（where、join、orderby、from、let）都是可选的。 任何可选子句都可以在查询正文中使用零次或多次。

#### where 子句

> 使用 where  子句可以根据一个或多个谓词表达式筛选掉源数据中的某些元素。 以下示例中的 where 子句含有两个谓词。

```c#
IEnumerable<City> queryCityPop =
    from city in cities
    where city.Population < 200000 && city.Population > 100000
    select city;
```



> where 子句使用一个方法来确定范围变量的当前值是偶数还是奇数。

```c#
class WhereSample3
{       
    static void Main()
    {
        // Data source
        int[] numbers = { 5, 4, 1, 3, 9, 8, 6, 7, 2, 0 };

        // Create the query with a method call in the where clause.
        // Note: This won't work in LINQ to SQL unless you have a
        // stored procedure that is mapped to a method by this name.
        var queryEvenNums =
            from num in numbers
            where IsEven(num)
            select num;
         // Execute the query.
        foreach (var s in queryEvenNums)
        {
            Console.Write(s.ToString() + " ");
        }
    }

    // Method may be instance method or static method.
    static bool IsEven(int i)
    {
        return i % 2 == 0;
    }    
}
//Output: 4 8 6 2 0
```



> where  子句是一种筛选机制。 除了不能是第一个或最后一个子句外，它几乎可以放在查询表达式中的任何位置。 where 子句可以出现在 [group](ms-xhelp:///?method=page&id=c817242e-b12c-4baa-a57e-73ee138f34d1&vendor=Microsoft&topicVersion=140&topicLocale=ZH-CN)  子句的前面或后面，具体情况取决于是必须在对源元素进行分组之前还是之后来筛选源元素。
>
> 如果指定的谓词对于数据源中的元素无效，则会发生编译时错误。 这是 LINQ 提供的强类型检查的一个优点。
>
> 编译时，where  关键字会被转换为对 [Where](ms-xhelp:///?method=page&id=overload%3asystem.linq.enumerable.where&vendor=Microsoft&topicVersion=140&topicLocale=ZH-CN)  标准查询运算符方法的调用。



#### orderby 子句

> 使用 orderby  子句可以按升序或降序对结果进行排序。 您还可以指定次要排序顺序。 下面的示例使用 Area 属性对 country 对象执行主要排序， 然后使用 Population 属性执行次要排序。

```c#
IEnumerable<Country> querySortedCountries =
    from country in countries
    orderby country.Area, country.Population descending
    select country;
```

> ascending  关键字是可选的；如果未指定顺序，则它是默认排序顺序。 有关更多信息，请参见 [orderby  子句（C# 参考）](ms-xhelp:///?method=page&id=21f87f48-d69d-4e95-9a52-6fec47b37e1f&vendor=Microsoft&topicVersion=140&topicLocale=ZH-CN)。



#### join 子句

> 使用 join  子句可以根据每个元素中指定键之间的相等比较，对一个数据源中的元素与另外一个数据源中的元素进行关联和/或组合。 在 LINQ 中，联接操作是针对其元素具有不同类型的对象序列执行的。 在联接两个序列之后，必须使用 select 或  group 语句指定要存储到输出序列中的元素。 还可以使用匿名类型将每组关联元素中的属性组合为输出序列的新类型。 



join后面跟所要连接的集合，on后面跟的是连接条件。

```c#
var categoryQuery =
    from cat in categories
    join prod in products on cat equals prod.Category
    select new { Category = cat, Name = prod.Name };
```



> join 子句使用特殊的  equals 关键字比较指定的键是否相等。 join  子句执行的所有联接都是同等联接。 join 子句的输出形式取决于所执行的联接的具体类型。 以下是三种最常见的联接类型：
>
> - 内部联接
> - 分组联接
> - 左外部联接

##### 内部联接

> 下面的示例演示一个简单的内部同等联接。 此查询产生一个“产品名称/类别”对平面序列。 同一类别字符串将出现在多个元素中。 如果 categories 中的某个元素不具有匹配的 products，则该类别不会出现在结果中。

```c#
var innerJoinQuery =
    from category in categories
    join prod in products on category.ID equals prod.CategoryID
    select new { ProductName = prod.Name, Category = category.Name }; //produces flat sequence
```



##### [Group Join](#)

> 含有 into 表达式的  join 子句称为分组联接。

```c#
var innerGroupJoinQuery =
    from category in categories
    join prod in products on category.ID equals prod.CategoryID into prodGroup
    select new { CategoryName = category.Name, Products = prodGroup };
```





##### 左外部联接

> 在左外部联接中，将返回左侧源序列中的所有元素，即使它们在右侧序列中没有匹配的元素也是如此。  若要在 LINQ 中执行左外部联接，请将 **DefaultIfEmpty**  方法与分组联接结合起来，以指定要在某个左侧元素不具有匹配元素时产生的默认右侧元素。 可以使用  null 作为任何引用类型的默认值，也可以指定用户定义的默认类型。  下面的示例演示了用户定义的默认类型：

```c#
var leftOuterJoinQuery =
    from category in categories
    join prod in products on category.ID equals prod.CategoryID into prodGroup
    from item in prodGroup.DefaultIfEmpty(new Product { Name = String.Empty, CategoryID = 0 })
    select new { CatName = category.Name, ProdName = item.Name };
```

##### [关联多个条件的写法](https://www.cnblogs.com/willingtolove/p/11872645.html)



###### 1、sql语句：

```sql
select * from Users u join Teachers t 
on u.UserID==t.TeacherID and u.Name=t.Name
```

###### 2、linq写法：

**1）、方法一：**
将多个字段的比较，new 一个对象进行比较；
注意：对象的属性名要保持一致；

```csharp
var userInfos = from u in db.Users join t in db.Teachers 
				on new { u.UserID , u.Name } equals new {  UserID = t.TeacherID,t.Name }
				select new { ID=u.UserID, t.Name };
```

**2）、方法二：**

```csharp
var userInfos = from u in db.Users
                from t in db.Teachers
                where u.UserID == t.TeacherID && u.Name == t.Name
                select new { ID=u.UserID, t.Name };
```



#### let 子句

> 使用 let  子句可以将表达式（如方法调用）的结果存储到新的范围变量中。 在下面的示例中，范围变量 firstName 存储了 Split  返回的字符串数组的第一个元素。



```c#
string[] names = { "Svetlana Omelchenko", "Claire O'Donnell", "Sven Mortensen", "Cesar Garcia" };
IEnumerable<string> queryFirstNames =
    from name in names
    let firstName = name.Split(new char[] { ' ' })[0]
    select firstName;

foreach (string s in queryFirstNames)
    Console.Write(s + " ");
//Output: Svetlana Claire Sven Cesar
```

#### 查询表达式中的子查询

> 查询子句本身可能包含一个查询表达式，该查询表达式有时称为“子查询”。 每个子查询都以它自己的 from  子句开头，该子句不一定指向第一个 from 子句中的同一数据源。  例如，下面的查询演示了一个在 select  语句中使用的查询表达式，用来检索分组操作的结果。

```c#
var queryGroupMax =
    from student in students
    group student by student.GradeLevel into studentGroup
    select new
    {
        Level = studentGroup.Key,
        HighestScore =
            (from student2 in studentGroup
             select student2.Scores.Average())
             .Max()
    };
```



### 在查询表达式中处理 Null 值

> 采用防御方式进行编码以避免 null 引用异常，如下面的示例中所示：

```c#
var query1 =
    from c in categories
    where c != null
    join p in products on c.ID equals
        (p == null ? null : p.CategoryID)
    select new { Category = c.Name, Name = p.Name };
```



> 在 join 子句中，只要其中一个比较键是可以为 null  的类型，您就可以在查询表达式中将另一个比较键强制转换成可以为 null 的类型。 在下面的示例中，假定 EmployeeID 是一个列，其中包含类型为  int? 的值：

```c#
void TestMethod(Northwind db)
{
    var query =
        from o in db.Orders
        join e in db.Employees
            on o.EmployeeID equals (int?)e.EmployeeID
        select new { o.OrderID, e.FirstName };
}
```



### 在查询表达式中处理异常

> 下面的示例演示如何将异常处理代码移至查询表达式外部。 仅当该方法不依赖于查询的任何本地变量时，才能这样做。

```c#
class ExceptionsOutsideQuery
{
    static void Main()
    {
        // DO THIS with a datasource that might
        // throw an exception. It is easier to deal with
        // outside of the query expression.
        IEnumerable<int> dataSource;
        try
        {
            dataSource = GetData();
        }
        catch (InvalidOperationException)
        {
            // Handle (or don't handle) the exception 
            // in the way that is appropriate for your application.
            Console.WriteLine("Invalid operation");
            goto Exit;
        }

        // If we get here, it is safe to proceed.
        var query = from i in dataSource
                    select i * i;

        foreach (var i in query)
            Console.WriteLine(i.ToString());

        //Keep the console window open in debug mode
        Exit:
        Console.WriteLine("Press any key to exit");
        Console.ReadKey();
    }

    // A data source that is very likely to throw an exception!
    static IEnumerable<int> GetData()
    {
        throw new InvalidOperationException();
    }
}
```



### **简单的linq语法**

转自:[[C# LINQ语法详解](https://www.cnblogs.com/zhaoshujie/p/10434657.html)]

#### **1.简单的linq语法**



```c#
//1
var ss = from r in db.Am_recProScheme
         select r;
//2
var ss1 = db.Am_recProScheme;
//3
string sssql = "select * from Am_recProScheme";
```



#### 2.带where的查询



```c#
//1
var ss = from r in db.Am_recProScheme
         where r.rpId > 10
         select r;
//2
var ss1 = db.Am_recProScheme.Where(p => p.rpId > 10);
//3
string sssql = "select * from Am_recProScheme where rpid>10";
```



#### 3.简单的函数计算（count，min，max，sum）



```c#
//1
////获取最大的rpId
//var ss = (from r in db.Am_recProScheme
//          select r).Max(p => p.rpId);
////获取最小的rpId
//var ss = (from r in db.Am_recProScheme
//          select r).Min(p => p.rpId);
//获取结果集的总数
//var ss = (from r in db.Am_recProScheme                  
//         select r).Count();
//获取rpId的和
var ss = (from r in db.Am_recProScheme
          select r).Sum(p => p.rpId);


//2
//var ss1 = db.Am_recProScheme.Max(p=>p.rpId);
//var ss1 = db.Am_recProScheme.Min(p => p.rpId);
//var ss1 = db.Am_recProScheme.Count() ;
var ss1 = db.Am_recProScheme.Sum(p => p.rpId);
Response.Write(ss);

//3
string sssql = "select max(rpId) from Am_recProScheme";
       sssql = "select min(rpId) from Am_recProScheme";
       sssql = "select count(1) from Am_recProScheme";
       sssql = "select sum(rpId) from Am_recProScheme";
```



#### 4.排序order by desc/asc



```c#
var ss = from r in db.Am_recProScheme
         where r.rpId > 10
         orderby r.rpId descending  //倒序
         //  orderby r.rpId ascending   //正序
         select r;
//正序
var ss1 = db.Am_recProScheme.OrderBy(p => p.rpId).Where(p => p.rpId > 10).ToList();
//倒序
var ss2 = db.Am_recProScheme.OrderByDescending(p => p.rpId).Where(p => p.rpId > 10).ToList();
string sssql = "select * from Am_recProScheme where rpid>10 order by rpId [desc|asc]";
```



#### 5.top(1)



```c#
//如果取最后一个可以按倒叙排列再取值
var ss = (from r in db.Am_recProScheme
          select r).FirstOrDefault();
//（）linq to ef 好像不支持 Last() 
var ss1 = db.Am_recProScheme.FirstOrDefault();
//var ss1 = db.Am_recProScheme.First();          
string sssql = "select top(1) * from Am_recProScheme";
```



#### 6.跳过前面多少条数据取余下的数据



```c#
//1
var ss = (from r in db.Am_recProScheme
          orderby r.rpId descending
          select r).Skip(10); //跳过前10条数据，取10条之后的所有数据   
//2  
var ss1 = db.Am_recProScheme.OrderByDescending(p => p.rpId).Skip(10).ToList();
//3
string sssql = "select * from  (select ROW_NUMBER()over(order by rpId desc) as rowNum, * from [Am_recProScheme]) as t where rowNum>10";
```



#### 7.分页数据查询



```c#
//1
var ss = (from r in db.Am_recProScheme
          where r.rpId > 10
          orderby r.rpId descending
          select r).Skip(10).Take(10); //取第11条到第20条数据
//2 Take(10): 数据从开始获取，获取指定数量（10）的连续数据
var ss1 = db.Am_recProScheme.OrderByDescending(p => p.rpId).Where(p => p.rpId > 10).Skip(10).Take(10).ToList();
//3
string sssql = "select * from  (select ROW_NUMBER()over(order by rpId desc) as rowNum, * from [Am_recProScheme]) as t where rowNum>10 and rowNum<=20";
```



#### 8.包含，类似like '%%'



```c#
//1
var ss = from r in db.Am_recProScheme
         where r.SortsText.Contains("张")
         select r;
//2
var ss1 = db.Am_recProScheme.Where(p => p.SortsText.Contains("张")).ToList();
//3
string sssql = "select * from Am_recProScheme where SortsText like '%张%'";
```



#### 9.分组group by



```c#
//1
var ss = from r in db.Am_recProScheme
         orderby r.rpId descending
         group r by r.recType into n
         select new
         {
             n.Key,  //这个Key是recType
             rpId = n.Sum(r => r.rpId), //组内rpId之和
             MaxRpId = n.Max(r => r.rpId),//组内最大rpId
             MinRpId = n.Min(r => r.rpId), //组内最小rpId
         };
foreach (var t in ss)
{
    Response.Write(t.Key + "--" + t.rpId + "--" + t.MaxRpId + "--" + t.MinRpId);
}
//2
var ss1 = from r in db.Am_recProScheme
         orderby r.rpId descending
         group r by r.recType into n
         select n;
foreach (var t in ss1)
{
    Response.Write(t.Key + "--" + t.Min(p => p.rpId));
}
//3
var ss2 = db.Am_recProScheme.GroupBy(p => p.recType);
foreach (var t in ss2)
{
    Response.Write(t.Key + "--" + t.Min(p => p.rpId));
}
//4
string sssql = "select recType,min(rpId),max(rpId),sum(rpId) from Am_recProScheme group by recType";
```



#### 10.sql中的In



```c#
//Linq
var ss = from p in db.Am_recProScheme
                  where (new int?[] { 24, 25,26 }).Contains(p.rpId)
                  select p;
foreach (var p in ss)
{
    Response.Write(p.Sorts);
}
//SQL
string st = "select * from Am_recProScheme where rpId in(24,25,26)";
```



#### 11.内连接 INNER JOIN



```c#
//Linq
var ss = from r in db.Am_recProScheme
         join w in db.Am_Test_Result on r.rpId equals w.rsId
         orderby r.rpId descending
         select r;
//Lambda
var ss1 = db.Am_recProScheme.Join(db.Am_Test_Result, p => p.rpId, r => r.rsId, (p, r) => p).OrderByDescending(p => p.rpId).ToList();
//SQL
string sssql = "select r.* from  [Am_recProScheme] as r inner join [dbo].[Am_Test_Result] as t on r.[rpId] = t.[rsId] order by r.[rpId] desc";
```



#### 12.左连接 LEFT JOIN



```c#
//两个DataTable关联，查找只在第一个表中的数据
static void Main(string[] args)
{
    DataTable dt1 = new DataTable("Ta");
    DataTable dt2 = new DataTable("Tb");
    dt1.Columns.Add("ID", typeof(int));
    dt1.Columns.Add("Name", typeof(string));
    dt1.Rows.Add(1, "小明");
    dt1.Rows.Add(2, "小红");
    dt1.Rows.Add(3, "小黑");
    dt2.Columns.Add("ID", typeof(int));
    dt2.Columns.Add("Name", typeof(string));
    dt2.Rows.Add(1, "小黄");
    dt2.Rows.Add(2, "小红");
    dt2.Rows.Add(3, "小强");
    //方法一：Linq
    //var query = from q1 in dt1.AsEnumerable()
    //            join q2 in dt2.AsEnumerable()
    //            on q1.Field<string>("Name") equals q2.Field<string>("Name") into tmp
    //            from q3 in tmp.DefaultIfEmpty()
    //            where q3 == null
    //            select new
    //            {
    //                ID = q1.Field<int>("ID"),
    //                Name = q1.Field<string>("Name")
    //            };
    //方法二：Lambda
    var query = dt1.AsEnumerable().GroupJoin(
        dt2.AsEnumerable(),
        x => x.Field<string>("Name"),
        y => y.Field<string>("Name"),
        (x, y) => y.DefaultIfEmpty(). Where(w => w == null).
        Select(z => new { ID = x.Field<int>("ID"), Name = x.Field<string>("Name") })
        ).SelectMany(x => x);
    foreach (var item in query)
    {
        Console.WriteLine($"ID={item.ID}    Name={item.Name}");
    }    Console.Read();
}
```



![img](https://img2018.cnblogs.com/blog/814410/201903/814410-20190327163706993-1949550596.png)

 三表连接

```c#
SELECT id, name, jname, cname   
        FROM userinfo u   
        LEFT JOIN job j on u.job = j.jid   
        LEFT JOIN city c on u.city = c.cid  
```



```c#
var list = (  
    from u in dc.userinfos  
        join j in dc.jobs on u.job equals j.jid into j_join  
    from x in j_join.DefaultIfEmpty()  
        join c in dc.cities on u.city equals c.cid into c_join  
    from v in c_join.DefaultIfEmpty()  
    select new  
    {  
        id = u.id,  
        name = u.name,  
        jname = x.jname,  
        cname = v.cname,  
        /*u1=u,x1=x,v1=v*/  
        //不要用对象的方式 因为对象可能为null那么对象.属性就会抛异常  
    }  
    ).ToList();  
      
    for (var i = 0; i < list.Count(); i++)  
    {  
        Console.WriteLine(list[i].name + '\t' + list[i].jname + '\t' + list[i].cname); //字段为null不报异常  
        //Console.WriteLine(list[i].u1.name+'\t'+list[i].x1.jname+'\t'+list[i].v1.cname+"\r\n"); //对象x1 v1 有可能为null 抛异常  
    }  
    Console.ReadLine();
```



 

#### 实例用法：



```c#
//数据库 + 自定义名称 =new 数据库
mydbDataContext con = new mydbDataContext();
//模糊查询表达式中用.Contains
con.car.Where(r=>r.name.Contains(TextBox1.Text.Trim())).ToList(）;
//开头查用.StartWith
con.car.Where(r => r.name.StartsWith(TextBox1.Text)).ToList();
//结尾查用.EndWith
con.car.Where(r => r.name.EndsWith(TextBox1.Text)).ToList();
//最大值
con.car.Max(r => r.price * r.oil).ToString();
//最小值
con.car.Min(r => r.price).ToString();
//求和
con.car.Sum(r => r.price).ToString();
//平均值
con.car.Average(r => r.price).ToString();
//升序：
con.car.OrderBy(r => r.price).ToList();
//降序：
con.car.OrderByDescending(r => r.price).ToList();

//上一页，下一页，组合查询：
int PageCount = 5;//每页显示条数
//上一页，PageCount_Label.Text为当前页码
int pageNum = Convert.ToInt32(PageCount_Label.Text) - 1;
Repeater1.DataSource = con.car.Skip((pageNum - 1) * PageCount).Take(PageCount);
Repeater1.DataBind(); 
PageCount_Label.Text = pageNum.ToString();
//下一页
int pageNum = Convert.ToInt32(PageCount_Label.Text) + 1;
Repeater1.DataSource = con.car.Skip((pageNum - 1) * PageCount).Take(PageCount);
Repeater1.DataBind();
PageCount_Label.Text = pageNum.ToString();
//组合查询的点击事件
List<car> list = con.car.ToList();
if (TextBox2.Text != "")
{
    List<car> list1 = con.car.Where(r => r.name.Contains(TextBox2.Text)).ToList();
    list = list.Intersect(list1).ToList();
}
if (TextBox3.Text != "")
{
    List<car> list1 = con.car.Where(r => r.oil == Convert.ToDecimal(TextBox3.Text)).ToList();
    list = list.Intersect(list1).ToList();
}
if (TextBox4.Text != "")
{
    List<car> list1 = con.car.Where(r => r.powers == Convert.ToInt32(TextBox4.Text)).ToList();
    list = list.Intersect(list1).ToList();
}
Repeater1.DataSource = list;
Repeater1.DataBind();
```

