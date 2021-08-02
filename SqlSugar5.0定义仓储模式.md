# SqlSugar5.0定义仓储模式

### 仓储说明

仓储可以让你的方法更加的规范，需要什么方法都封装到仓储中，下次就能重复使用，并且能很好的和你业务拆分开

 这种设计模式简单粗暴用起来也方便 

引用：[](http://www.donet5.com/Home/Doc?typeId=1228)

### 创建仓储

定义 After.Repository 类库

定义的Repository是公用类

```c#
using After.Generic;
using SqlSugar;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace After.Repository.Repository
{
    public class Repository<T> : SimpleClient<T> where T : class, new()
    {
        public Repository(ISqlSugarClient context = null) : base(context)//注意这里要有默认值等于null
        {
            if (context == null)
            {
                string[] sqlText = Type2.SqlText();
                base.Context = new SqlSugarClient(new ConnectionConfig()
                {
                    //DbType = SqlSugar.DbType.SqlServer,
                    DbType = (DbType)Convert.ToSByte(sqlText[1]),
                    InitKeyType = InitKeyType.Attribute,
                    IsAutoCloseConnection = true,
                    ConnectionString = sqlText[0]
                });
            }
        }

        /// <summary>
        /// 扩展方法，自带方法不能满足的时候可以添加新方法
        /// </summary>
        /// <returns></returns>
        public List<T> CommQuery(string json)
        {
            //base.Context.Queryable<T>().ToList();可以拿到SqlSugarClient 做复杂操作
            return null;
        }

    }
}

```

### 定义IService Service类库

**IConfigService.cs**

```c#
using After.Model;
using System.Collections.Generic;
using System.Threading.Tasks;

namespace After.IService.IService
{
    public interface IConfigService
    {
        Task<List<config>> GetAllAsync();
    }
}

```

**ConfigService.cs**

```c#
using After.IService.IService;
using After.Model;
using After.Repository.Repository;
using System.Collections.Generic;
using System.Threading.Tasks;

namespace After.Service
{
    public class ConfigService : Repository<config>,IConfigService
    {
        public ConfigService()
        {
        }
        /// <summary>
        /// 查询全部
        /// </summary>
        /// <returns></returns>

        public async Task<List<config>> GetAllAsync()
        {
           return await base.GetListAsync();
        }
    }
}

```

### 使用

```c#
//创建实例
ConfigService configService = new ConfigService();

	       var date = configService.GetAllAsync();
            foreach (var item in date.Result)
            {
				MessageBox.Show(item.ConfigText);
            }
```

### 仓储自带方法

```c#
//查询
var data1 = base.GetById(1);//根据id查询
var data2 = base.GetList();//查询所有
var data3 = base.GetList(it => it.Id == 1); //根据条件查询
var data4 = base.GetSingle(it => it.Id == 1);
var p = new PageModel() { PageIndex = 1, PageSize = 2 };
var data5 = base.GetPageList(it => it.Name == "xx", p);
Console.Write(p.PageCount);
var data6 = base.GetPageList(it => it.Name == "xx", p, it => it.Name, OrderByType.Asc);
Console.Write(p.PageCount);
List<IConditionalModel> conModels = new List<IConditionalModel>();
conModels.Add(new ConditionalModel(){FieldName="id",ConditionalType=ConditionalType.Equal,FieldValue="1"});//id=1
var data7 = base.GetPageList(conModels, p, it => it.Name, OrderByType.Asc);
base.AsQueryable().Where(x => x.Id == 1).ToList();
 
//插入
base.Insert(insertObj);
base.InsertRange(InsertObjs);
var id = base.InsertReturnIdentity(insertObj);
base.AsInsertable(insertObj).ExecuteCommand();
 
 
//删除
base.Delete(insertObj);
base.DeleteById(1);
base.DeleteByIds(new object [] { 1, 2 }); //数组带是 ids方法 ，封装传 object [] 类型
base.Delete(it => it.Id == 1);
base.AsDeleteable().Where(it => it.Id == 1).ExecuteCommand();
 
//更新
base.Update(insertObj); 
base.UpdateRange(InsertObjs); 
base.Update(it => new Order() { Name = "a", }, it => it.Id == 1);
base.AsUpdateable(insertObj).UpdateColumns(it=>new { it.Name }).ExecuteCommand();
```

