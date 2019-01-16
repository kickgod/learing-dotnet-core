### [Web 底层的HTTP对象](#top) :grey_exclamation: <b id="top"></b>
`ASP.NET Core 无论怎么样都是基于Http的 所以那些HTTP对象是非常重要的,需要你去本质的了解`:white_check_mark:

------

- [x] [`1.HttpRequest`](#target1)
- [x] [`2.HttpContext`](#target2)
- [x] [`3.HttpResponse`](#target3)

------

#####  :octocat: [1.HttpRequest](#top) <b id="target1"></b> 
`概括`:`可以通过HttpRequest 可以获得 QueryString  Cookie Header From传递的数据 ` [`官方API`](https://docs.microsoft.com/zh-cn/dotnet/api/system.web.httprequest?view=netframework-4.7.2)

##### 属性 :speech_balloon:
* `Headers`:`获取 HTTP 头集合。`
* `Cookies`:`获取客户端发送的 cookie 的集合。`
* `QueryString`:`获取 HTTP 查询字符串变量集合。`

#####  :octocat: [2.HttpContext](#top) <b id="target2"></b> 
`封装有关个别 HTTP 请求的所有 HTTP 特定的信息。` [`官方API`](https://docs.microsoft.com/zh-cn/dotnet/api/system.web.httpcontext?view=netframework-4.7.2)
##### 属性 :speech_balloon:
* `Session`: `为当前 HTTP 请求获取` [`ISeesion`](https://docs.microsoft.com/zh-cn/dotnet/api/microsoft.aspnetcore.http.isession?view=aspnetcore-2.2) `对象。`
* `Request`:`为当前 HTTP 请求获取 HttpRequest 对象。`
* `Response`:`获取当前 HTTP 响应的 HttpResponse 对象。`
* `Server`:`获取提供用于处理 Web 请求的方法的 HttpServerUtility 对象。`
* `Current`:`为当前 HTTP 请求获取或设置 HttpContext 对象。`
#####  :octocat: [3.HttpResponse](#top) <b id="target3"></b> 



--------------------
`作者:` `JxKicker` 
`完成时间`:`2019年1月16日20:13:19`
`备注信息`: `任意使用` 
