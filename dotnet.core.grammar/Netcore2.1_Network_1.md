### [Net Core 网络编程](#top) :grey_exclamation: <b id="top"></b>
`我们来使用.NET 进行网络通信吧` [`API 查询通道`](https://docs.microsoft.com/dotnet/api/) :white_check_mark: 

----
- [x] [`1.网络编程介绍`](#intro)
- [x] [`2.HttpClient`](#httpc)


-----


##### :octocat: [1.网络编程介绍](#top) <b id="intro"></b> 
`我们讨论如何使用网络协议创建客户端和服务器,从最简单的实例开始,阐明怎么样给服务端发送请求和在响应中存储信息,创建HTTP服务器,使用TCP,UDP，如
何利用Socket,记住所有的代码底层都是跑的协议`

* `命名空间:`
  * `System.Net` 
  * `System.Net.Http` 
  * `System.Net.Sockets` 

##### :octocat: [2.HttpClient](#top) <b id="httpc"></b>
[`HttpClient`](https://docs.microsoft.com/zh-cn/dotnet/api/system.net.http.httpclient?view=netframework-4.7.2) 
`提供基本类，用于发送 HTTP 请求和接收来自通过 URI 确认的资源的 HTTP 响应。` `派生自 System.Net.Http.HttpMessageInvoker这个基类负责SendAsync方法
它是异步的,这样就可以编写一个完全异步的系统来调用Web服务了`

* `HttpClint  是线程安全的,所以一个HttpClinet对象可以用于处理多个请求,每一个HttpClinet实例都有自己的线程池,所以对象之间的请求会被隔离,调用Dispose
方法释放资源。`


##### 构造函数 :speech_balloon: 
* `HttpClient()`:`初始化 HttpClient 类的新实例。`
* `HttpClient(HttpMessageHandler)`:`用特定的处理程序初始化 HttpClient 类的新实例。`
* `HttpClient(HttpMessageHandler, Boolean)`:`用特定的处理程序初始化 HttpClient 类的新实例。`

##### 属性 :speech_balloon: 
* `BaseAddress`
  * `获取或设置发送请求时使用的 Internet 资源的统一资源标识符 (URI) 的基址。`
* `DefaultRequestHeaders`
  * `获取与每个请求一起发送的标题。`
* `MaxResponseContentBufferSize`
  * `获取或设置读取响应内容时要缓冲的最大字节数。`
* `Timeout`
  * `获取或设置请求超时前等待的时间跨度。`
##### 方法 :speech_balloon: 
* `CancelPendingRequests()`
   * `取消该实例所有挂起的请求。`
* `DeleteAsync(String)/DeleteAsync(String, CancellationToken)` ----`线程安全`	:triangular_flag_on_post:
  * `以异步操作将 DELETE 请求发送给指定 URI。`/`用以异步操作的取消标记发送 DELETE 请求到指定的 URI。`








--------------------
`作者:` `KickGod` 
`完成时间`:`2019年1月10日13:20:50`
`备注信息`: `任意使用` 
