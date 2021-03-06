### [ASP.NET Core 中间件 与 服务注册   ](#top) :grey_exclamation: <b id="top"></b>
`中间件和服务注册`:white_check_mark:

------

- [x] [`1.中间件`](#target1)
- [x] [`2.配置中间件`](#target2)
    -  [`2.1 内置中间件`](#innermid)
- [x] [`3.注册一个服务吧`](#target3)

------

#####  :octocat: [1.中间件](#top) <b id="target1"></b> 
`中间件是一种独立的系统软件或服务程序，分布式应用软件借助这种软件在不同的技术之间共享资源 ASP.NET Core使用中间件完成功能 
在 Asp.net core 中 中间件是一种装配到应用管道以处理请求和响应的软件。每个组件：1.选择是否将请求传递到管道中的下一个组件。2.可在调用管道中的下一个组件前后执行工作。` 
[`ASP.NET Core 官方 中间件解释手册`](https://docs.microsoft.com/zh-cn/aspnet/core/fundamentals/middleware/index?view=aspnetcore-2.2)

* `MVC 就是一种中间件`
`定义 使用MVC 中间件,那么整个 ASP.NET Core 就是 MVC模式了`
```c#
app.UseMvc(routes =>
{
    routes.MapRoute(
        name: "default",
        template: "{controller=Home}/{action=Index}/{id?}");
});
```
`中间件就是 挨个 得到请求信息 做出自己的处理 或者信息的获取`

----

![`zjj`](https://docs.microsoft.com/zh-cn/aspnet/core/fundamentals/middleware/index/_static/request-delegate-pipeline.png?view=aspnetcore-2.2)

`对于Asp.net core 中间件的流程就是 将用户请求传递的管道 web 服务接受一个 用户请求,首先可以将请求传递给 Logger 中间件记录 请求信息日志 处理完毕后
Logger 中间件又把请求信息对象 传递给 授权中间件 授权中间件判别用户权限后  再将请求传递给 路由中间件  路由中间件 分析路由然后将请求交给特定的方法处理得到
得到相应 HttpResponse 响应结果可能是 JSON,XML,JSON,图片,视频等等资源类型 然后响应再通过路由中间件 传递给授权中间件 再到 Logger 中间件 最后发送给
用户。`

----

![`zjj`](./resources/middleware.png)


##### 系统默认配置的中间件
`以下 Startup.Configure 方法将为常见应用方案添加中间件组件：`
* `异常/错误处理`
* `HTTP 严格传输安全协议`
* `HTTPS 重定向`
* `静态文件服务器`
* `Cookie 策略实施`
* `身份验证`
* `会话`
* `MVC`
```c#
public void Configure(IApplicationBuilder app)
{
    //当应用程序在开发环境中运行时：
    if (env.IsDevelopment())
    {
        //使用Developer Exception页面报告应用程序运行时错误。
        //使用Database Error Page报告数据库运行时错误。
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        //当应用程序未在开发环境中运行时：
        //启用异常处理程序中间件以捕获异常
        //抛出以下中间件。
        //使用HTTP严格传输安全协议（HSTS）
        //中间件
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    //使用HTTPS重定向中间件将HTTP请求重定向到HTTPS。
    app.UseHttpsRedirection();

    //返回静态文件并结束管道。
    app.UseStaticFiles();

    //使用Cookie策略中间件符合欧盟通用数据
    //保护条例（GDPR）规定。
    app.UseCookiePolicy();

    //在用户访问安全资源之前进行身份验证。
    app.UseAuthentication();

    //如果应用程序使用会话状态，请在Cookie之后调用会话中间件
    //策略中间件和MVC中间件之前。
    app.UseSession();

    //将MVC添加到请求管道。并配置默认路由
    app.UseMvc(routes =>
    {
        routes.MapRoute(
            name: "default",
            template: "{controller=Home}/{action=Index}/{id?}");
    });
}
```


#####  :octocat: [2.配置中间件](#top) <b id="target2"></b> 
`IApplicationBuilder app 这个接口是用来精确配置 中间件的 一个初始化的 ASP.NET Core Web 项目模式只是配置了两个中间件`
`app.Run是一个非常简单的中间件 默认配置的 context 是 HttpContext 就是 请求内容 床底然后 简单的响应一个 Hello World! 所有的请求Url路径
都返回 Hello World!文本  要想让 用户可以请求其他资源 我们需要使用其他的 中间件处理用户请求 ` 
* `IApplicationBuilder  用于 创建中间件管道`
```c#
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }

    //我们自己来写一个中间件 中间件 返回的内容是一个 task 类型的 参数为 HttpContext
    app.Use(next =>
    {
        return async httpContext =>
        {
            if (httpContext.Request.Path.StartsWithSegments("/first"))
            {
                await httpContext.Response.WriteAsync("First!!!! text Hello 你好啊");
            }
            else
            {
                await next(httpContext);
            }
        };
    });

    app.Run(async (context) =>
    {
        await context.Response.WriteAsync("Hello World!");
    });
}
```
`自己实现一个中间件 ... next 参数是RequestDelegate 委托`
<br/>
#####  :octocat: [2.1 内置中间件](#top) <b id="innermid"></b> 
[`官方 内置中间件 手册 可以查看中间件详情内容`](https://docs.microsoft.com/zh-cn/aspnet/core/fundamentals/middleware/?view=aspnetcore-2.2#built-in-middleware)

|`中间件名称`|`说明`|`中间件顺序`|
|:----|:----|:----|
|`CORS`|`配置跨域资源共享.`|`在使用 CORS 的组件之前。`|
|`身份验证`|`提供身份验证支持`|`在需要 HttpContext.User 之前。 OAuth 回叫的终端。`|
|`HTTPS 重定向`|`将所有 HTTP 请求重定向到 HTTPS（ASP.NET Core 2.1 或更高版本）。`|`在使用 URL 的组件之前。`|
|`静态文件`|`为提供静态文件和目录浏览提供支持`|`如果请求与文件匹配，则为终端。`|
|`WebSockets`|`启用 WebSockets 协议`|`在接受 WebSocket 请求所需的组件之前`|
|`会话`|`提供对管理用户会话的支持`|`在需要会话的组件之前`|
|`MVC`|`用 MVC/Razor Pages 处理请求（ASP.NET Core 2.0 或更高版本）。`|`如果请求与路由匹配，则为终端。`|

 	 	
#####  :octocat: [3.注册一个服务吧](#top) <b id="target3"></b> 
`先看结果你就懂了 服务 其实就是一个接口,需要做的就是 把接口对应一个实现者,对的 就是依赖注入。` 
[`官方依赖注入服务手册`](https://docs.microsoft.com/zh-cn/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-2.2)


##### [框架提供的服务](https://docs.microsoft.com/zh-cn/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-2.2#framework-provided-services)
`asp.net core 框架提供了很多的服务可供使用,日志服务 路由 Http 请求....` 
* **`注意`**:` NetCore 2.0之后 程序默认自带 依赖注入框架 Ninject .netstandard2.0 程序集为 Microsoft.Extensions.DependencyInjection.dll。所以可以通过注册服务完成依赖注入  而不再需要其他的依赖注入框架`



|`服务`|`说明`|`使用方式`|
|:-----|:------|:------|
|[`Microsoft.AspNetCore.Hosting.IHostingEnvironment`](https://docs.microsoft.com/zh-cn/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment?view=aspnetcore-2.2)|`获得系统环境 比如引用环境 web路径`|`使用依赖注入可以获得`|
|[`Microsoft.Extensions.Logging.ILogger<T>`](https://docs.microsoft.com/zh-cn/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.2)|`日志记录 他有许多的方法 可以做日志记录`|`依赖注入 T 为使用在那个类中`|

`我们要在 注册服务`
```c#
public void ConfigureServices(IServiceCollection services)
{

}
```
##### 服务的生存周期
`为每个注册的服务选择适当的生存期。 可以使用以下生存期配置 ASP.NET Core 服务：` [`理解依赖注入三种生命周期`](https://www.cnblogs.com/gdsblog/p/8465101.html)
* `暂时`:`暂时生存期服务是每次请求时创建的。 这种生存期适合轻量级、 无状态的服务。`  
   * `例子 services.Transient<IOperationTransient, Operation>()`
   
* `作用域（Scoped）`:`作用域生存期服务以每个请求一次的方式创建。`
   *  `例子 services.AddScoped<IMyDependency, MyDependency>()`
* `单例`:`单一实例生存期服务是在第一次请求时（或者在运行 ConfigureServices 并且使用服务注册指定实例时）创建的。 每个后续
请求都使用相同的实例。 如果应用需要单一实例行为，建议允许服务容器管理服务的生存期。 不要实现单一实例设计模式并提供用户代码来
管理对象在类中的生存期。`
   *  `services.Singleton<IOperationTransient, Operation>() `

```
在中间件内使用有作用域的服务时，请将该服务注入至 Invoke 或 InvokeAsync 方法。 请不要通过构造函数注入进行注入，因
为它会强制服务的行为与单一实例类似。 有关更多信息，请参见ASP.NET Core 中间件。
```

```c#
public interface ILoginInfoService
{
    String Message();
}

public class LoginInfoService:ILoginInfoService
{
    public String Message(){
        return "这是我自己配置的服务哟";
    }
}
```
`在 startup 类中 注册并使用`
```c#
public class Startup
{

    public void ConfigureServices(IServiceCollection services)
    {
       services.AddSingleton<ILoginInfoService,LoginInfoService>(); //依赖注入 注册服务
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env,ILoginInfoService info)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        String info_ = info.Message();


        app.Run(async (context) =>
        {
            context.Response.ContentType = "text/plain;charset=utf-8"; //指定编码格式 不然可能乱码
            await context.Response.WriteAsync(info_);
        });
    }
}

```
`dotnet run 运行它 然后你会发现 文字变了`
##### 使用日志服务 
`依赖注入进去就行  几乎在 asp.net core 的每一个地方都支持依赖注入`
```c#
public class Startup
{

    public void ConfigureServices(IServiceCollection services)
    {
       services.AddSingleton<ILoginInfoService,LoginInfoService>();
    }

    public void Configure(
     IApplicationBuilder app,
     IHostingEnvironment env,
     ILoginInfoService info,
     ILogger<Startup> log)
    {

        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        String info_ = info.Message();

        app.Use(next =>
        {
            log.LogInformation("it run start in  app.Use");
            return async httpContext =>
            {
                if (httpContext.Request.Path.StartsWithSegments("/first"))
                {
                    log.LogInformation("it run start in  app.Run url: /first");
                    httpContext.Response.ContentType = "text/plain;charset=utf-8";
                    await httpContext.Response.WriteAsync("First!!!! text Hello 你好啊");
                }
                else
                {
                    log.LogInformation("next middleware ---->");
                    await next(httpContext);
                }
            };
        });


        app.Run(async (context) =>
        {
            log.LogInformation("it run start in  app.Run");
            context.Response.ContentType = "text/plain;charset=utf-8";
            await context.Response.WriteAsync(info_);
        });
    }
}

```

--------------------
`作者:` `KickGod` 
`完成时间`:`2019年1月19日11:57:18`
`备注信息`: `任意使用` 
