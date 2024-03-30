---
title: ASP.NET Core MVC 自动映射
date: 2023-08-23
tags:
- CSharp
- NET
categories:
- CSharp
---

## 手动映射
- 在小项目中一般采用手动映射赋值，如：将StudentViewModel对象的属性赋值给Student
```c#
// StudentController.cs
// StudentViewModel.cs 是一个类
[HttpPost]
public IActionResult Add(StudentViewModel studentViewModel)
{
    var student = new Student()
    {
        Id = studentViewModel.Id,
        Name = studentViewModel.Name,
        Age = studentViewModel.Age,
        Sex = studentViewModel.Sex,
    };
    studentService.Add(student);
    return View();
}
```
- 手动映射需要逐个属性进行赋值，灵活度比较高，但项目中如果需要映射的地方比较多的话，则工作量和复杂度也会随之提升

## 自动映射
自动映射是由程序自动匹配属性名进行赋值，一般使用AutoMapper库完成，即将A对象的属性映射到B对象的属性值。
1. 安装自动映射包 - AutoMapper.Extensions.Microsoft.DependencyInjection/第三方库
2. 创建自动映射关系
```c#
// Profiles/AutomapProfile.cs
using AutoMapper;
using MvcDemo.ViewModels;
using MvcDemo.Models;

namespace MvcDemo.Profiles
{
    public class AutomapProfile:Profile
    {
        public AutomapProfile()
        {
            //创建映射关系
            CreateMap<StudentViewModel, Student>();
        }
    }
}
```
3. 注册自动映射服务
```c#
// Program.cs
//builder.Services.AddAutoMapper(typeof(AutomapProfile));
builder.Services.AddAutoMapper(cfg =>
{
    cfg.AddProfile<AutomapProfile>();
});
```
4. 注入IMapper接口
```c#
// Controllers/StudentController.cs
private readonly IMapper mapper;
private IStudentService studentService;

public StudentController(IStudentService studentService,IMapper mapper)
{
    this.studentService = studentService;
    this.mapper = mapper;
}
```
5. 调用映射方法
```c#
// Controllers/StudentController.cs
[HttpPost]
public IActionResult Add(StudentViewModel studentViewModel)
{
    var student =  mapper.Map<StudentViewModel, Student>(studentViewModel);
    studentService.Add(student);
    return View();
}
```

## 多个关系映射文件
当存在很多个对象需要映射时，通常会根据不同的类型，创建多个关系映射类，则在项目启动注册自动映射服务时，就需要加载多个映射类。
```c#
// Program.cs
builder.Services.AddAutoMapper(cfg =>
{
    cfg.AddProfile<AutomapProfile>();
    cfg.AddProfile<CompanyProfile>();
});
```
也可以通过扫描程序集的方式加载映射文件，配置程序集名称，程序会自动扫描继承了Profile类的文件
```c#
// Program.cs
builder.Services.AddAutoMapper(cfg =>
{
    cfg.AddMaps("MvcDemo");
});
```
【注】：AddMaps参数配置的是程序集名称，而不是命名空间，程序集名称可以通过项目属性获取

## 自动映射命名规则
默认情况下，自动映射的数据源和目标的属性，必须一致，才能进行映射，但是在实际应用中，属性名之间可能会存在差异，如果不处理，默认是无法自动映射成功的。</br>
需要在映射时进行配置源的命名格式和目标命名格式：
```c#
// Profiles/AutomapProfile.cs
namespace MvcDemo.Profiles
{
    public class AutomapProfile:Profile
    {
        public AutomapProfile()
        {
            SourceMemberNamingConvention = new LowerUnderscoreNamingConvention();
            DestinationMemberNamingConvention = new PascalCaseNamingConvention();
            //创建映射关系
            CreateMap<StudentViewModel, Student>();
        }
    }
}

// SourceMemberNamingConvention: 源类型成员命名规则
// LowerUnderscoreNamingConvention(): 下划线命名法
// DestinationMemberNamingConvention: 目标类型成员命名规则
// PascalCaseNamingConvention(): 帕斯卡命名法
```

## 字符替换
在实际开发中，如果映射源存在一些特殊字符，映射目标是正常字符，则需要进行替换才能映射。
```c#
var configuration = new MapperConfiguration(c =>
{
    c.ReplaceMemberName("Ä", "A");
    c.ReplaceMemberName("í", "i");
    c.ReplaceMemberName("Airlina", "Airline");
});
```

## 自动映射匹配前缀和后缀
数据源一般都会有固定的风格，如带有前缀、后缀等标识。</br>
默认情况下，带前缀是无法自动映射的，需要在映射匹配文件中，增加映射前缀RecongnizePrefixes("s")
```c#
// Profiles/AutomapProfile.cs
namespace MvcDemo.Profiles
{
    public class AutomapProfile:Profile
    {
        public AutomapProfile()
        {
            RecognizePrefixes("s");
            SourceMemberNamingConvention = new LowerUnderscoreNamingConvention();
            DestinationMemberNamingConvention = new PascalCaseNamingConvention();
            //创建映射关系
            CreateMap<StudentViewModel, Student>();
        }
    }
}
```
- 一般前缀都是具有一定规律的设置，否则有些前缀a，有些前缀b，没有一定的规律，则无法完全匹配
- 后缀通过RecognizePostfixes("s");设置
- 取消前缀设置ClearPrefixes();就是取消所有的前缀设置列表中设置的前缀，Automapper默认匹配了Get前缀，如果不需要可以清除

## 自动映射控制（不常用）
- 使用ShouldMapField和ShouldMapProperty控制哪些属性和字段能够被映射
- 默认所有public的field和property都会被map，也会map private 的setter，但是不会map整个property都是internal/private的属性
```c#
cfg.ShouldMapField = fi => false;
cfg.ShouldMapProperty = pi =>pi.GetMethod != null && (pi.GetMethod.IsPublic || pi.GetMethod.IsPrivate);
```

## 列表映射
在实际开发中，列表的应用场景还是比较多的，列表映射也比较常用。
```c#
// Controllers/StudentController.cs
[HttpPost]
public IActionResult Add(StudentViewModel studentViewModel)
{
    var listStudents=new List<StudentViewModel>();
    listStudents.Add(studentViewModel);
    var students =  mapper.Map<List<StudentViewModel>,List< Student>>(listStudents);
    studentService.Adds(students);
    return View();
}
```
AutoMapper默认会自动映射以下类型：
- IEnumerable
- IEnumerable<T>
- ICollection
- ICollection<T>
- IList
- IList<T>
- List<T>
- Arrays

这些集合之间可以相互映射，如：`mapper.Map<Source[], IEnumerable<Destination>>(sources);`

## 手动控制映射（不常用）
当对于完全没有任何规律的映射，那就需要手动配置映射属性列。
```c#
// 源类型
namespace MvcDemo.ViewModels
{
    public class UserViewModel
    {
        public int UserId { get; set; }

        public string UserName { get; set; }

        public string Mail { get; set; }
    }
}

// 目标类型
namespace MvcDemo.Models
{
    public class User
    {
        public int Id { get; set; }

        public string Name { get; set; }

        public string Email { get; set; }
    }
}

// 配置映射属性列
public class AutomapProfile:Profile
{
    public AutomapProfile()
    {
        CreateMap<UserViewModel, User>()
            .ForMember(dest => dest.Id, opt => opt.MapFrom(source => source.UserId))
            .ForMember(dest => dest.Name, opt => opt.MapFrom(source => source.UserName))
            .ForMember(dest => dest.Email, opt => opt.MapFrom(source => source.Mail));
    }
}

// 手动配置后就可以实现自动映射
public IActionResult Add(UserViewModel userViewModel)
{

    var user =  mapper.Map<UserViewModel,User>(userViewModel);

    return View();
}
```

## 嵌套映射
对于复杂的嵌套类型，对象的属性可能是一个复杂引用类型对象
```c#
// 源类型
namespace MvcDemo.ViewModels
{
    public class EmployeeViewModel
    {
        public int Id { get; set; }
        // 属性User为类型为UserViewModel的引用类型
        public UserViewModel User { get; set; }
    }
}
// 目标类型
namespace MvcDemo.Models
{
    public class Employee
    {
        public int Id { get; set; }
        // 属性Uer为类型为User的引用类型
        public User User { get; set; }
    }
}
// 需要对于属性类型也创建映射关系
CreateMap<UserViewModel, User>()
    .ForMember(dest => dest.Id, opt => opt.MapFrom(source => source.UserId))
    .ForMember(dest => dest.Name, opt => opt.MapFrom(source => source.UserName))
    .ForMember(dest => dest.Email, opt => opt.MapFrom(source => source.Mail));
CreateMap<EmployeeViewModel,Employee>();
// 控制器中调用
public IActionResult Add(UserViewModel userViewModel)
{
    EmployeeViewModel employeeViewModel = new EmployeeViewModel() { Id = 1, User = userViewModel };
    var employee = mapper.Map<EmployeeViewModel, Employee>(employeeViewModel);

    return View();
}
```