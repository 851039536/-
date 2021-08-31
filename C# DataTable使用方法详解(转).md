# C# DataTable使用方法详解

在项目中常常常使用到DataTable,假设DataTable使用得当，不仅能使程序简洁有用，并且可以提高性能，达到事半功倍的效果，现对DataTable的使用技巧进行一下总结。

## **1、添加引用**

```
using System.Data;
```

## **2、创建表**

```c++
//创建一个空表
DataTable dt = new DataTable();
//创建一个名为"Table_New"的空表
DataTable dt = new DataTable("Table_New");
```

## **3、创建列**

```c#
//1.创建空列
DataColumn dc = ``new` `DataColumn();
dt.Columns.Add(dc);
//2.创建带列名和类型名的列(两种方式任选其一)
dt.Columns.Add("column0", System.Type.GetType("System.String"));
dt.Columns.Add("column0", typeof(String));
//3.通过列架构添加列
DataColumn dc = new DataColumn("column1",System.Type.GetType("System.DateTime"));
DataColumn dc = new DataColumn("column1", typeof(DateTime));
dt.Columns.Add(dc);
```

## **4、创建行**

```c#
//1.创建空行
DataRow dr = dt.NewRow();
dt.Rows.Add(dr);
//2.创建空行
dt.Rows.Add();
//3.通过行框架创建并赋值
dt.Rows.Add("张三",DateTime.Now);//Add里面参数的数据顺序要和dt中的列的顺序对应 
//4.通过复制dt2表的某一行来创建
dt.Rows.Add(dt2.Rows[i].ItemArray);
```

## **5、赋值和取值**

```c#
//新建行的赋值
DataRow dr = dt.NewRow();
dr[0] = "张三";//通过索引赋值
dr["column1"] = DateTime.Now; //通过名称赋值
//对表已有行进行赋值
dt.Rows[0][0] = "张三"; //通过索引赋值
dt.Rows[0]["column1"] = DateTime.Now;//通过名称赋值
//取值
string name=dt.Rows[0][0].ToString();
string time=dt.Rows[0]["column1"].ToString();
```

## **6、筛选行**

```
//选择column1列值为空的行的集合
DataRow[] drs = dt.Select(``"column1 is null"``);
//选择column0列值为"李四"的行的集合
DataRow[] drs = dt.Select(``"column0 = '李四'"``);
//筛选column0列值中有"张"的行的集合(模糊查询)
DataRow[] drs = dt.Select(``"column0 like '张%'"``);``//如果的多条件筛选，可以加 and 或 or
//筛选column0列值中有"张"的行的集合并按column1降序排序
DataRow[] drs = dt.Select(``"column0 like '张%'"``, ``"column1 DESC"``);
```

## **7、删除行**

```c#
//使用DataTable.Rows.Remove(DataRow)方法
dt.Rows.Remove(dt.Rows[0]);
//使用DataTable.Rows.RemoveAt(index)方法
dt.Rows.RemoveAt(0);
//使用DataRow.Delete()方法
dt.Row[0].Delete();
dt.AcceptChanges();
```

 

```c#
//-----区别和注意点-----
//Remove()和RemoveAt()方法是直接删除
//Delete()方法只是将该行标记为deleted，但是还存在，还可DataTable.RejectChanges()回滚，使该行取消删除。
//用Rows.Count来获取行数时，还是删除之前的行数，需要使用DataTable.AcceptChanges()方法来提交修改。
//如果要删除DataTable中的多行，应该采用倒序循环DataTable.Rows，而且不能用foreach进行循环删除，因为正序删除时索引会发生变化，程式发生异常，很难预料后果。
for (int i = dt.Rows.Count - 1; i >= 0; i--)
{
　　dt.Rows.RemoveAt(i);
}
```

## **8、复制表**

```c#
//复制表，同时复制了表结构和表中的数据
DataTable dtNew = new DataTable();
dtNew = dt.Copy();
//复制表
DataTable dtNew = dt.Copy(); //复制dt表数据结构
dtNew.Clear() //清空数据
for(int i = 0; i < dt.Rows.Count; i++)
{
 if(条件语句)
 {
   dtNew.Rows.Add(dt.Rows[i].ItemArray); //添加数据行
 }
}
//克隆表，只是复制了表结构，不包括数据
DataTable dtNew = new DataTable();
dtNew = dt.Clone();
//如果只需要某个表中的某一行
DataTable dtNew = new DataTable();
dtNew = dt.Copy();
dtNew.Rows.Clear();//清空表数据
dtNew.ImportRow(dt.Rows[0]);//这是加入的是第一行
```

## **9、表排序**

```c#
DataTable dt = new DataTable();//创建表
dt.Columns.Add("ID", typeof(Int32));//添加列
dt.Columns.Add("Name", typeof(String));
dt.Columns.Add("Age", typeof(Int32));
dt.Rows.Add(new object[] { 1, "张三",20});//添加行
dt.Rows.Add(new object[] { 2, "李四",25});
dt.Rows.Add(new object[] { 3, "王五",30});
DataView dv = dt.DefaultView;//获取表视图
dv.Sort = "ID DESC";//按照ID倒序排序
dv.ToTable();//转为表
```



转自https://www.cnblogs.com/qianqian528/p/8456351.html