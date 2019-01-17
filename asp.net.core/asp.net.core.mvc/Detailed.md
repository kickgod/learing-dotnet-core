### [Asp.Net Core 细节问题](#top) :grey_exclamation: <b id="top"></b>
`细节问题,重中之重的思考,生存或者毁灭`:white_check_mark:

------

- [x] [`1.ViewData 和 ViewBag`](#target1)
- [x] [`2.提取页面重复部分`](#target2)
- [x] [`3.TempData/Cache/Session`](#target3)
- [x] [`4.文件路径的问题`](#target4)
- [x] [`5._ViewStart.cshtml 是什么？`](#target5)
- [x] [`6._ViewImports.cshtml 是什么？`](#target6)
- [x] [`7.数据验证探究`](#target7)

------

#####  :octocat: [1.ViewData 和 ViewBag的区别](#top) <b id="target1"></b> 

|`ViewData`|`ViewBag`|
|:---|:----|
|`键值对`|`动态类型 所以用于Razor中 的方法参数的时候就需要强转`|
|`索引器`|`ViewData的封装`|
|`支持任意类型`|`动态属性`|

```c#
public IActionResult LoginUser([FromQuery] [BindRequired] String Name ,
[FromQuery,BindRequired]Int32 Age) {
    if (ModelState.IsValid)
    {
        ViewData["Name"] = Name;
        ViewData["Age"] = Age;

        ViewBag.Name = Name;
        ViewBag.Age = Age;
    }
    else {
        ViewData["Name"] = "不明";
        ViewData["Age"] = "不明";

        ViewBag.Name = "不明";
        ViewBag.Age = "不明";
    }
    return View();
}
```

#####  :octocat: [2.提取页面重复部分](#top) <b id="target2"></b> 
`概括`:`解决这个问题 .net 提供了3种方法可以实现页面视图的重用问题`
* `1.母版页`:`@renderbody() 只能使用一次`
    * `在子页面中使用Layout属性 指定母版页`
    * `不能独立使用`
* `2.子视图`:`@rendersection() 实现子页面内容个性化，这种可以达到内容最大化`
    * `可以独立使用,可以用于局部页面刷新`
* `3.部分视图`:`部分视图需要结合 Ajax load 请求使用,也可以使用 html辅助 `
    * `@Html.Partial("~/Views/Shared/MaPartialView.cshtml")`
> `还用说吗,肯定可以在一个页面里面同时结合使用它们啊！`


#####  :octocat: [3.TempData/Cache/Session](#top) <b id="target3"></b> 
|`TempData`|[`Cache`](https://docs.microsoft.com/zh-cn/dotnet/api/microsoft.extensions.caching.memory.imemorycache?view=aspnetcore-2.2)|`Session`|
|:----|:----|:----|
|`视图级别`|`应用程序级别 `|`回话级别 记住它和 ASP.NET MVC的 Session是不一样的`|
|`只允许消费一次,取的时候就销毁`|`服务端保存`|`服务端保存`|
|`可多次赋值`|`可以设置有效期`|`客户端浏览器关闭就没有了`|
|`键值对形式`|`键值对形式`|`键值对形式`|

##### TempData
`write 视图创建 Read视图去读 `
```c#
public IActionResult Write() {
    TempData["userName"] = "JxKicker";
    return Content("userName 已经创建");
}

public IActionResult Read()
{
    return View();
}
```
`write.cshtml`
```cshtml
@{
    ViewData["Title"] = "Write";
    Layout = "~/Views/Shared/_Layout.cshtml";
}

<h2>Write</h2>

<a asp-area="" asp-controller="Demo" asp-action="Read">跳转第二个视图</a>
```
`read.cshtml`
```cshtml
@{
    ViewData["Title"] = "Read";
    Layout = "~/Views/Shared/_Layout.cshtml";
}

<h4>@TempData["userName"]</h4>
```

##### Cache
`记住它是 一个全新的实现不同于 .net framework 是一个` `IMemoryCache` `接口的实现者 使用Get/Set 操作数据`
* `需要提过依赖注入的方式去获取`
```c#
private readonly IMemoryCache _cache;

private readonly IHostingEnvironment _hostingEnvironment;

public DemoController(IHostingEnvironment hostingEnvironment, IMemoryCache memoryCache)
{
    _hostingEnvironment = hostingEnvironment;
    _cache = memoryCache;
}
```
`使用它`
```c#
public IActionResult Cache() {
    _cache.Set<String>("Name", "JxKicker");
    _cache.Set<Int32>("Age", 18);
    return Content("Cache 设置好了");
}

public IActionResult GetCache()
{
    String Name = _cache.Get<String>("Name");
    int Age = _cache.Get<Int32>("Age");
    _cache.Set<Int32>("Age", 18);
    return Json(new { Name = Name,Age = Age });
}
```
##### cache 缓存深入一点点
`ASP.NET Core 缓存Caching，.NET Core 中为我们提供了Caching 的组件。目前Caching 组件提供了三种存储方式。`
`IDistributedCache 提供了分布式缓存接口 支持Redis 或者 Sql Server 分布式缓存`
* `Memory`
* `Redis`
* `SqlServer`
```powershell
//添加引用
Install-Package Microsoft.Extensions.Caching.Memory
```
`注册组件 在在Startup.cs 中 ConfigureServices`
```c#
public void ConfigureServices(IServiceCollection services)
{
    services.AddMemoryCache();
    // Add framework services.
    services.AddMvc();            
}
```
##### 全部使用例子
```c#
private IMemoryCache _memoryCache;
public HomeController(IMemoryCache memoryCache)
{
    _memoryCache = memoryCache;
}

public IActionResult Index()
{
    string cacheKey = "key";
    string result;
    if (!_memoryCache.TryGetValue(cacheKey, out result))
    {
        result = $"LineZero{DateTime.Now}";
        _memoryCache.Set(cacheKey, result);
        //设置相对过期时间2分钟
        _memoryCache.Set(cacheKey, result, new MemoryCacheEntryOptions()
            .SetSlidingExpiration(TimeSpan.FromMinutes(2)));
        //设置绝对过期时间2分钟
        _memoryCache.Set(cacheKey, result, new MemoryCacheEntryOptions()
            .SetAbsoluteExpiration(TimeSpan.FromMinutes(2)));
        //移除缓存
        _memoryCache.Remove(cacheKey);
        //缓存优先级 （程序压力大时，会根据优先级自动回收）
        _memoryCache.Set(cacheKey, result, new MemoryCacheEntryOptions()
            .SetPriority(CacheItemPriority.NeverRemove));
        //缓存回调 10秒过期会回调
        _memoryCache.Set(cacheKey, result, new MemoryCacheEntryOptions()
            .SetAbsoluteExpiration(TimeSpan.FromSeconds(10))
            .RegisterPostEvictionCallback((key, value, reason, substate) =>
            {
                Console.WriteLine($"键{key}值{value}改变，因为{reason}");
            }));
        //缓存回调 根据Token过期
        var cts = new CancellationTokenSource();
        _memoryCache.Set(cacheKey, result, new MemoryCacheEntryOptions()
            .AddExpirationToken(new CancellationChangeToken(cts.Token))
            .RegisterPostEvictionCallback((key, value, reason, substate) =>
            {
                Console.WriteLine($"键{key}值{value}改变，因为{reason}");
            }));
        cts.Cancel();
    }
    ViewBag.Cache = result;
    return View();
}
```
##### 再深入一点
* [`内存缓存`](https://docs.microsoft.com/zh-cn/aspnet/core/mvc/views/tag-helpers/built-in/cache-tag-helper?view=aspnetcore-2.2)
* [`分布式缓存`](https://docs.microsoft.com/zh-cn/aspnet/core/mvc/views/tag-helpers/built-in/distributed-cache-tag-helper?view=aspnetcore-2.2)
* [`进阶-内存缓存`](https://docs.microsoft.com/zh-cn/aspnet/core/performance/caching/memory?view=aspnetcore-2.2)
* [`进阶-分布式缓存`](https://docs.microsoft.com/zh-cn/aspnet/core/performance/caching/distributed?view=aspnetcore-2.2)
* [`高阶-响应缓存中间件`](https://docs.microsoft.com/zh-cn/aspnet/core/performance/caching/middleware?view=aspnetcore-2.2)
#####  :octocat: [4.文件路径的问题](#top) <b id="target4"></b>
`在ASP.NET Core 中获取网站更目录的方式需要通过依赖注入的方式得到 借助 IHostingEnvironment 接口`
```c#
private readonly IMemoryCache _cache;

private readonly IHostingEnvironment _hostingEnvironment;

public DemoController(IHostingEnvironment hostingEnvironment, IMemoryCache memoryCache)
{
    _hostingEnvironment = hostingEnvironment;
    _cache = memoryCache;
}
//在方法中使用 WebRootPath / ContentRootPath 获取不同的路径
string webRootPath = _hostingEnvironment.WebRootPath;
string contentRootPath = _hostingEnvironment.ContentRootPath;
```
* `webRootPath`:`wwwroot 文件夹路径`
* `ContentRootPath`:`网站根目录`
#####  :octocat: [5. _ViewStart.cshtml 是什么？](#top) <b id="target5"></b>
`注意:名称固定,不能改变,一般放在视图目录根目录下面,自动执行无需手工调用`
* `视图呈现之前执行`
* `可以做全局数据初始化,比如模板指定 `
* `不要在视图中做大量的业务操作`

`默认内容：配置母版页`
```c#
@{
    Layout = "_Layout";
}
```
#####  :octocat: [6. _ViewImports.cshtml 是什么？_](#top) <b id="target6"></b>
`名称可以看出来是引入,因为我们要在 Razor 中使用对象,需要在 @{ } 指定命名空间的,对于功能的命名空间可以在 Import里面公共引用,
好做一个公共的维护,对于一些全局引用`
* `不要在视图中做大量的业务操作，视图目录下的根目录下`

`默认内容 引入项目命名空间 和模型类 和TagHelper`
```cshtml
@using Demo  
@using Demo.Models
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
```
#####  :octocat: [7.数据验证探究](#top) <b id="target7"></b>
[`数据验证特性`](https://docs.microsoft.com/zh-cn/dotnet/api/system.componentmodel.dataannotations.validationattribute?view=netframework-4.7.2) `都是实现的这个 ValidationAttribute 特性`

* `Required、StringLength、RegularExpression 和 Max,Min Range...都是内置的验证类`

* `Required 和 MinimumLength 特性表示属性必须有值；但用户可输入空格来满足此验证。`

* `[Display(Name = "Release Date")]`:`展示名称`
* `[DataType(DataType.Date)]`:`日期类型`
* `RegularExpression 特性用于限制可输入的字符。 在上述代码中，Genre 和 Rating 必须使用纯字母（禁用首字母大写、空格、数字和特殊字符）。`
* `Range 特性将值限制在指定范围内。`
* `StringLength 特性使你能够设置字符串属性的最大长度，以及可选的最小长度。`
* `从本质上来说，需要值类型（如 decimal、int、float、DateTime），但不需要 [Required] 特性。`

##### 使用
`定义强类型视图并床底包含验证规则的业务数据模型`
* `使用HtmlHelperValidationFor 初始化前端验证规则`

* `使用HtmlHelperValidationMessageFor 生成提示文字`



--------------------
`作者:` `模板` 
`完成时间`:`2018年12月31日18:33:38`
`备注信息`: `禁止转载` 
