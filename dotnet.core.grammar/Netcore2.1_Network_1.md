### [Net Core 网络编程](#top) :grey_exclamation: <b id="top"></b>
`我们来使用.NET 进行网络通信吧` [`API 查询通道`](https://docs.microsoft.com/dotnet/api/) :white_check_mark: 

----
- [x] [`1.网络编程介绍`](#intro)
- [x] [`2.HttpClient`](#httpc)
- [x] [`3.HttpResponseMessage`](#httprm)
- [x] [`4.HttpContent`](#httpct)
- [x] [`5.HttpCompletionOption 枚举`](#httpcpm)

-----

##### :octocat: [1.网络编程介绍](#top) <b id="intro"></b> 
`我们讨论如何使用网络协议创建客户端和服务器,从最简单的实例开始,阐明怎么样给服务端发送请求和在响应中存储信息,创建HTTP服务器,使用TCP,UDP，如
何利用Socket,记住所有的代码底层都是跑的协议`

* `命名空间:`
  * `System.Net` 
  * `System.Net.Http` 
  * `System.Net.Sockets` 

##### Uri 类
`提供统一资源标识符 (URI) 的对象表示形式和对 URI 各部分的轻松访问`

* `构造：Uri(String)`:`用指定的 URI 初始化 Uri 类的新实例。`

```c#
Uri siteUri = new Uri("http://www.contoso.com/");

//Uri 此外可以用于表示本地文件系统路径
Uri uri1 = new Uri("C:/test/path/file.txt") // Implicit file path.
Uri uri2 = new Uri("file:///C:/test/path/file.txt") // Explicit file path
```
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
  
* `DeleteAsync(Uri) / DeleteAsync(Uri, CancellationToken)` ----`线程安全`	:triangular_flag_on_post: 	
  * `以异步操作将 DELETE 请求发送给指定 URI。`/`用以异步操作的取消标记发送 DELETE 请求到指定的 URI。`
* `GetAsync(String) / GetAsync(String, CancellationToken) 	` 	----`线程安全 String 可以换成 Uri`	:triangular_flag_on_post: 	
  * `以异步操作将 GET 请求发送给指定 URI。` / `用以异步操作的取消标记发送 GET 请求到指定的 URI。`
* `GetAsync(String, HttpCompletionOption)` 	----`线程安全 String可以换成 Uri`	:triangular_flag_on_post: 	
  * `用以异步操作的 HTTP 完成选项发送 GET 请求到指定的 URI。` ----`线程安全 String可以换成 Uri`	:triangular_flag_on_post: 	
* `GetAsync(String, HttpCompletionOption, CancellationToken)`
* `GetByteArrayAsync(String)` 	 ----`线程安全 String可以换成 Uri`	:triangular_flag_on_post: 	
  * `将 GET 请求发送到指定 URI 并在异步操作中以字节数组的形式返回响应正文。`
* `GetStreamAsync(String)` ----`线程安全 String可以换成 Uri`	:triangular_flag_on_post: 	
  * `将 GET 请求发送到指定 URI 并在异步操作中以流的形式返回响应正文。`
* `PostAsync(String, HttpContent) / PostAsync(String, HttpContent, CancellationToken) 	` 	----`线程安全 String可以换成 Uri`	:triangular_flag_on_post: 	
  * `以异步操作将 POST 请求发送给指定 URI。`/`用以异步操作的取消标记发送 POST 请求。`

```c#
SendAsync(HttpRequestMessage) 	以异步操作发送 HTTP 请求。

SendAsync(HttpRequestMessage, CancellationToken) 	以异步操作发送 HTTP 请求。

SendAsync(HttpRequestMessage, HttpCompletionOption) 	以异步操作发送 HTTP 请求。

SendAsync(HttpRequestMessage, HttpCompletionOption, CancellationToken) 	以异步操作发送 HTTP 请求。
```

`Get 请求天气预报`
```c#
public async Task GetDataFromInternal() {
   using (var client = new HttpClient())
   {
       String Url = 
       @"http://v.juhe.cn/weather/index?cityname=%E6%88%90%E9%83%BD&
       dtype=json&format=1&key=9837f47b1ea204dfb671cfc4c2d039c6";
       HttpResponseMessage httpResponse = await client.GetAsync(Url);//获取成都市的天气预报
       //如果请求成功
       if (httpResponse.IsSuccessStatusCode)
       {
           Console.WriteLine($"Response Result Status Code: {httpResponse.StatusCode}");
           Console.WriteLine($"ContentType: {httpResponse.Content.Headers.ContentType }");
           String content = await httpResponse.Content.ReadAsStringAsync();
           Console.WriteLine($"Content: {content} ");    
       }
   }
}

//结果:
Hello World!
Response Result Status Code: OK
ContentType: application/json; charset=utf-8
Content: {"resultcode":"200","reason":"successed!","result":{"s......
```
##### 设置HTTP 头部 :speech_balloon: 
`通过这个DefaultRequestHeaders属性 允许设置或改变标题，使用Add方法可以给集合添加标题,设置后标题和值就会与这个HttpClient对象发生的每个请求一起发送`

##### [HttpRequestHeaders Class ](https://docs.microsoft.com/zh-cn/dotnet/api/system.net.http.headers.httprequestheaders?view=netframework-4.7.2)
`此类能设置的是请求 HTTP 标头信息`

`属性`:[`1.HTTP 标头字段参考手册`](https://blog.csdn.net/echizao1839/article/details/81028448) [`2.HTTP 标头字段参考手册`](http://tools.jb51.net/table/http_header)

- [x] `Accept 	获取 HTTP 请求的 Accept 标头值。`：`text/plain, text/html`

- [x] `AcceptCharset 	获取 HTTP 请求的 Accept-Charset 标头值。`
- [x] `AcceptEncoding 	获取 HTTP 请求的 Accept-Encoding 标头值。`
- [x] `AcceptLanguage 	获取 HTTP 请求的 Accept-Language 标头值。`
- [x] `Authorization 	获取或设置 HTTP 请求的 Authorization 标头值。`
- [x] `CacheControl 	获取或设置 HTTP 请求的 Cache-Control 标头值。`
- [x] `Connection 	获取 HTTP 请求的 Connection 标头值。`
- [x] `ConnectionClose 	获取或设置一个值，该值表明 HTTP 请求的 Connection 标头是否含有 Close。`
- [x] `Date 	获取或设置 HTTP 请求的 Date 标头值。`
- [x] `Expect 	获取 HTTP 请求的 Expect 标头值。`
- [x] `ExpectContinue 	获取或设置一个值，该值指示 HTTP 请求的 Expect 标头是否含有 Continue。`
- [x] `From 	获取或设置 HTTP 请求的 From 标头值。`
- [x] `Host 	获取或设置 HTTP 请求的 Host 标头值。`
- [x] `IfMatch 	获取 HTTP 请求的 If-Match 标头值。`
- [x] `IfModifiedSince 	获取或设置 HTTP 请求的 If-Modified-Since 标头值。`
- [x] `IfNoneMatch 	获取 HTTP 请求的 If-None-Match 标头值。`
- [x] `IfRange 	获取或设置 HTTP 请求的 If-Range 标头值。`
- [x] `IfUnmodifiedSince 	获取或设置 HTTP 请求的 If-Unmodified-Since 标头值。`
- [x] `MaxForwards 	获取或设置 HTTP 请求的 Max-Forwards 标头值。`
- [x] `Pragma 	获取 HTTP 请求的 Pragma 标头值。`
- [x] `ProxyAuthorization 	获取或设置 HTTP 请求的 Proxy-Authorization 标头值。`
- [x] `Range 	获取或设置 HTTP 请求的 Range 标头值。`
- [x] `Referrer 	获取或设置 HTTP 请求的 Referer 标头值。`
- [x] `TE 	获取 HTTP 请求的 TE 标头值。`
- [x] `Trailer 	获取 HTTP 请求的 Trailer 标头值。`
- [x] `TransferEncoding 	获取 HTTP 请求的 Transfer-Encoding 标头值。`
- [x] `TransferEncodingChunked 	获取或设置一个值，该值表明 HTTP 请求的 Transfer-Encoding 标头是否含有 chunked。`
- [x] `Upgrade 	获取 HTTP 请求的 Upgrade 标头值。`
- [x] `UserAgent 	获取 HTTP 请求的 User-Agent 标头值。`
- [x] `Via 	获取 HTTP 请求的 Via 标头值。`
- [x] `Warning 	获取 HTTP 请求的 Warning 标头值。`

`方法`

- [x] `Add(String, IEnumerable<String>)` :`添加指定的标头及其值到 HttpHeaders 集合中。`

- [x] `Add(String, String)`:`添加指定的标头及其值到 HttpHeaders 集合中。`
- [x] `GetEnumerator()`:` 	返回可循环访问 HttpHeaders 实例的枚举器。`
- [x] `Remove(String)`:`从 HttpHeaders 集合中移除指定的标头。(Inherited from HttpHeaders) `
- [x] `Clear()`:`从 HttpHeaders 集合中移除所有标头。`
- [x] `Contains(String)`:`在 HttpHeaders 集合中存在特定标头时返回。`
- [x] `GetValues(String)`:` 	返回存储于 HttpHeaders 集合中的指定标头的所有标头值。`

```c#
HttpResponseMessage httpResponse = await client.GetAsync(Url);

client.DefaultRequestHeaders.Add("Header-Name", "JxKicker");
client.DefaultRequestHeaders.Add("Accept","application/json");

IEnumerator<KeyValuePair<String, IEnumerable<String>>> enumerator = 
                                    client.DefaultRequestHeaders.GetEnumerator();
//返回你所有设置过的HTTP 标头值
while(enumerator.MoveNext())
{
    String value = "";
    foreach(var val in enumerator.Current.Value)
    {
        value += $"{val};";
    }
    Console.WriteLine( $"{enumerator.Current.Key}: {value}");
}
```

##### :octocat: [3.HttpResponseMessage](#top) <b id="httprm"></b> 
`表示包括状态代码和数据的 HTTP 响应消息。` [`官方API`](https://docs.microsoft.com/zh-cn/dotnet/api/system.net.http.httpresponsemessage?view=netframework-4.7.2)


##### 构造函数 :speech_balloon: 
* `HttpResponseMessage()`:`初始化 HttpResponseMessage 类的新实例。`
* `HttpResponseMessage(HttpStatusCode)`:`初始化指定的 HttpResponseMessage 的 StatusCode 类的新实例。`

##### 属性 :speech_balloon: 
* `Content :HttpContent`:`获取或设置 HTTP 响应消息的内容。` [`HttpContent:表示 HTTP 实体正文和内容标头的基类。`](https://docs.microsoft.com/zh-cn/dotnet/api/system.net.http.httpcontent?view=netframework-4.7.2) 
* `Headers`:`获取 HTTP 响应标头的集合。`
* `IsSuccessStatusCode`:`获取一个值，该值指示 HTTP 响应是否成功。`
* `ReasonPhrase`:`获取或设置通常由服务器发出的原因短语（与状态代码一起发出）。`
* `RequestMessage`:`获取或设置导致此响应消息的请求消息。·
* `StatusCode`:`获取或设置 HTTP 响应的状态代码。`
* `Version`:` 	获取或设置 HTTP 消息版本。`

##### 方法 :speech_balloon: 
* `EnsureSuccessStatusCode()`:`如果 HTTP 响应的 IsSuccessStatusCode 属性是 false，则引发异常。`

##### :octocat: [4.HttpContent](#top) <b id="httpct"></b>
`表示 HTTP 实体正文和内容标头的基类。`
> `HttpResponseMessage的Content 属性里面存储着访问内容它的类型是 HttpContent` `那么怎么获得内容呢？`

##### 构造函数 :speech_balloon: 
* `HttpContent()`: `初始化 HttpContent 类的新实例。`

##### 属性 :speech_balloon: 
* `Headers`:`获取 RFC 2616 中定义的 HTTP 内容标头。`

##### 方法
* `ReadAsByteArrayAsync()`
  * `将 HTTP 内容序列化到字节数组，此为异步操作。`
  
* `ReadAsStreamAsync()`
  * `将 HTTP 内容序列化并返回将内容表示为异步操作的流。`
* `ReadAsStringAsync()`
  * `将 HTTP 内容序列化到字符串，此为异步操作。`
* `LoadIntoBufferAsync()`
  * `以异步操作方式将 HTTP 内容序列化到内存缓冲区。`
* `LoadIntoBufferAsync(Int64)`
  * `以异步操作方式将 HTTP 内容序列化到内存缓冲区。`
* `CreateContentReadStreamAsync()`
  * `采用异步操作将 HTTP 内容序列化到内存流。`
* `CopyToAsync(Stream)` 	
  * `将 HTTP 内容序列化为字节流，并将其复制到作为 stream 参数提供的流对象。`
* `CopyToAsync(Stream, TransportContext)` 	

  * `将 HTTP 内容序列化为字节流，并将其复制到作为 stream 参数提供的流对象。`

* `TryComputeLength(Int64)` 	
  * `确定 HTTP 内容是否具有有效的长度（以字节为单位）。`


##### :octocat: [5.HttpCompletionOption 枚举](#top) <b id="httpcpm"></b>
`指示 HttpClient 操作是在响应可利用时立即视为已完成，还是在读取包含上下文的整个答案信息之后才视为已完成。`

|`值` |`说明`|
|:-----|:-----|
|`ResponseContentRead`|`操作应在阅读包括该内容的整个响应之后完成`|
|`ResponseHeadersRead`|`响应一可用且标题可读时即应完成的操作。 尚未读取的内容。`|
 	 	











--------------------
`作者:` `KickGod` 
`完成时间`:`2019年1月10日13:20:50`
`备注信息`: `任意使用` 
