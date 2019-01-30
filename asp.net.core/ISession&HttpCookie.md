### [ASP.NET Core Session / Cookie](#top) :grey_exclamation: <b id="top"></b>
------
`Session /Cookie /Application 是一个web的基石`:white_check_mark:

- [x] [`1.Session`](#target1)
- [x] [`2.Cookie`](#target2)
- [x] [`3.HttpApplication`](https://docs.microsoft.com/zh-cn/dotnet/api/system.web.httpapplication?view=netframework-4.7.2)
- [x] [`4.ISession`](#target3)
- [x] [`5.ASP.NET Core 使用 Session`](#target5)

------

#####  :octocat: [System.Web.SessionState.HttpSessionState ](#top) <b id="target1"></b> 
`概括`:`HttpSessionState 对,这就是Session ,这个类具有很多的属性和方法,我们来认识一下吧 `
* `它提供对会话状态值以及会话级别设置和生存期管理方法的访问。`
* `它继承自 System.Collections.ICollection`
* `HttpSessionStateBase 用作提供对会话状态值、会话级别设置和生存期管理方法的访问的类的基类。 抽象类`

* `HttpSessionStateWrapper类派生自 HttpSessionStateBase 类，并充当包装HttpSessionState类。`
    * `它的构造函数:  HttpSessionStateWrapper(HttpSessionState) 初始化 HttpSessionStateWrapper 类的新实例。`
    * `【设计模式】-装饰者模式（Wrapper） 了解一下 你就懂了 HttpSessionState和 HttpSessionStateBase的关系`
    
`本质上Session的值可以是任意类型`
#####  属性 :speech_balloon: 
* `Timeout`
   * `获取或设置在会话状态提供程序终止会话之前各请求之间所允许的时间（以分钟为单位）。`
* `SessionID`
   * `获取会话的唯一标识符。`
* `Mode` 	
   * `获取当前会话状态模式。Get 只读 `:`属性类型 SessionStateMode Enum  `
* `Item[Int32]` `索引器`
   * `按数字索引获取或设置会话值。`
* `Item[String]` `索引器` 	 
   * `按名称获取或设置会话值。`
* `Count` 	
   * **`获取会话状态集合中的项数。可以获得在线人数`**
* `Keys` 	
   * `获取存储在会话状态集合中所有值的键的集合。`
* `IsNewSession` 	
   * `获取一个值，该值指示会话是否是与当前请求一起创建的。`  
* `IsCookieless` 	
  * `获取一个值，该值指示会话 ID 是嵌入在 URL 中还是存储在 HTTP Cookie 中。`
  ```xml
    <configuration>  
      <system.web>  
        <sessionState   
          mode="InProc"  
          cookieless="true"  
          timeout="30" />  
      </system.web>  
    </configuration>  
  ```
* `IsReadOnly` 	
   * `获取一个值，该值指示会话是否为只读。`
* `IsSynchronized`
   * `获取一个值，该值指示对会话状态值的集合的访问是否是同步（线程安全）的。`
   
|`值 number`|`说明`|
|:-----|:----|
|`Custom 	4` | `会话状态正在使用自定义数据存储来存储会话状态信息。`|
|`InProc 	1` | `会话状态正在处理 ASP.NET 辅助进程。`|
|`Off 	0 	` |`会话状态被禁用。`|
|`SQLServer 	3`|`会话状态正在使用进程外 SQL Server 数据库存储状态信息。`|
|`StateServer 	2`|` 	会话状态正在使用进程外 ASP.NET 状态服务存储状态信息。`|

##### 方法 :speech_balloon: 
* `Abandon()`
   * `取消当前会话。`
* `Add(String, Object) 	`
   * `向会话状态集合添加一个新项。`
* `Clear()`
   * `从会话状态集合中删除所有键和值。`
* `CopyTo(Array, Int32) `
   * `从数组中指定索引处开始，将会话状态值集合复制到一维数组。`
* `Remove(String)`
   * `从会话状态集合中删除某项。`
* `RemoveAll()`
   * `从会话状态集合中删除所有键和值。`
* `RemoveAt(Int32) 	`
   * `删除会话状态集合中指定索引处的项。`

```c#
JsonData data = new JsonData();
data["UserID"] = "123456";
data["UserPwd"] = "sddsda";
String UserData =JsonMapper.ToJson(data);
Session["User"] = UserData;  
 
//获得值
//Session的本质是键值对 所以通过键去获取就可以了
String data=(String)Session["User"]; 
 
Session.Clear(); //从回话状态中移除所有键值
Session.Abandon(); //取消当前回话状态
Session.Remove("User");//移除当个键
Session.RemoveAll();//从回话状态中移除所有键值
```

#####  :octocat: [System.Net. Cookie  ](#top) <b id="target2"></b> 
`提供一组用于管理 Cookie 的属性和方法。 此类不能被继承。 在 ASP.NET Core 中使用 Cookie 和在 ASP.NET 中完全不一样
Core 提供了一套自己的身份验证授权架构 所以显得十分的麻烦`

##### 构造函数
* `Cookie()`:`初始化 Cookie 类的新实例。`
* `Cookie(String, String)`
   * `使用指定的 CookieName和 Value初始化 类的新实例。`
* `Cookie(String, String, String)` 	
   * `使用指定的 Name、Value 和 Path 初始化 Cookie 类的新实例。`
* `Cookie(String, String, String, String)` 	
   * `使用指定的 Name、Value、Path 和 Domain 初始化 Cookie 类的新实例。`

##### 属性
* `Comment` 	
   * `获取或设置服务器可添加到 Cookie 中的注释。`
* `CommentUri` 	
   * `获取或设置服务器可通过 Cookie 来提供的 URI 注释。`
* `Domain` 
   * `获取或设置 Cookie 对其有效的 URI。`
* `Expired `	
   * `获取或设置 Cookie 的当前状态。`
* `Path` 	
   * `获取或设置此 Cookie 适用于的 URI。`
* `HttpOnly`	
   * `确定页面脚本或其他活动内容是否可访问此 Cookie。`
* `Name` 	
   * `获取或设置 Cookie 的名称。`
* `Value` 	
   * `获取或设置 Value 的 Cookie。`
* `Version` 	
   * `获取或设置此 Cookie 符合的 HTTP 状态维护版本。`
* `Secure`
   * `获取或设置 Cookie 的安全性级别。`
   * `如果客户端仅在使用安全超文本传输协议 (HTTPS) 的后继请求中返回 Cookie，则为 true；否则为 false。 默认值为 false。`
   
```c#
HttpWebRequest request = (HttpWebRequest)WebRequest.Create(args[0]);
request.CookieContainer = new CookieContainer();

HttpWebResponse response = (HttpWebResponse) request.GetResponse();
 

// Print the properties of each cookie.
foreach (Cookie cook in response.Cookies)
{
    Console.WriteLine("Cookie:");
    Console.WriteLine("{0} = {1}", cook.Name, cook.Value);
    Console.WriteLine("Domain: {0}", cook.Domain);
    Console.WriteLine("Path: {0}", cook.Path);
    Console.WriteLine("Port: {0}", cook.Port);
    Console.WriteLine("Secure: {0}", cook.Secure);
 
    Console.WriteLine("When issued: {0}", cook.TimeStamp);
    Console.WriteLine("Expires: {0} (expired? {1})", 
        cook.Expires, cook.Expired);
    Console.WriteLine("Don't save: {0}", cook.Discard);    
    Console.WriteLine("Comment: {0}", cook.Comment);
    Console.WriteLine("Uri for comments: {0}", cook.CommentUri);
    Console.WriteLine("Version: RFC {0}" , cook.Version == 1 ? "2109" : "2965");

    // Show the string representation of the cookie.
    Console.WriteLine ("String: {0}", cook.ToString());
}
```


##### 添加一个单值Cookie

```c#
HttpCookie ck_User = new HttpCookie("User");
ck_User.Value = UserData;
ck_User.Expires = DateTime.Now.AddSeconds(60); //不设置的话,那么关闭浏览器回话结束就没有了 设置有效期
Response.Cookies.Add(ck_User);


//获得Cookie的值
ViewBag.User = Request.Cookies["User"].Value;

//销毁Cookie
//设置Cookie为已经过期就可以了

Response.Cookies["User"].Expires = DateTime.MinValue;
```

##### 多值Cookie

```c#
HttpCookie ck_User = new HttpCookie("User");
ck_User.Values.Add("UserData", UserData);
ck_User.Values.Add("LoginTime", DateTime.Now.ToLocalTime().ToString());
ck_User.Values.Add("Type", "Code");

ck_User.Expires = DateTime.Now.AddSeconds(200); //不设置的话,那么关闭浏览器回话结束就没有了 设置有效期
Response.Cookies.Add(ck_User);

//获取多值Cookie的值

ViewBag.User = Request.Cookies["User"].Values.Get("UserData");
ViewBag.LoginTime = Request.Cookies["User"].Values.Get("LoginTime");
```

#####  :octocat: [ISession  ](#top) <b id="target3"></b>
`在 ASP.NET Core中的Session 是重写的,实现ISessio 接口`

##### 属性
* `Id`:`获得SessionId`

* `Keys`:`获取所有的键`

##### 方法
* `Clear()`:`消除所有的Session 值`
* `CommitAsync(CancellationToken)`:	`异步存储Session`

* `LoadAsync(CancellationToken)`:`异步加载数据` 	
* `Remove(String)`:`通过键获得值`
* `Set(String, Byte[])`:`存储对象 但是需要编码`
* `TryGetValue(String, Byte[])`:`尝试获取值` 	

##### Session 扩展方法
`Session 扩展方法,通过此可以存储或获取特定类型的值`
```c#
Get(ISession, String)
GetInt32(ISession, String)
GetString(ISession, String)
SetInt32(ISession, String, Int32)
SetString(ISession, String, String)
```

#####  :octocat: [5 Microsoft.AspNetCore.Session  ](#top) <b id="target5"></b> 
`在 ASP.NET Core 中Session 不是自备在框架中的 要使用它 需要引入 Nuget 包`
* `1.安装 Microsoft.AspNetCore.Session `

`其次就算引用了也要注册 Session 中间件 才能够正常使用`
* `2.中间件使用 在 Startup 类的 Configure 方法中`

```c#
//使用 Session  依赖:Microsoft.AspNetCore.Session 
app.UseSession();
```
* `3.ConfigureServices 配置`
```c#
services.Configure<CookiePolicyOptions>(options =>
{
    options.CheckConsentNeeded = context => false; //一定是 false 不然存了找不到
    options.MinimumSameSitePolicy = SameSiteMode.None;
});
```
* `4.正常使用`
```c#
HttpContext.Session.SetString("uid",UserId);
HttpContext.Session.SetString("upd",UserPassword);


String id = HttpContext.Session.GetString("uid");
String upd = HttpContext.Session.GetString("upd");
```






--------------------
`作者:` `KickGod` 
`完成时间`:`2019年1月12日18:26:44`
`备注信息`: `任意使用` 
