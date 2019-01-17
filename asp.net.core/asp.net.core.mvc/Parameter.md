### [ASP.NET Core Mvc 参数绑定](#top) :grey_exclamation: <b id="top"></b>
`参数绑定,我们可以对于传入的参数绑定到方法参数上面`:white_check_mark:

------

- [x] [`1.参数绑定实例`](#target1)
- [x] [`2.参数特性`](#target2)
- [x] [`3.ModelState`](#target3)


------

#####  :octocat: [1.参数绑定实例](#top) <b id="target1"></b> 

##### 参数来自 QueryString :speech_balloon: 
`绑定查询参数 Url`: `https://localhost:44340/Demo/Index?name=jxkicker&age=89`
```c#
public class DemoController : Controller
{

    public IActionResult Index( String name,  Int32 age)
    {
        return Content($"{name} {age}");
    }
}
//结果 jxkicker 89
```

##### 参数来自 路由 :speech_balloon: 
`Url`：`https://localhost:44340/Demo/jxkicker/67`
```c#
public class DemoController : Controller
{
    [Route("/Demo/{name}/{age}")]
    public IActionResult Index( String name,  Int32 age)
    {
        return Content($"{name} {age}");
    }
}
```

##### 参数来源
`参数的来源可以是多个地方 可以来自于 Http 标头，路由数据,请求体,表单数据,查询字符串,服务注册 为了便于区分我们提供了参数特性` 



#####  :octocat: [2.参数特性](#top) <b id="target2"></b> 

|`特性`|`数据源`|
|:-----|:------|
|`FromHeaderAttribute` `[FromHeader]`|`Http Header`|
|`FromQueryAttribute` `[FromQuery]`|`查询字符串`|
|`FromRouteAttribute` `[FromRoute]`|`来自路由`|
|`FromFormAttribute` `[FromForm]`|`来自表单`|
|`FromServicecAttribute` `[FromService]`|`来自服务注册 此特性使用依赖关系注入绑定服务中的参数`|
|`FromBodyAttribute` `[FromBody]`|`来自请求体`|
| `[BindNever]`|`指示模型绑定器从不绑定到此参数。`|
| `[BindRequired]`|`如果无法发生绑定，此特性将添加模型状态错误。`|

##### 实例
`Url`：`https://localhost:44340/Demo/user?Name=JKXIHSDKND5845&Age=78`

```c#
public IActionResult User([FromQuery] User user)
{
    if (ModelState.IsValid)
    {
        return Content($"{user.Name} {user.Age}");
    }
    else
    {
        return Content($"Bind Error");
    }
}


public class User
{
    public String Name { get; set; }
    public Int32 Age { get; set; }
}

//页面结果
JKXIHSDKND5845 78
```
#####  :octocat: [3.ModelState](#top) <b id="target3"></b> 
`ModelState 我们通常在方法中用此判断模型绑定是否正确 那么我们来认识一下这个对象吧,它属于类` [`ModelStateDictionary`](https://docs.microsoft.com/zh-cn/dotnet/api/system.web.modelbinding.modelstatedictionary?view=netframework-4.7.2&viewFallbackFrom=netcore-2.2)

##### 属性 :speech_balloon: 
* `Count`: 	`获取集合中键/值对的数目。`
* `IsReadOnly`:`获取一个值，该值指示字典是否为只读。`
* `IsValid`:`获取指示字典中所有模型状态对象是否有任何错误的值。`
* `Item[String]`:`获取或设置与指定的键关联的值。返回` [`ModelStateEntry`](https://docs.microsoft.com/zh-cn/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelstateentry?view=aspnetcore-2.2) `类型`
* `Keys`:`获取包含词典中的键的集合。`
* `Values`:`获取包含词典中的值的集合。`

##### 方法
* `GetEnumerator()`:`返回一个可用于循环访问字典的枚举器。`
* `AddModelError(String, Exception)`:`使用指定的密钥和使用指定值异常，添加一个模型错误到错误集合。`
* `AddModelError(String, String)`:`使用指定的密钥和使用指定值错误消息，添加指定的模型错误到错误集合。`
* `TryGetValue(String, ModelState)`:`尝试获取与指定的键关联的值。`

```c#
public IActionResult LoginUser([FromQuery] [BindRequired] String Name ,
[FromQuery,BindRequired]Int32 Age) {

    var count = ModelState.Count;
    var name =ModelState["Name"].RawValue; //获取Name
    if (ModelState.IsValid)
    {

        ViewBag.Name = Name;
        ViewBag.Age = Age;
        
        ViewBag.ModelName = name;
        ViewBag.Count = count; //绑定了多少个值 
    }
    else {
        ViewBag.Name = "不明";
        ViewBag.Age = "不明";

        ViewBag.ModelName = "不明";
        ViewBag.Count = 0;
    }
    return View();
}
```

--------------------
`作者:` `KickGod` 
`完成时间`:`2018年12月31日18:33:38`
`备注信息`: `任意使用` 
