# netCore自定义接口依赖注入DI

### 定义接口

**ICacheUtil.cs**

```c#
namespace Snblog.Cache.CacheUtil
{
    public interface ICacheUtil
    {
        /// <summary>
        /// 设置并返回缓存值(值类型)
        /// </summary>
        /// <typeparam name="T">返回类型</typeparam>
        /// <param name="key">缓存键值</param>
        /// <param name="value">要缓存的值</param>
        /// <returns>result</returns>
        public T CacheNumber<T>(string key, T value);
        /// <summary>
        /// 设置并返回缓存值(字符串)
        /// </summary>
        /// <typeparam name="T">返回类型</typeparam>
        /// <param name="key">缓存键值</param>
        /// <param name="value">要缓存的值</param>
        public T CacheString<T>(string key, T value);
    }
}

```

### 继承接口

```c#
using Snblog.Cache.Cache;

namespace Snblog.Cache.CacheUtil
{
    public class CacheUtil:ICacheUtil
    {

        //创建内存缓存对象
        private static CacheManager _cache = new CacheManager();
        /// <summary>
        /// 设置并返回缓存值(值类型)
        /// </summary>
        /// <typeparam name="T">返回类型</typeparam>
        /// <param name="key">缓存键值</param>
        /// <param name="value">要缓存的值</param>
        /// <returns>result</returns>
        public T CacheNumber<T>(string key, T value)
        {
            T result = default;

            if (_cache.IsInCache(key)) //如果存在缓存取值
            {
                result = _cache.Get<T>(key);
            }
            else
            {
                if (!value.Equals(0))
                {
                _cache.Set_AbsoluteExpire<T>(key, value, _cache.time);
                }
            }
            return result;
        }

          /// <summary>
        /// 设置并返回缓存值(字符串)
        /// </summary>
        /// <typeparam name="T">返回类型</typeparam>
        /// <param name="key">缓存键值</param>
        /// <param name="value">要缓存的值</param>
        public T CacheString<T>(string key, T value)
        {
            T result = default;
            if (_cache.IsInCache(key)) //如果存在缓存取值
            {
                result = _cache.Get<T>(key);
            }
            else
            {
                if (value != null)
                {
                    _cache.Set_AbsoluteExpire<T>(key, value, _cache.time);
                }
            }
            return result;
        }
    }
}

```

### DI依赖注入配置

**ConfigureServices**

```c#
services.AddScoped<ICacheUtil,CacheUtil>();
```



### 使用

```c#
private readonly CacheUtil _cacheUtil;
public SnArticleService(ICacheUtil cacheUtil,IRepositoryFactory repositoryFactory, IconcardContext mydbcontext, snblogContext coreDbContext, ILogger<SnArticleService> logger) : base(repositoryFactory, mydbcontext)
        {
            _coreDbContext = coreDbContext;
            _cacheUtil= (CacheUtil)cacheUtil;
            _logger = logger ?? throw new ArgumentNullException(nameof(Logger));
        }
        
  result = _cacheUtil.CacheString("AsyGetTestName" + id, result);
```



### 实例的生命周期

我们上面看到了，.NET Core DI 为我们提供的实例生命周其包括三种：

- Transient： 每一次GetService都会创建一个新的实例
- Scoped： 在同一个Scope内只初始化一个实例 ，可以理解为（ 每一个request级别只创建一个实例，同一个http request会在一个 scope内）
- Singleton ：整个应用程序生命周期以内只创建一个实例 