

# NetCore3.1webApi + EFCore + DI 框架封装

## 框架结构

### 实体层：

 **Typecho.Enties** 

### 仓储层：

#### **Typecho.IRepository** 

>   IconcardContext.cs
>
>   IRepositoryFactory.cs
>
>   IRepositorys.cs

#### **Typecho.Repository** 

>  RepositoryFactory.cs
>
>  Repositorys.cs
>
>  typechoContext.cs

### 业务层：

#### **Typecho.IService**

> IBaseService.cs
>
> ITypechoTestService.cs



#### **Typecho.Service**

> BaseService.cs
>
> TypechoService.cs

### UI层（api）：

#### **TypechoCore1**

> TestController.cs

##  一： EF从数据库生成实体类到Enties



### 1.执行以下语句安装依赖包

```c#
Install-Package MySql.Data.EntityFrameworkCore -Pre
Install-Package Pomelo.EntityFrameworkCore.MySql
Install-Package Microsoft.EntityFrameworkCore.Tools
Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design
```



### 2.在程序包包管理器控制台

```c#
Scaffold-DbContext "server=localhost;userid=root;pwd=1;port=3306;database=syerp;sslmode=none;" Pomelo.EntityFrameworkCore.MySql -OutputDir Models -Force
```

> 自动生成所有类模型文件，typechoContext.cs数据库上下文自动创建好了

```
-OutputDir  实体文件所存放的文件目录
-ContextDir   DbContext文件存放的目录
-Context         DbContext文件名
-Schemas       需要生成实体数据的数据表所在的模式
-Tables            需要生成实体数据的数据表的集合
-DataAnnotations
-UseDatabaseNames   直接使用数据库中的表名和列名（某些版本不支持）
-Force              强制执行，重写已经存在的实体文件
```

> 链接： [asp.net core3.1 实战开发（EF+Mysql 从数据库生成实体类到项目）](https://www.cnblogs.com/jiamiemie/p/12683369.html)



##  二：封装数据访问层



> 封装仓储Repositorys模式，把typechoContext.cs这个类复制到Typecho.Repository程序集中



### typechoContext类：



```c#
using Microsoft.EntityFrameworkCore;
using Typecho.Enties.Models;
using Typecho.IRepository;

namespace Typecho.Repository
{
    public partial class typechoContext : DbContext , IconcardContext
    {
      

        public typechoContext(DbContextOptions<typechoContext> options)
            : base(options)
        {
        }

        public virtual DbSet<typecho_comments> typecho_comments { get; set; }
        public virtual DbSet<typecho_contents> typecho_contents { get; set; }
        public virtual DbSet<typecho_fields> typecho_fields { get; set; }
        public virtual DbSet<typecho_links> typecho_links { get; set; }
        public virtual DbSet<typecho_metas> typecho_metas { get; set; }
        public virtual DbSet<typecho_options> typecho_options { get; set; }
        public virtual DbSet<typecho_relationships> typecho_relationships { get; set; }
        public virtual DbSet<typecho_users> typecho_users { get; set; }

          public virtual DbSet<typecho_test> typecho_test { get; set; }

//        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
//        {
//            if (!optionsBuilder.IsConfigured)
//            {
//#warning To protect potentially sensitive information in your connection string, you should move it out of source code. See http://go.microsoft.com/fwlink/?LinkId=723263 for guidance on storing connection strings.
//                optionsBuilder.UseMySql("server=localhost;userid=root;pwd=woshishui;port=3306;database=typecho;sslmode=none", x => x.ServerVersion("8.0.16-mysql"));
//            }
//        }

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<typecho_comments>(entity =>
            {
                entity.HasKey(e => e.coid)
                    .HasName("PRIMARY");

                entity.HasIndex(e => e.cid)
                    .HasName("cid");

                entity.HasIndex(e => e.created)
                    .HasName("created");

                entity.Property(e => e.coid).HasColumnType("int(10) unsigned");

                entity.Property(e => e.agent)
                    .HasColumnType("varchar(200)")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.author)
                    .HasColumnType("varchar(200)")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.authorId)
                    .HasColumnType("int(10) unsigned")
                    .HasDefaultValueSql("'0'");

                entity.Property(e => e.cid)
                    .HasColumnType("int(10) unsigned")
                    .HasDefaultValueSql("'0'");

                entity.Property(e => e.created)
                    .HasColumnType("int(10) unsigned")
                    .HasDefaultValueSql("'0'");

                entity.Property(e => e.ip)
                    .HasColumnType("varchar(64)")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.mail)
                    .HasColumnType("varchar(200)")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.ownerId)
                    .HasColumnType("int(10) unsigned")
                    .HasDefaultValueSql("'0'");

                entity.Property(e => e.parent)
                    .HasColumnType("int(10) unsigned")
                    .HasDefaultValueSql("'0'");

                entity.Property(e => e.stars)
                    .HasColumnType("int(10)")
                    .HasDefaultValueSql("'0'");

                entity.Property(e => e.status)
                    .HasColumnType("varchar(16)")
                    .HasDefaultValueSql("'approved'")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.text)
                    .HasColumnType("text")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.type)
                    .HasColumnType("varchar(16)")
                    .HasDefaultValueSql("'comment'")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.url)
                    .HasColumnType("varchar(200)")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");
            });

            modelBuilder.Entity<typecho_contents>(entity =>
            {
                entity.HasKey(e => e.cid)
                    .HasName("PRIMARY");

                entity.HasIndex(e => e.created)
                    .HasName("created");

                entity.HasIndex(e => e.slug)
                    .HasName("slug")
                    .IsUnique();

                entity.Property(e => e.cid).HasColumnType("int(10) unsigned");

                entity.Property(e => e.allowComment)
                    .HasColumnType("char(1)")
                    .HasDefaultValueSql("'0'")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.allowFeed)
                    .HasColumnType("char(1)")
                    .HasDefaultValueSql("'0'")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.allowPing)
                    .HasColumnType("char(1)")
                    .HasDefaultValueSql("'0'")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.authorId)
                    .HasColumnType("int(10) unsigned")
                    .HasDefaultValueSql("'0'");

                entity.Property(e => e.commentsNum)
                    .HasColumnType("int(10) unsigned")
                    .HasDefaultValueSql("'0'");

                entity.Property(e => e.created)
                    .HasColumnType("int(10) unsigned")
                    .HasDefaultValueSql("'0'");

                entity.Property(e => e.modified)
                    .HasColumnType("int(10) unsigned")
                    .HasDefaultValueSql("'0'");

                entity.Property(e => e.order)
                    .HasColumnType("int(10) unsigned")
                    .HasDefaultValueSql("'0'");

                entity.Property(e => e.parent)
                    .HasColumnType("int(10) unsigned")
                    .HasDefaultValueSql("'0'");

                entity.Property(e => e.password)
                    .HasColumnType("varchar(32)")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.slug)
                    .HasColumnType("varchar(200)")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.status)
                    .HasColumnType("varchar(16)")
                    .HasDefaultValueSql("'publish'")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.template)
                    .HasColumnType("varchar(32)")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.text)
                    .HasColumnType("longtext")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.title)
                    .HasColumnType("varchar(200)")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.type)
                    .HasColumnType("varchar(16)")
                    .HasDefaultValueSql("'post'")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.views)
                    .HasColumnType("int(10)")
                    .HasDefaultValueSql("'0'");
            });

            modelBuilder.Entity<typecho_fields>(entity =>
            {
                entity.HasKey(e => new { e.cid, e.name })
                    .HasName("PRIMARY");

                entity.HasIndex(e => e.float_value)
                    .HasName("float_value");

                entity.HasIndex(e => e.int_value)
                    .HasName("int_value");

                entity.Property(e => e.cid).HasColumnType("int(10) unsigned");

                entity.Property(e => e.name)
                    .HasColumnType("varchar(200)")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.float_value).HasDefaultValueSql("'0'");

                entity.Property(e => e.int_value)
                    .HasColumnType("int(10)")
                    .HasDefaultValueSql("'0'");

                entity.Property(e => e.str_value)
                    .HasColumnType("text")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.type)
                    .HasColumnType("varchar(8)")
                    .HasDefaultValueSql("'str'")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");
            });

            modelBuilder.Entity<typecho_links>(entity =>
            {
                entity.HasKey(e => e.lid)
                    .HasName("PRIMARY");

                entity.Property(e => e.lid)
                    .HasColumnType("int(10) unsigned")
                    .HasComment("links表主键");

                entity.Property(e => e.description)
                    .HasColumnType("varchar(200)")
                    .HasComment("links描述")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.image)
                    .HasColumnType("varchar(200)")
                    .HasComment("links图片")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.name)
                    .HasColumnType("varchar(200)")
                    .HasComment("links名称")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.order)
                    .HasColumnType("int(10) unsigned")
                    .HasDefaultValueSql("'0'")
                    .HasComment("links排序");

                entity.Property(e => e.sort)
                    .HasColumnType("varchar(200)")
                    .HasComment("links分类")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.url)
                    .HasColumnType("varchar(200)")
                    .HasComment("links网址")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.user)
                    .HasColumnType("varchar(200)")
                    .HasComment("自定义")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");
            });

            modelBuilder.Entity<typecho_metas>(entity =>
            {
                entity.HasKey(e => e.mid)
                    .HasName("PRIMARY");

                entity.HasIndex(e => e.slug)
                    .HasName("slug");

                entity.Property(e => e.mid).HasColumnType("int(10) unsigned");

                entity.Property(e => e.count)
                    .HasColumnType("int(10) unsigned")
                    .HasDefaultValueSql("'0'");

                entity.Property(e => e.description)
                    .HasColumnType("varchar(200)")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.name)
                    .HasColumnType("varchar(200)")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.order)
                    .HasColumnType("int(10) unsigned")
                    .HasDefaultValueSql("'0'");

                entity.Property(e => e.parent)
                    .HasColumnType("int(10) unsigned")
                    .HasDefaultValueSql("'0'");

                entity.Property(e => e.slug)
                    .HasColumnType("varchar(200)")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.type)
                    .IsRequired()
                    .HasColumnType("varchar(32)")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");
            });

            modelBuilder.Entity<typecho_options>(entity =>
            {
                entity.HasKey(e => new { e.name, e.user })
                    .HasName("PRIMARY");

                entity.Property(e => e.name)
                    .HasColumnType("varchar(32)")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.user).HasColumnType("int(10) unsigned");

                entity.Property(e => e.value)
                    .HasColumnType("text")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");
            });

            modelBuilder.Entity<typecho_relationships>(entity =>
            {
                entity.HasKey(e => new { e.cid, e.mid })
                    .HasName("PRIMARY");

                entity.Property(e => e.cid).HasColumnType("int(10) unsigned");

                entity.Property(e => e.mid).HasColumnType("int(10) unsigned");
            });

            modelBuilder.Entity<typecho_users>(entity =>
            {
                entity.HasKey(e => e.uid)
                    .HasName("PRIMARY");

                entity.HasIndex(e => e.mail)
                    .HasName("mail")
                    .IsUnique();

                entity.HasIndex(e => e.name)
                    .HasName("name")
                    .IsUnique();

                entity.Property(e => e.uid).HasColumnType("int(10) unsigned");

                entity.Property(e => e.activated)
                    .HasColumnType("int(10) unsigned")
                    .HasDefaultValueSql("'0'");

                entity.Property(e => e.authCode)
                    .HasColumnType("varchar(64)")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.created)
                    .HasColumnType("int(10) unsigned")
                    .HasDefaultValueSql("'0'");

                entity.Property(e => e.group)
                    .HasColumnType("varchar(16)")
                    .HasDefaultValueSql("'visitor'")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.logged)
                    .HasColumnType("int(10) unsigned")
                    .HasDefaultValueSql("'0'");

                entity.Property(e => e.mail)
                    .HasColumnType("varchar(200)")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.name)
                    .HasColumnType("varchar(32)")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.password)
                    .HasColumnType("varchar(64)")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.screenName)
                    .HasColumnType("varchar(32)")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");

                entity.Property(e => e.url)
                    .HasColumnType("varchar(200)")
                    .HasCharSet("utf8")
                    .HasCollation("utf8_general_ci");
            });

            OnModelCreatingPartial(modelBuilder);
        }

        partial void OnModelCreatingPartial(ModelBuilder modelBuilder);
    }
}

```

###  IRepositorys接口：

```c#
 public interface IRepositorys<T> : IDisposable where T : class
    {
        /// <summary>
        /// 显式开启数据上下文事务
        /// </summary>
        /// <param name="isolationLevel">指定连接的事务锁定行为</param>
        void BeginTransaction(IsolationLevel isolationLevel = IsolationLevel.Unspecified);

        /// <summary>
        /// 提交事务的更改
        /// </summary>
        void Commit();

        /// <summary>
        /// 显式回滚事务，仅在显式开启事务后有用
        /// </summary>
        void Rollback();

        /// <summary>
        /// 提交当前单元操作的更改
        /// </summary>
        int SaveChanges();
         Task<int> SaveChangesAsync();

        /// <summary>
        /// 获取 当前实体类型的查询数据集，数据将使用不跟踪变化的方式来查询，当数据用于展现时，推荐使用此数据集，如果用于新增，更新，删除时，请使用<see cref="TrackEntities"/>数据集
        /// </summary>
        IQueryable<T> Entities { get; }

        /// <summary>
        /// 获取 当前实体类型的查询数据集，当数据用于新增，更新，删除时，使用此数据集，如果数据用于展现，推荐使用<see cref="Entities"/>数据集
        /// </summary>
        IQueryable<T> TrackEntities { get; }

        /// <summary>
        /// 插入 - 通过实体对象添加
        /// </summary>
        /// <param name="entity">实体对象</param>
        /// <param name="isSave">是否执行</param>
        /// /// <returns></returns>
        T Add(T entity, bool isSave = true);
         Task<T> AysAdd(T entity, bool isSave = true);
        /// <summary>
        /// 批量插入 - 通过实体对象集合添加
        /// </summary>
        /// <param name="entitys">实体对象集合</param>
        /// <param name="isSave">是否执行</param>
        void AddRange(IEnumerable<T> entitys, bool isSave = true);

        /// <summary>
        /// 删除 - 通过实体对象删除
        /// </summary>
        /// <param name="entity">实体对象</param>
        /// <param name="isSave">是否执行</param>
        void Delete(T entity, bool isSave = true);

        /// <summary>
        /// 批量删除 - 通过实体对象集合删除
        /// </summary>
        /// <param name="entitys">实体对象集合</param>
        /// <param name="isSave">是否执行</param>
        void Delete(bool isSave = false, params T[] entitys);

        /// <summary>
        /// 删除 - 通过主键ID删除
        /// </summary>
        /// <param name="id">主键ID</param>
        Task<int> AsyDelete(object id);
        int Delete(object id);
        /// <summary>
        /// 批量删除 - 通过条件删除
        /// </summary>
        /// <param name="where">过滤条件</param>
        /// <param name="isSave">是否执行</param>
        void Delete(Expression<Func<T, bool>> @where, bool isSave = true);

        /// <summary>
        /// 修改 - 通过实体对象修改
        /// </summary>
        /// <param name="entity">实体对象</param>
        Task<int> AysUpdate(T entity);

        int  Update(T entity);
        /// <summary>
        /// 批量修改 - 通过实体对象集合修改
        /// </summary>
        /// <param name="entitys">实体对象集合</param>
        void Update( params T[] entitys);

        /// <summary>
        /// 是否满足条件
        /// </summary>
        /// <param name="where">过滤条件</param>
        /// <returns></returns>
        bool Any(Expression<Func<T, bool>> @where);

        /// <summary>
        /// 返回总条数
        /// </summary>
        /// <returns></returns>
        int Count();

        /// <summary>
        /// 返回总条数 - 通过条件过滤
        /// </summary>
        /// <param name="where">过滤条件</param>
        /// <returns></returns>
        int Count(Expression<Func<T, bool>> @where);

        /// <summary>
        /// 返回第一条记录
        /// </summary>
        /// <param name="where">过滤条件</param>
        /// <returns></returns>
        T FirstOrDefault(Expression<Func<T, bool>> @where);

        /// <summary>
        /// 返回第一条记录 - 通过条件过滤
        /// </summary>
        /// <typeparam name="TOrder">排序约束</typeparam>
        /// <param name="where">过滤条件</param>
        /// <param name="order">排序条件</param>
        /// <param name="isDesc">排序方式</param>
        /// <returns></returns>
        T FirstOrDefault<TOrder>(Expression<Func<T, bool>> @where, Expression<Func<T, TOrder>> order, bool isDesc = false);

        /// <summary>
        /// 去重查询
        /// </summary>
        /// <param name="where">过滤条件</param>
        /// <returns></returns>
        IQueryable<T> Distinct(Expression<Func<T, bool>> @where);

        /// <summary>
        /// 条件查询
        /// </summary>
        /// <param name="where">过滤条件</param>
        /// <returns></returns>
        IQueryable<T> Where(Expression<Func<T, bool>> @where);

        /// <summary>
        /// 条件查询 - 支持排序
        /// </summary>
        /// <typeparam name="TOrder">排序约束</typeparam>
        /// <param name="where">过滤条件</param>
        /// <param name="order">排序条件</param>
        /// <param name="isDesc">排序方式</param>
        /// <returns></returns>
        IQueryable<T> Where<TOrder>(Expression<Func<T, bool>> @where, Expression<Func<T, TOrder>> order, bool isDesc = false);

        /// <summary>
        /// 条件分页查询 - 支持排序
        /// </summary>
        /// <typeparam name="TOrder">排序约束</typeparam>
        /// <param name="where">过滤条件</param>
        /// <param name="order">排序条件</param>
        /// <param name="pageIndex">当前页码</param>
        /// <param name="pageSize">每页记录条数</param>
        /// <param name="count">返回总条数</param>
        /// <param name="isDesc">是否倒序</param>
        /// <returns></returns>
        IEnumerable<T> Where<TOrder>(Func<T, bool> @where, Func<T, TOrder> order, int pageIndex, int pageSize, out int count, bool isDesc = false);

        /// <summary>
        /// 条件分页查询 - 支持排序 - 支持Select导航属性查询
        /// </summary>
        /// <typeparam name="TOrder">排序约束</typeparam>
        /// <param name="where">过滤条件</param>
        /// <param name="order">排序条件</param>
        /// <param name="pageIndex">当前页码</param>
        /// <param name="pageSize">每页记录条数</param>
        /// <param name="count">返回总条数</param>
        /// <param name="isDesc">是否倒序</param>
        /// <returns></returns>
        IQueryable<T> Where<TOrder>(Expression<Func<T, bool>> @where, Expression<Func<T, TOrder>> order, int pageIndex, int pageSize, out int count, bool isDesc = false);

        /// <summary>
        /// 获取所有数据
        /// </summary>
        /// <returns></returns>
        IQueryable<T> GetAll();

        /// <summary>
        /// 获取所有数据 - 支持排序
        /// </summary>
        /// <typeparam name="TOrder">排序约束</typeparam>
        /// <param name="order">排序条件</param>
        /// <param name="isDesc">排序方式</param>
        /// <returns></returns>
        IQueryable<T> GetAll<TOrder>(Expression<Func<T, TOrder>> order, bool isDesc = false);

        /// <summary>
        /// 根据ID查询
        /// </summary>
        /// <typeparam name="TType">字段类型</typeparam>
        /// <param name="id">主键ID</param>
        /// <returns></returns>
        T GetById<TType>(TType id);

        /// <summary>
        /// 获取最大值
        /// </summary>
        /// <typeparam name="TType">字段类型</typeparam>
        /// <param name="column">字段条件</param>
        /// <returns></returns>
        TType Max<TType>(Expression<Func<T, TType>> column);

        /// <summary>
        /// 获取最大值
        /// </summary>
        /// <typeparam name="TType">字段类型</typeparam>
        /// <param name="column">字段条件</param>
        /// <param name="where">过滤条件</param>
        /// <returns></returns>
        TType Max<TType>(Expression<Func<T, TType>> column, Expression<Func<T, bool>> @where);

        /// <summary>
        /// 获取最小值
        /// </summary>
        /// <typeparam name="TType">字段类型</typeparam>
        /// <param name="column">字段条件</param>
        /// <returns></returns>
        TType Min<TType>(Expression<Func<T, TType>> column);

        /// <summary>
        /// 获取最小值
        /// </summary>
        /// <typeparam name="TType">字段类型</typeparam>
        /// <param name="column">字段条件</param>
        /// <param name="where">过滤条件</param>
        /// <returns></returns>
        TType Min<TType>(Expression<Func<T, TType>> column, Expression<Func<T, bool>> @where);

        /// <summary>
        /// 获取总数
        /// </summary>
        /// <typeparam name="TType">字段类型</typeparam>
        /// <param name="selector">字段条件</param>
        /// <param name="where">过滤条件</param>
        /// <returns></returns>
        TType Sum<TType>(Expression<Func<T, TType>> selector, Expression<Func<T, bool>> @where) where TType : new();
    }
```

### Repositorys类，CRUD功能的封装

```c#
  public class Repositorys<T> : IRepositorys<T> where T : class
    {
        private typechoContext _dbContext;
        private readonly DbSet<T> _dbSet;
        private readonly string _connStr;

        public Repositorys(IconcardContext mydbcontext)
        {
            _dbContext = mydbcontext as typechoContext;
            if (_dbContext == null)
            {
                return;
            }

            _dbSet = _dbContext.Set<T>();
            _connStr = _dbContext.Database.GetDbConnection().ConnectionString;
        }

        public void BeginTransaction(IsolationLevel isolationLevel = IsolationLevel.Unspecified)
        {
            if (_dbContext.Database.CurrentTransaction == null)
            {
                _dbContext.Database.BeginTransaction(isolationLevel);
            }
        }

        public void Commit()
        {
            var transaction = this._dbContext.Database.CurrentTransaction;
            if (transaction != null)
            {
                try
                {
                    transaction.Commit();
                }
                catch (Exception)
                {
                    transaction.Rollback();
                    throw;
                }
            }
        }

        public void Rollback()
        {
            if (_dbContext.Database.CurrentTransaction != null)
            {
                _dbContext.Database.CurrentTransaction.Rollback();
            }
        }

        public int SaveChanges()
        {
            return _dbContext.SaveChanges();
        }
          public async Task<int> SaveChangesAsync()
        {
            return await _dbContext.SaveChangesAsync();
        }


        public IQueryable<T> Entities
        {
            get { return _dbSet.AsNoTracking(); }
        }

        public IQueryable<T> TrackEntities
        {
            get { return _dbSet; }
        }

        public T Add(T entity, bool isSave = true)
        {

            _dbSet.Add(entity);
            if (isSave)
            {
                SaveChanges();
            }
            return entity;
        }

         public async Task<T> AysAdd(T entity, bool isSave = true)
        {
           await _dbSet.AddAsync(entity);
            if (isSave)
            {
               await  SaveChangesAsync();
            }
            return entity;
        }

        public void AddRange(IEnumerable<T> entitys, bool isSave = true)
        {
            _dbSet.AddRange(entitys);
            if (isSave)
            {
                SaveChanges();
            }
        }

        public void Delete(T entity, bool isSave = true)
        {
            this._dbSet.Remove(entity);
            if (isSave)
            {
                this.SaveChanges();
            }
        }

        public void Delete(bool isSave = true, params T[] entitys)
        {
            this._dbSet.RemoveRange(entitys);
            if (isSave)
            {
                this.SaveChanges();
            }
        }

        public async Task<int> AsyDelete(object id)
        {
             int de = 0;
             //执行查询
             var todoItem = await _dbSet.FindAsync(id);
            if (todoItem == null)
            {
                //return NotFound();
                de = 0;
            }
            else
            {
              _dbSet.Remove(todoItem);
             de = SaveChanges();
            }
            return de;
        }

           public int Delete(object id)
        {
             int de = 0;
             //执行查询
             var todoItem =_dbSet.Find(id);
            if (todoItem == null)
            {
                //return NotFound();
                de = 0;
            }
            else
            {
              _dbSet.Remove(todoItem);
             de = SaveChanges();
            }
            return de;
        }

        public void Delete(Expression<Func<T, bool>> @where, bool isSave = true)
        {
            T[] entitys = this._dbSet.Where(@where).ToArray();
            if (entitys.Length > 0)
            {
                this._dbSet.RemoveRange(entitys);
            }
            if (isSave)
            {
                this.SaveChanges();
            }
        }

        public async Task<int> AysUpdate(T entity)
        {
            
            var entry = this._dbContext.Entry(entity);
            if (entry.State == EntityState.Detached)
            {
                entry.State = EntityState.Modified;
            }
            var da = await Task.Run(SaveChangesAsync);
            return da;
        }

        public int Update(T entity)
        {
            
            var entry = this._dbContext.Entry(entity);
            if (entry.State == EntityState.Detached)
            {
                entry.State = EntityState.Modified;
            }
            var da = SaveChanges();
            return da;
        }
        public void Update( params T[] entitys)
        {
            var entry = this._dbContext.Entry(entitys);
            if (entry.State == EntityState.Detached)
            {
                entry.State = EntityState.Modified;
            }
            SaveChanges();
        }

        public bool Any(Expression<Func<T, bool>> @where)
        {
            return this._dbSet.AsNoTracking().Any(@where);
        }

        public int Count()
        {
            return this._dbSet.AsNoTracking().Count();
        }

        public int Count(Expression<Func<T, bool>> @where)
        {
            return this._dbSet.AsNoTracking().Count(@where);
        }

        public T FirstOrDefault(Expression<Func<T, bool>> @where)
        {
            return this._dbSet.AsNoTracking().FirstOrDefault(@where);
        }

        public T FirstOrDefault<TOrder>(Expression<Func<T, bool>> @where, Expression<Func<T, TOrder>> order, bool isDesc = false)
        {
            if (isDesc)
            {
                return this._dbSet.AsNoTracking().OrderByDescending(order).FirstOrDefault(@where);
            }
            else
            {
                return this._dbSet.AsNoTracking().OrderBy(order).FirstOrDefault(@where);
            }
        }

        public IQueryable<T> Distinct(Expression<Func<T, bool>> @where)
        {
            return this._dbSet.AsNoTracking().Where(@where).Distinct();
        }

        public IQueryable<T> Where(Expression<Func<T, bool>> @where)
        {
            return this._dbSet.Where(@where);
        }

        public IQueryable<T> Where<TOrder>(Expression<Func<T, bool>> @where, Expression<Func<T, TOrder>> order, bool isDesc = false)
        {
            if (isDesc)
            {
                return this._dbSet.Where(@where).OrderByDescending(order);
            }
            else
            {
                return this._dbSet.Where(@where).OrderBy(order);
            }
        }

        public IEnumerable<T> Where<TOrder>(Func<T, bool> @where, Func<T, TOrder> order, int pageIndex, int pageSize, out int count, bool isDesc = false)
        {
            count = Count();
            if (isDesc)
            {
                return this._dbSet.Where(@where).OrderByDescending(order).Skip((pageIndex - 1) * pageSize).Take(pageSize);
            }
            else
            {
                return this._dbSet.Where(@where).OrderBy(order).Skip((pageIndex - 1) * pageSize).Take(pageSize);
            }
        }

        public IQueryable<T> Where<TOrder>(Expression<Func<T, bool>> @where, Expression<Func<T, TOrder>> order, int pageIndex, int pageSize, out int count, bool isDesc = false)
        {
            count = Count();
            if (isDesc)
            {
                return this._dbSet.Where(@where).OrderByDescending(order).Skip((pageIndex - 1) * pageSize).Take(pageSize);
            }
            else
            {
                return this._dbSet.Where(@where).OrderBy(order).Skip((pageIndex - 1) * pageSize).Take(pageSize);
            }
        }

        public IQueryable<T> GetAll()
        {
            return this._dbSet.AsNoTracking();
        }

        public IQueryable<T> GetAll<TOrder>(Expression<Func<T, TOrder>> order, bool isDesc = false)
        {
            if (isDesc)
            {
                return this._dbSet.AsNoTracking().OrderByDescending(order);
            }
            else
            {
                return this._dbSet.AsNoTracking().OrderBy(order);
            }
        }

        public T GetById<TType>(TType id)
        {
            return this._dbSet.Find(id);
        }

        public TType Max<TType>(Expression<Func<T, TType>> column)
        {
            if (this._dbSet.AsNoTracking().Any())
            {
                return this._dbSet.AsNoTracking().Max(column);
            }
            return default(TType);
        }

        public TType Max<TType>(Expression<Func<T, TType>> column, Expression<Func<T, bool>> @where)
        {
            if (this._dbSet.AsNoTracking().Any(@where))
            {
                return this._dbSet.AsNoTracking().Where(@where).Max(column);
            }
            return default(TType);
        }

        public TType Min<TType>(Expression<Func<T, TType>> column)
        {
            if (this._dbSet.AsNoTracking().Any())
            {
                return this._dbSet.AsNoTracking().Min(column);
            }
            return default(TType);
        }

        public TType Min<TType>(Expression<Func<T, TType>> column, Expression<Func<T, bool>> @where)
        {
            if (this._dbSet.AsNoTracking().Any(@where))
            {
                return this._dbSet.AsNoTracking().Where(@where).Min(column);
            }
            return default(TType);
        }

        public TType Sum<TType>(Expression<Func<T, TType>> selector, Expression<Func<T, bool>> @where) where TType : new()
        {
            object result = 0;

            if (new TType().GetType() == typeof(decimal))
            {
                result = this._dbSet.AsNoTracking().Where(where).Sum(selector as Expression<Func<T, decimal>>);
            }
            if (new TType().GetType() == typeof(decimal?))
            {
                result = this._dbSet.AsNoTracking().Where(where).Sum(selector as Expression<Func<T, decimal?>>);
            }
            if (new TType().GetType() == typeof(double))
            {
                result = this._dbSet.AsNoTracking().Where(where).Sum(selector as Expression<Func<T, double>>);
            }
            if (new TType().GetType() == typeof(double?))
            {
                result = this._dbSet.AsNoTracking().Where(where).Sum(selector as Expression<Func<T, double?>>);
            }
            if (new TType().GetType() == typeof(float))
            {
                result = this._dbSet.AsNoTracking().Where(where).Sum(selector as Expression<Func<T, float>>);
            }
            if (new TType().GetType() == typeof(float?))
            {
                result = this._dbSet.AsNoTracking().Where(where).Sum(selector as Expression<Func<T, float?>>);
            }
            if (new TType().GetType() == typeof(int))
            {
                result = this._dbSet.AsNoTracking().Where(where).Sum(selector as Expression<Func<T, int>>);
            }
            if (new TType().GetType() == typeof(int?))
            {
                result = this._dbSet.AsNoTracking().Where(where).Sum(selector as Expression<Func<T, int?>>);
            }
            if (new TType().GetType() == typeof(long))
            {
                result = this._dbSet.AsNoTracking().Where(where).Sum(selector as Expression<Func<T, long>>);
            }
            if (new TType().GetType() == typeof(long?))
            {
                result = this._dbSet.AsNoTracking().Where(where).Sum(selector as Expression<Func<T, long?>>);
            }
            return (TType)result;
        }

        public void Dispose()
        {
            this._dbContext.Dispose();
        }
    }
```



## 三： 通过DI创建实例

> 添加RepositoryFactory类和IRepositoryFactory接口



### IRepositoryFactory接口：

```c#
   public interface IRepositoryFactory
    {
        IRepositorys<T> CreateRepository<T>(IconcardContext mydbcontext) where T : class;
    }
```



### RepositoryFactory类：

```c#
   public class RepositoryFactory : IRepositoryFactory
    {
        public IRepositorys<T> CreateRepository<T>(IconcardContext mydbcontext) where T : class
        {
            return new Repositorys<T>(mydbcontext);
        }
    }
```



## 四：Service层

> 添加BaseService类和IBaseService接口



### IBaseService接口：

```c#
 public interface IBaseService
    {
        IRepositorys<T> CreateService<T>() where T : class, new();
    }
```



### BaseService类：

```c#
 public class BaseService : IBaseService
    {
        private IRepositoryFactory _repositoryFactory;
        private IconcardContext _mydbcontext;
        public BaseService(IRepositoryFactory repositoryFactory, IconcardContext mydbcontext)
        {
            this._repositoryFactory = repositoryFactory;
            this._mydbcontext = mydbcontext;
        }

        public IRepositorys<T> CreateService<T>() where T : class, new()
        {
            return _repositoryFactory.CreateRepository<T>(_mydbcontext);
        }
    }
```

> 添加Service模块xxxService类和IxxxService接口，xxxService类继承父类BaseService，生成构造函数。

```c#
//构造函数 自动生成
public TypechoService(IRepositoryFactory repositoryFactory, IconcardContext mydbcontext) : base(repositoryFactory, mydbcontext)
        {
        }
```



## 五：DI依赖注入配置



### 注册DbContext

```c#
         services.AddDbContext<typechoContext>(options=>options.UseMySql(Configuration.GetConnectionString("DefaultConnection")));


\\appsettings.json
     "ConnectionStrings": {
    "DefaultConnection": "Server=localhost;database=typecho;uid=root;pwd=woshishui;"
  }
```

```c#
services.AddScoped<IconcardContext, typechoContext>();//dbc
            services.AddScoped<IRepositoryFactory, RepositoryFactory>();//泛型工厂
            services.AddScoped<ITypechoTestService, TypechoService>();//ioc
```



## 六：UI层调用service接口

> TestController ：实现调用仓储封装增删改查

```c#
 [Route("api/[controller]")]
    [ApiController]
    public class TestController : Controller
    {
          private readonly typechoContext _coreDbContext;
          private readonly ITokenHelper _tokenHelper = null;
         // ITypechoTestService service = new TypechoService();
          private readonly ITypechoTestService _service; //IOC依赖注入
      

        public TestController(typechoContext coreDbContext,ITokenHelper tokenHelper,ITypechoTestService service)
        {
            _coreDbContext = coreDbContext;
            _tokenHelper = tokenHelper;
            _service=service;
        }
        /// <summary>
        /// 验证Token
        /// </summary>
        /// <param name="tokenStr">token</param>
        /// <returns></returns>
        [HttpGet("ValiToken")]
        public ReturnModel ValiToken(string tokenStr)
        {
            var ret = new ReturnModel
            {
                TnToken = new TnToken()
            };
            bool isvilidate = _tokenHelper.ValiToken(tokenStr);
            if(isvilidate)
            {
                ret.Code = 200;
                ret.Msg = "Token验证成功";
                ret.TnToken.TokenStr = tokenStr;
            }
            else
            {
                ret.Code = 500;
                ret.Msg = "Token验证失败";
                ret.TnToken.TokenStr = tokenStr;
            }
            return ret;
        }
        /// <summary>
        /// 验证Token 带返回状态
        /// </summary>
        /// <param name="tokenStr"></param>
        /// <returns></returns>
        [HttpGet("ValiTokenState")]
        public ReturnModel ValiTokenState(string tokenStr)
        {
            var ret = new ReturnModel
            {
                TnToken = new TnToken()
            };
            string loginID = "";
            TokenType tokenType = _tokenHelper.ValiTokenState(tokenStr, a => a["iss"] == "WYY" && a["aud"] == "EveryTestOne", action => { loginID = action["loginID"]; });
            if (tokenType == TokenType.Fail)
            {
                ret.Code = 202;
                ret.Msg = "token验证失败";
                return ret;
            }
            if (tokenType == TokenType.Expired)
            {
                ret.Code = 205;
                ret.Msg = "token已经过期";
                return ret;
            }

            //..............其他逻辑
            var data = new List<Dictionary<string, string>>();
            var bb = new Dictionary<string, string>
            {
                { "Wyy", "123456" }
            };
            data.Add(bb);
            ret.Code = 200;
            ret.Msg = "访问成功!";
            ret.Data =data ;
            return ret;
        }

        /// <summary>
        /// 登录测试
        /// </summary>
        /// <param name="user"></param>
        /// <returns></returns>
         [HttpPost("Login")]
        public IActionResult Login([FromBody]LoginInput user)
        {
            var ret = new ReturnModel();
            try
            {
                if (string.IsNullOrWhiteSpace(user.Username) || string.IsNullOrWhiteSpace(user.Password))
                {
                    ret.Code = 201;
                    ret.Msg = "用户名密码不能为空";
                    return NotFound();
                }
                //登录操作 我就没写了 || 假设登录成功
                if (true)
                {
                    Dictionary<string, string> keyValuePairs = new Dictionary<string, string>
                    {
                        { "loginID", user.Username }
                    };
                    ret.Code = 200;
                    ret.Msg = "登录成功";
                    ret.TnToken= _tokenHelper.CreateToken(keyValuePairs);
                }
            }
            catch(Exception ex)
            {
                ret.Code = 500;
                ret.Msg = "登录失败:"+ex.Message;
            }
            return Ok(ret);
        }

        /// <summary>
        /// 异步查询
        /// [ApiExplorerSettings(IgnoreApi = true)] 隐藏接口
        /// </summary>
        /// <returns></returns>
        [ServiceFilter(typeof(TokenFilter))]
        [HttpGet("AsyGetTest")]
        public async Task<IActionResult> AsyGetTest(string token)
        {
          return Ok(await _service.AsyGetTest());
        }

         /// <summary>
        /// 同步查询
        /// </summary>
        /// <returns></returns>
        [HttpGet("GetTest")]
        public IActionResult GetTest()
        {
          return Ok(_service.GetTest());
         }

         /// <summary>
         /// 条件查询
         /// </summary>
         /// <returns></returns>
         [HttpGet("AsyGetTestName")]
        public async Task<IActionResult> AsyGetTestName(int id)
        {
            return Ok(await _service.AsyGetTestName(id));
        }

         /// <summary>
        /// 过滤查询
        /// </summary>
        /// <returns></returns>
        [HttpGet("AsyGetTestG")]
        public async Task<IActionResult> AsyGetTestG(string name )
        {
              var courses = _coreDbContext.typecho_test
                  .Where(w => w.name.Contains(name));
            return Ok( await courses.FirstAsync());
        }

        /// <summary>
        /// 模糊查询
        /// </summary>
        /// <returns></returns>
        [HttpGet("AsyGetTestLink")]
        public async Task<IActionResult> AsyGetTestLink(string name )
        {
              var courses = _coreDbContext.typecho_test
                  .Where(w=>EF.Functions.Like(w.name,"%"));
            return Ok( await courses.ToListAsync());
        }


        /// <summary>
        /// 异步删除数据
        /// </summary>
        /// <param name="id"></param>
        /// <returns></returns>
        [HttpDelete("AsyDetTestId")]
        public async Task<IActionResult> AsyDetTestId(int id)
        {
          return Ok(await _service.AsyDetTestId(id));
        }

        /// <summary>
        /// 同步删除数据
        /// </summary>
        /// <param name="id"></param>
        /// <returns></returns>
        [HttpDelete("DetTestId")]
        public  IActionResult DetTestId(int id)
        {
          return Ok( _service.DetTestId(id));
        }
        
        /// <summary>
        /// 异步添加数据
        /// </summary>
        /// <returns></returns>
        [HttpPost("AsyIntTest")]
        public async Task<ActionResult<typecho_test>> AsyIntTest(typecho_test test)
        {
            return Ok(await _service.AsyIntTest(test));
        }
          
        /// <summary>
        /// 同步添加数据
        /// </summary>
        /// <returns></returns>
        [HttpPost("IntTest")]
        public  ActionResult<typecho_test> IntTest(typecho_test test)
        {
            return Ok(_service.IntTest(test));
        }

        /// <summary>
        /// 异步更新数据
        /// </summary>
        /// <param name="test"></param>
        /// <returns></returns>
        [HttpPut("AysUpTest")]
        public async Task<IActionResult> AysUpTest(typecho_test test)
        {
           var data=await Task.Run(()=> _service.AysUpTest(test));
           return Ok(data);
        }

        /// <summary>
        /// 同步更新数据
        /// </summary>
        /// <param name="id"></param>
        /// <param name="test"></param>
        /// <returns></returns>
        [HttpPut("UpTest")]
        public  IActionResult UpTest(int id, typecho_test test)
        { 
            var data= _service.UpTest(test);
           return Ok(data);
        }
    }
```





## 参考：[.Net Core2.2 + EF Core + DI，三层框架项目搭建教程](https://www.cnblogs.com/han1982/p/11058788.html)