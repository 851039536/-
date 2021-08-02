# net Core 使用AutoMapper

数据库的实体模型和视图模型进行分离

数据库持久化对象（Persistent Object）：顾名思义，这个对象是用来将我们的数据持久化到数据库，一般来说，持久化对象中的字段会与数据库中对应的 table 保持一致。

视图对象（View Object）：视图对象 VO 是面向前端用户页面的，一般会包含呈现给用户的某个页面/组件中所包含的所有数据字段信息。

数据传输对象（Data Transfer Object）：数据传输对象 DTO 一般用于前端展示层与后台服务层之间的数据传递，以一种媒介的形式完成 数据库持久化对象 与 视图对象 之间的数据传递。

### NuGet

通过 NuGet 安装 AutoMapper 的包。

```c#
AutoMapper 
AutoMapper.Extensions.Microsoft.Dependencylnjection
```



### 配置对象转换

创建MappingProfile继承抽象类 Profile

```c#
using AutoMapper;
using Snblog.Models;
using System;
using System.Collections.Generic;
using System.Text;

namespace Snblog.Enties.AutoMapper
{
    public class MappingProfile : Profile
    {
        /// <summary>
        /// 配置构造函数，用来创建关系映射
        /// </summary>
        public MappingProfile()
        {
            //构建实体映射规则添加映射对象  
            //如两个实体字段一致可直接映射关系
            //SnUser原对象类型，SnUserDto 目标对象类型  ReverseMap，可相互转换
            CreateMap<SnUser, SnUserDto>().ReverseMap(); 
            //CreateMap<SnUser, SnUserDto>();
            //CreateMap<SnUserDto, SnUser>();
        }
    }
}

```

### 表实体

在转化的时候只有名字相同的字段才会成功附上对应的值。

**数据库实体**

```c#
using System;
using System.Collections.Generic;
using System.ComponentModel.DataAnnotations;

namespace Snblog.Models
{
    public partial class SnUser
    {
        public int UserId { get; set; }
        public string UserIp { get; set; }
        public string UserName { get; set; }
        public string UserEmail { get; set; }
        public string UserPwd { get; set; }
        public string UserPhoto { get; set; }
        public string UserTime { get; set; }
        public string UserNickname { get; set; }
        public string UserBrief { get; set; }
    }
}

```

**视图模型**

```c#
using System;
using System.Collections.Generic;
using System.ComponentModel.DataAnnotations;

namespace Snblog.Models
{
    public partial class SnUserDto
    {
        public int UserId { get; set; }
        public string UserIp { get; set; }
        public string UserName { get; set; }
        public string UserEmail { get; set; }
        public string UserPwd { get; set; }
        public string UserPhoto { get; set; }
        public string UserTime { get; set; }
        public string UserNickname { get; set; }
        public string UserBrief { get; set; }
    }
}

```



### 注册服务

```c#
            #region 实体映射
            services.AddAutoMapper(typeof(MappingProfile));
            #endregion
```

#### 自动化注册 1

```c#
            //自动化注册
            //Assembly.Load() 里面传的是当前项目的名称，或者类库的名称。获取当前项目下的所有类文件。
            //通过 Lamda 表达式 Where 找到所有以 Mapper 结尾的文件。
            //这样就可以自动注册项目内的所有 Mapper 文件了。
            services.AddAutoMapper(
               Assembly.Load("Snblog.Enties").GetTypes()
                   .Where(t => t.FullName.EndsWith("Mapper"))
                   .ToArray()
           );
```

#### 自动化注册 2

添加接口类 IProfile

```c#
    public interface IProfile
    {
     
    }
```

在所有的 Mapper 类里面，实现这个接口。

```c#
    public class UserMapper : Profile, IProfile
    {
        public UserMapper()
        {
            CreateMap<UserDto, User>();
            CreateMap<User, UserViewModel>()
                .BeforeMap((u, v) => u.Remark = "Good")
                .ForMember(v => v.DepartmentId, u => u.MapFrom(user => user.DepartmentId))
                .ForMember(v => v.DepartmentName, u => u.MapFrom(user => user.DepartmentInfo.Name))
                .AfterMap((u, v) => u.Age++);
        }
    }
```



再通过查找所有实现了 IProfile 接口的类，就可以找到所有的 Mapper 对象。

（ Profile 是 AutoMapper 组件里的接口，必须自定义项目中的唯一标记。）

创建 MapperRegister 类，实现获取所有的 Mapper 对象。

```c#
    public class MapperRegister
    {
        /// <summary>
        /// 通过反射自动化注册
        /// </summary>
        /// <returns></returns>
        public static Type[] MapType()
        {
            Assembly ass = Assembly.GetAssembly(typeof(IProfile));
            Type[] types = ass.GetTypes();

            List<Type> allList = new List<Type>();

            foreach (Type item in types)
            {
                if (item.IsInterface) continue;//判断是否是接口
                Type[] ins = item.GetInterfaces();
                foreach (Type ty in ins)
                {
                    if (ty == typeof(IProfile))
                    {
                        allList.Add(item);
                    }
                }
            }
            Type[] alltypes = allList.ToArray();
            return alltypes;
        }

    }
```



修改 StrartUp 类，调整注册方式。

```c#
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddControllers();
            //services.AddAutoMapper(typeof(UserMapper));
            //自动化注册
            services.AddAutoMapper(MapperRegister.MapType());

        }
```

这样就可以每次添加 Mapper 类的时候，只需要添加 IProfile 标记，就可以自动注册了。

转自 https://www.cnblogs.com/miaowacao/p/12757275.html

### 应用

入参控制好 Add/Up  Dto 转数据实体。

返回参数 数据实体转Dto

```c#
 // 创建一个字段来存储mapper对象
  private readonly IMapper _mapper;
  
   public SnUserService(IMapper mapper)
        {
            _service = service;
            _mapper = mapper;
        }
       //更新-转数据实体
       var model = _mapper.Map<SnUser>(user);
       int da = await CreateService<SnUser>().UpdateAsync(model);

       //查询- 数据库实体转Dto
       public async Task<List<SnUserDto>> AsyGetUser()
        {
            var user = await  _service.SnUser.ToListAsync();
            return _mapper.Map<List<SnUserDto>>(user); 
        }
```



