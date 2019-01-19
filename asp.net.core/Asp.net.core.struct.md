### [Asp.Net.Core 项目配置](#top) :grey_exclamation: <b id="top"></b>
`项目结构和配置信息,然我们来看看我们新建的项目的结构为什么会成为这个样子.`:white_check_mark:

------

- [x] [`1.appsettings.json`](#target1)
- [x] [`2.Program`](#target2)
- [x] [`3.Startup`](#target3)

------

#####  :octocat: [1.appsettings.json](#top) <b id="target1"></b> 
`概括`:`项目配置Json 文件代替 Web.config`
* `通过依赖注入可以获得Json文件内容`
```c#
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*",
  "Message": "hello World My Life" 
}
```
`获取 Message 的信息`
```c#
public class HomeController : Controller
{
    private readonly IConfiguration _conf;

    public HomeController(IConfiguration configuration)
    {
        _conf = configuration;
    }

    public IActionResult Index()
    {
        ViewBag.Message = _conf["Message"];
        return View();
    }
 }
```

#####  :octocat: [2.Program 类](#top) <b id="target2"></b> 
```c#
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run(); //构建 web 服务
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>(); //使用 Startup 类来配置 web服务
}
```
`CreateWebHostBuilder 默认配置的内容`:
*  `使用Kestrel Web 服务器是 ASP.NET Core 内置的跨平台的 也可以配置IIS集成 netcore2.2 开启了这个配置的在源码
中集成了`
```
UseIIS(),UseIISIntegration()
```
* `Log`:`记录日志`
* `IConfiguration 接口`:`依赖注入可以获得 系统的配置信息`
   * `IConfiguration配置信息来源`
      * `appsetting.json`
      * `User Secrets`
      * `环境变量`
      * `命令行参数`
```c#
public static IWebHostBuilder CreateDefaultBuilder(string[] args)
{
  IWebHostBuilder hostBuilder = new WebHostBuilder().UseKestrel((Action<WebHostBuilderContext, KestrelServerOptions>) ((builderContext, options) => options.Configure((IConfiguration) builderContext.Configuration.GetSection("Kestrel")))).UseContentRoot(Directory.GetCurrentDirectory()).ConfigureAppConfiguration((Action<WebHostBuilderContext, IConfigurationBuilder>) ((hostingContext, config) =>
  {
    IHostingEnvironment hostingEnvironment = hostingContext.HostingEnvironment;
    config.AddJsonFile("appsettings.json", true, true).AddJsonFile(string.Format("appsettings.{0}.json", (object) hostingEnvironment.EnvironmentName), true, true);
    if (hostingEnvironment.IsDevelopment())
    {
      Assembly assembly = Assembly.Load(new AssemblyName(hostingEnvironment.ApplicationName));
      if (assembly != (Assembly) null)
        config.AddUserSecrets(assembly, true);
    }
    config.AddEnvironmentVariables();
    if (args == null)
      return;
    config.AddCommandLine(args);
  })).ConfigureLogging((Action<WebHostBuilderContext, ILoggingBuilder>) ((hostingContext, logging) =>
  {
    logging.AddConfiguration((IConfiguration) hostingContext.Configuration.GetSection("Logging"));
    logging.AddConsole();
    logging.AddDebug();
  })).ConfigureServices((Action<WebHostBuilderContext, IServiceCollection>) ((hostingContext, services) =>
  {
    services.PostConfigure<HostFilteringOptions>((Action<HostFilteringOptions>) (options =>
    {
      if (options.AllowedHosts != null && options.AllowedHosts.Count != 0)
        return;
      string str = hostingContext.Configuration["AllowedHosts"];
      string[] strArray1;
      if (str == null)
        strArray1 = (string[]) null;
      else
        strArray1 = str.Split(new char[1]{ ';' }, StringSplitOptions.RemoveEmptyEntries);
      string[] strArray2 = strArray1;
      HostFilteringOptions filteringOptions = options;
      string[] strArray3;
      if (strArray2 == null || strArray2.Length == 0)
        strArray3 = new string[1]{ "*" };
      else
        strArray3 = strArray2;
      filteringOptions.AllowedHosts = (IList<string>) strArray3;
    }));
    services.AddSingleton<IOptionsChangeTokenSource<HostFilteringOptions>>((IOptionsChangeTokenSource<HostFilteringOptions>) new ConfigurationChangeTokenSource<HostFilteringOptions>(hostingContext.Configuration));
    services.AddTransient<IStartupFilter, HostFilteringStartupFilter>();
  })).UseIISIntegration().UseDefaultServiceProvider((Action<WebHostBuilderContext, ServiceProviderOptions>) ((context, options) => options.ValidateScopes = context.HostingEnvironment.IsDevelopment()));
  if (args != null)
    hostBuilder.UseConfiguration((IConfiguration) new ConfigurationBuilder().AddCommandLine(args).Build());
  return hostBuilder;
}
```
`你会发现源码是这样写的 Program 中的  WebHost.CreateDefaultBuilder(args) 方法源码如上所示`
* `先配置Json文件`:`config.AddJsonFile("appsettings.json", true, true).AddJsonFile(string.Format("appsettings.{0}.json", (object) hostingEnvironment.EnvironmentName), true, true);`
* `再配置了系统环境变量`:`config.AddEnvironmentVariables(); `
* `再配置了命令行参数`:`config.AddCommandLine(args);`
* `后配置的如果重复配置某一个信息 那么后面覆盖前面 这就是配置信息的优先级`


#####  :octocat: [3.Startup](#top) <b id="target3"></b> 
`配置服务,中间件管道等等....`
* `让我们先从 asp.net core web 说起`
```node
//IServiceCollection 通过Asp.net core 框架自动依赖注入 可以在其中配置服务 或者依赖注入
public void ConfigureServices(IServiceCollection services)
{

}
//方法参数都是依赖注入的
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // 你会发现你的页面写错了 浏览器页面会报错各种代码异常信息 
    // 这就是下面四行代码的作用 
    // 对于程序开发过程中 捕获异常并且暂时在错误页面
    // IHostingEnvironment 可以自动获取系统运行环境 环境一般有开发环境  和 生产环境 预览环境 isStatging
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();//这是一个中间件
    }

    app.Run(async (context) =>
    {
        await context.Response.WriteAsync("Hello World!");
    });
}
```

--------------------
`作者:` `JxKicker` 
`完成时间`:`2019年1月18日23:40:07`
`备注信息`: `任何使用` 
