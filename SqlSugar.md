# SqlSugar

[官方文档：http://www.codeisbug.com/Doc/8]()



## **Nuget 安装**

SqlSugar是.NET版本，SqlSugarCore是.NET CORE版本，根据你的项目选择你的引用dll

`Install-Package sqlSugar `

`Install-Package sqlSugarCore`

**优点**

很多人都会问您这款ORM有哪些特点,有什么竞争力，我归纳出以上几点供你参考

1、高性能 ,不夸张的说，去掉Sql在数据库执行的时间，SqlSugar是EF数倍性能，另外在批量操作和一对多查询上也有不错的SQL优化

2、高扩展性 ，支持自定义拉姆达函数解析、扩展数据类型、支持自定义实体特性，外部缓存等

## **定义DbContext**

```c#
  public DbContext()
        {
            Db = new SqlSugarClient(new ConnectionConfig()
            {
                ConnectionString = "server=localhost;uid=root;pwd=woshishui;database=test",
                DbType = DbType.MySql,
                InitKeyType = InitKeyType.Attribute,//从特性读取主键和自增列信息
                IsAutoCloseConnection = true,//开启自动释放模式和EF原理一样我就不多解释了

            });
            //调式代码 用来打印SQL 
            Db.Aop.OnLogExecuting = (sql, pars) =>
            {
                Console.WriteLine(sql + "\r\n" +
                    Db.Utilities.SerializeObject(pars.ToDictionary(it => it.ParameterName, it => it.Value)));
                Console.WriteLine();
            };

        }
        //注意：不能写成静态的，不能写成静态的
        public SqlSugarClient Db;//用来处理事务多表查询和复杂的操作
        public SimpleClient<User> Userdb { get { return new SimpleClient<User>(Db); } }//用来处理Student表的常用操作
       // public SimpleClient<School> SchoolDb { get { return new SimpleClient<School>(Db); } }//用来处理School表的常用操作
```

## **使用DbContext完成增删查改**

DemoManager

```c#
  public class DemoManager : DbContext//继承DbContext
    {
        public void query()
        {
          
            var data2 = Userdb.GetList();//查询所有
            var data1 = Userdb.GetById(1);//根据ID查询
        }

        public void SearchDemo()
        {

          
            var data2 = Userdb.GetList();//查询所有
            var data3 = Userdb.GetList(it => it.id == 1);  //根据条件查询  
            var data4 = Userdb.GetSingle(it => it.id == 1);//根据条件查询一条

            var p = new PageModel() { PageIndex = 1, PageSize = 2 };// 分页查询
            var data5 = Userdb.GetPageList(it => it.用户 == "xx", p);
            Console.Write(p.PageCount);//返回总数


            // 分页查询加排序
            var data6 = Userdb.GetPageList(it => it.用户 == "xx", p, it => it.用户, OrderByType.Asc);
            Console.Write(p.PageCount);//返回总数


            //组装条件查询作为条件实现 分页查询加排序
            List<IConditionalModel> conModels = new List<IConditionalModel>();
            conModels.Add(new ConditionalModel() { FieldName = "id", ConditionalType = ConditionalType.Equal, FieldValue = "1" });//id=1
            var data7 = Userdb.GetPageList(conModels, p, it => it.用户, OrderByType.Asc);

            //4.9.7.5支持了转换成queryable,我们可以用queryable实现复杂功能
            Userdb.AsQueryable().Where(x => x.id == 1).ToList();
        }


        //插入例子
        public void InsertDemo()
        {

            var student = new User() { 用户 = "jack" };
            var studentArray = new User[] { student };

            Userdb.Insert(student);//插入

            Userdb.InsertRange(studentArray);//批量插入

            var id = Userdb.InsertReturnIdentity(student);//插入返回自增列

            //4.9.7.5我们可以转成 Insertable实现复杂插入
           // Userdb.AsInsertable(insertObj).ExecuteCommand();
        }


        //更新例子
        public void UpdateDemo()
        {
            var student = new User() { id = 1, 用户 = "jack" };
            var studentArray = new User[] { student };

            Userdb.Update(student);//根据实体更新

            Userdb.UpdateRange(studentArray);//批量更新

           // Userdb.Update(it => new User() { 用户 = "a", CreateTime = DateTime.Now }, it => it.Id == 1);// 只更新Name列和CreateTime列，其它列不更新，条件id=1

            //支持Userdb.AsUpdateable(student)
        }

        //删除例子
        public void DeleteDemo()
        {
            var student = new User() { id = 1, 用户 = "jack" };

            Userdb.Delete(student);//根据实体删除
            Userdb.DeleteById(1);//根据主键删除
            Userdb.DeleteById(new int[] { 1, 2 });//根据主键数组删除
            Userdb.Delete(it => it.id == 1);//根据条件删除

            //支持Userdb.AsDeleteable()
        }

        //使用事务的例子
        public void TranDemo()
        {

            var result = Db.Ado.UseTran(() =>
            {
                //这里写你的逻辑
            });
            if (result.IsSuccess)
            {
                //成功
            }
            else
            {
                Console.WriteLine(result.ErrorMessage);
            }
        }

        //多表查询
        public void JoinDemo()
        {

            var list = Db.Queryable<User, User>((st, sc) => new object[] {
            JoinType.Left,
            st.用户==sc.用户
        }).Select<User>().ToList();
        }
    }
```

## 实体类

```c#
 [SugarTable("user")]
    public class User
    {
        //指定主键和自增列，当然数据库中也要设置主键和自增列才会有效
        [SugarColumn(IsPrimaryKey = true, IsIdentity = true)]
        public int id { get; set; }
        public string 用户 { get; set; }
        public string 密码 { get; set; }
        public int 权限 { get; set; }
    }
```

##  使用

 `DemoManager demo = new DemoManager();`

` demo.query();`