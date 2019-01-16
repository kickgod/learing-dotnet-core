### [ASP.NET Core Mvc 参数绑定](#top) :grey_exclamation: <b id="top"></b>
`参数绑定,我们可以对于传入的参数绑定到方法参数上面`:white_check_mark:

------

- [x] [`1.参数绑定实例`](#target1)
- [x] [`2.参数特性`](#target2)


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



--------------------
`作者:` `KickGod` 
`完成时间`:`2018年12月31日18:33:38`
`备注信息`: `任意使用` 
