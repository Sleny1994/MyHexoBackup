---
title: ASP.NET Core MVC 缓存
date: 2023-08-24
tags:
- CSharp
- NET
categories:
- CSharp
---

## 缓存的作用
1. 提高应用程序的访问速度
2. 适用于不易改变的数据

## 缓存的分类
根据缓存的应用范围和存储方式，可以分为以下几种：
1. 内存缓存：这种方式是将内容缓存到Web服务器内存中，主要适用于单服务器程序，且在服务器重启后，缓存中的数据也会丢失。
2. 缓存服务器：对于分布式部署的Web系统，缓存与内存中的方式会造成各个Web服务器中的缓存内容不一致，一般都会有独立的缓存服务器，如Redis，SQL Server等存储缓存的地方。缓存服务器中的内容，不会随着Web服务器的重启而变化。
3. 客户端：缓存于客户端一般通过Header实现，也可以通过localStorage，Cookie等方式。
### 内存缓存
In-Memory缓存，将数据缓存在Web服务器内存中，适用于单服务器部署的程序。在ASP.NET Core MVC程序中，使用内存缓存的步骤如下：
1. 添加缓存服务
```c#
// Program.cs
//内存缓存
builder.Services.AddMemoryCache();
```
2. 注入缓存接口
在需要用到内存缓存的控制器中，添加内存缓存接口IMemoryCache注入。
```c#
// Controllers
private readonly ILogger<HomeController> _logger;

//内存缓存接口
private readonly IMemoryCache _memoryCache;

public HomeController(ILogger<HomeController> logger,IMemoryCache memoryCache)
{
    _logger = logger;
    _memoryCache = memoryCache;
}
```
3. 获取/设置缓存
```c#
// Controllers
public IActionResult Index()
{

    if(!_memoryCache.TryGetValue("citys",out List<City> cityList))
    {
        cityList = GetCitys();
        var memoryCacheEntryOptions = new MemoryCacheEntryOptions();
        memoryCacheEntryOptions.SetAbsoluteExpiration(TimeSpan.FromSeconds(10));
        memoryCacheEntryOptions.RegisterPostEvictionCallback((object key, object value, EvictionReason reason, object state) =>
        {
            //在被清除缓存时，重新回调，重新填充
            _logger.LogInformation("缓存已被释放！");
        }, this);
        _memoryCache.Set("citys", cityList, memoryCacheEntryOptions);
    }
    ViewBag.Citys = cityList;
    return View();
}
```
4. 参数说明
示例中MemoryCacheEntryOptions，主要用于设置内存缓存参数，主要有以下几个参数：
    - AbsoluteExpiration 设置绝对过期时间
    - SlidingExpiration 滑动过期时间
    - PostEvictionCallbacks 缓存清除时的回调函数
### 分布式缓存
分布式缓存是由多个应用服务器共享的缓存，通常作为访问它的应用服务器的外部服务进行维护。分布式缓存可以提高 ASP.NET Core 应用的性能和可伸缩性，尤其是当应用由云服务或服务器场托管时。

与其他将缓存数据存储在单个应用服务器上的缓存方案相比，分布式缓存具有多个优势。分布式缓存的优点：
- 无需Sticky Session
- 可扩展，适用于多台Web服务器部署的情况
- 独立存储，Web服务器重启不会影响缓存
- 性能更好
#### 部署要求
1. 前提条件，添加包引用
    - Redis：Microsoft.Extensions.Caching.StackExchangeRedis
    - SQL Server：Microsoft.Extensions.Caching.SqlServer
    - NCache：NCache.Microsoft.Extensions.Caching.OpenSource
2. 环境搭建，根据选择的类别进行部署
3. 安装依赖包（以Redis为例），NuGet安装第三方库Microsoft.Extensions.Caching.StackExchangeRedis
4. 添加分布式缓存服务，添加StackExchangeRedisCache服务，通过Configruation配置Redis连接信息和InstanceName实例名称
```c#
builder.Services.AddStackExchangeRedisCache(options =>
{
    options.Configuration = "192.168.1.2:6379";
    options.InstanceName = "redis";
});
```
5. 注入分布式缓存接口，在Controller中，注入分布式缓存接口IDistributedCache
```c#
private readonly IDistributedCache _distributedCache;

public HomeController(ILogger<HomeController> logger ,IDistributedCache distributedCache)
{
    _logger = logger;
    _distributedCache = distributedCache;
}
```
6. 获取/设置缓存，在使用缓存的地方，获取GetString和设置SetString缓存
```c#
public IActionResult Index()
{
    var cityList = new List<City>();
    var obj = _distributedCache.GetString("citys");
    if (string.IsNullOrEmpty(obj))
    {
        cityList = GetCitys();
        DistributedCacheEntryOptions options = new DistributedCacheEntryOptions();
        options.SetAbsoluteExpiration(TimeSpan.FromSeconds(60));
        obj = JsonConvert.SerializeObject(cityList);
        _distributedCache.SetString("citys", obj,options);
    }
    cityList = JsonConvert.DeserializeObject<List<City>>(obj);
    ViewBag.Citys = cityList;
    return View();
}
```
7. 测试会发现，数据首次获取是从数据库中获取，再次获取时，则会从缓存获取，可以从Redis服务器上进行查看
    - 缓存服务器中存储的Key是加了配置的InstanceName前缀`Keys *`
    - 虽然代码中是通过SetString进行存储，由于存储JSON序列化对象，所以Redis自动识别对象类型为hash`type rediscitys`
    - 存储的中文在缓存服务器中是转码后的