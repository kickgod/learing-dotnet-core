### [NetCore 异步编程](#top) :grey_exclamation: <b id="top"></b>
`NET Core 异步编程很强的哟,Net Core提供了非常舒服的方式用于异步编程,当然先把基础学了再说,核心 异步只是多线程使用方式的一种,异步的核心是： 多个线程之间没有任何关系,相互之间没有执行顺序依赖,也不依赖另一个线程的执行结果`:white_check_mark: 
[`官方文档`](https://docs.microsoft.com/zh-cn/dotnet/standard/async)

---
> - [x] [`1.搞清楚异步和多线程`](#know) 
> - [x] [`2.异步编程介绍`](#intro) 
>   - [`依赖命名空间`](#using)
> - [x] [`3.普通的异步模式`](#async)
> - [x] [`4.基于事件的异步模式 EAP`](#event)
> - [x] [`5.基于任务的异步模式 TAP [掌握它]`](#task)
----
#####  :octocat: [1.搞清楚异步和多线程](#top) <b id="know"></b>
`异步和多线程有什么区别？其实，异步是目的，而多线程是实现这个目的的方法。`  [`请看完概念再看异步编程`](https://github.com/kickgod/learing-dotnet-core/blob/master/dotnet.core.grammar/Netcore2.1_Thread_1.md)
> `异步是说，A发起一个操作后（一般都是比较耗时的操作，如果不耗时的操作就没有必要异步了），可以继续自顾自的处理它自己的事儿，不用干
等着这个耗时操作返回。.Net中的这种异步编程模型，就简化了多线程编程，我们甚至都不用去关心Thread类，就可以做一个异步操作出来。`
* `多线程和异步的相同：都可以解决线程阻塞，响应慢的问题。`
* `多线程和异步的不同：`
  * `异步线程可以减少共享变量，减少死锁可能，异步操作无须额外的线程负担。但采用回调方式复杂度较高，切不易调试。多线程程中的处理程
  序依然是顺序执行，编程相对简单而且易理解，但编程简单本质上是进程中一段并发运行的代码，需要操作系统投入CPU资源来运行和调度，
，线程的使用（滥用）会给系统带来上下文切换的额外负担。并且线程间的共享变量可能造成死锁的出现。`
* `异步适用于IO操作（文件，网络数据修改，数据库操作、Web Service、HttpRequest以及.Net Remoting等跨进程的调用）`
* `多线程适用于需要长时间CPU运算的场合（例如耗时较长的图形处理和算法执行） 例如 编译器 浏览器之类的桌面应用程序 大型计算应用程序`
* `可以使用线程来执行耗时较长的I/O操作。只适用于只有少数几个并发操作的时候，如果需要处理大量的并发操作时就不合适。因为大量线程
可能会导致死机卡死,而异步却可以避免`

```node
0-1————————————————————————————————————————————————–2-3

从点 0 到 1 所花费时间很长，直到异步方法将控制让步于其调用方才结束。
从点 1 到点 2 所用时间是花费在 I/O 上的时间，且 CPU 没有耗时。
最后，点 2 到点 3 所花费时间用于将控制（和可能的值）传递回异步方法，此时将再次执行。
```

#####  :octocat: [2.异步编程介绍](#top) <b id="intro"></b>
`使用异步编程,防止单线程调用阻塞,Net Core支持三种不同的异步编程方法`
* [`普通的异步模式`](https://docs.microsoft.com/zh-cn/dotnet/standard/asynchronous-programming-patterns/asynchronous-programming-model-apm)
* [`基于事件的异步模式`](https://docs.microsoft.com/zh-cn/dotnet/standard/asynchronous-programming-patterns/event-based-asynchronous-pattern-eap)
* [`基于任务 并使用 awiat async 关键字的异步模式[TAP]`](https://docs.microsoft.com/zh-cn/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap)

[`为什么要使用异步呢？`:`比如你做一个网络请求,中间可能需要经过网络等待！不可等在等待的时候 其他的什么事情就不做吧 这个时候你就需要多线程`](#top)
* `1.新型应用广泛使用文件和网络 I/O。 默认情况下 I/O API 一般会阻塞，导致糟糕的用户体验和硬件利用率`
`请求网络Json 数据 结果:--- 输出为: 先输出循环 再输出 json数据`
```c#
static void Main(string[] args)
{
    StartRun();
    Console.ReadKey();
}
/// <summary>
///  从网络请求一个json 数据下来
/// </summary>
static async void StartRun()
{

    HttpClient clint = new HttpClient();
    String url = "http://v.juhe.cn/xianxing/citys?dtype=&key=1b31c23cdb9c33d876e9a55c87bffcc7";
    String json = await clint.GetStringAsync(url);

    for (int i = 10; i < 25; i++) {
        //假设循环是正需要做的其他事情
        Console.WriteLine(i);
    }

    Console.WriteLine(json);

    Console.WriteLine("Task Finish");
    Console.ReadKey();
}
```
`执行方式:` <br/>
`系统执行到 await clint.GetStringAsync(url);做一个事件,等到网络请求完毕,提示StartRun 方法 接受数据 然后继续向下执行直到
下面的程序执行换了,再去查看 await clint.GetStringAsync(url); 是否执行完毕 如果执行完毕 那么就获得 请求的网络数据 否则就继续等待网络数据`
#####  :octocat: [2.2 异步编程命名空间](#top) <b id="using"></b> 
* `System.Threading`
* `System.Threading.Tasks;`
#####  :octocat: [3. 普通的异步模式----Dotnet core 并不支持这样古老的模式 了解就好 Net Framework 可以运行](#top) <b id="async"></b> 
* `使用 IAsyncResult 设计模式的异步操作是通过名为 BeginXXX 和 EndXXX 的两个方法来实现的，这两个方法分别开始和结束异步操作 XXX。 例如， FileStream 类提供 BeginRead 和 EndRead 方法来从文件异步读取字节。 这两个方法实现了 Read 方法的异步版本。`
* `在调用 BeginXXX 后，应用程序可以继续在调用线程上执行指令，同时异步操作在另一个线程上执行。 每次调用 BeginXXX 时，应用程序还应调用对应 EndXXX 来获取操作的结果。`
##### [`IAsyncResult `](https://docs.microsoft.com/zh-cn/dotnet/api/system.iasyncresult?view=netframework-4.7.2)
`BeginOperationName 方法开始异步操作 OperationName，并返回实现 IAsyncResult 接口的对象。 IAsyncResult 对象存储有关异步操作的信息。 下表显示有关异步操作的信息。`

|`成员`|`说明`|
|:----|:----|
|`AsyncState`| `一个特定于应用程序的可选对象，其中包含有关异步操作的信息。`|
|`AsyncWaitHandle`| `	一个 WaitHandle ，可用来在异步操作完成之前阻止应用程序执行。`|
|`CompletedSynchronously`| 	`一个值，指示异步操作是否是在用于调用 BeginOperationName 的线程上完成，而不是在单独的 ThreadPool 线程上完成。`|
|`IsCompleted`| 	`一个值，指示异步操作是否已完成。`|

`异步模式的优点在于委托,所有的委托类型都定义了Invoke 方法 还有它的异步方法 BeginInvoke EndInvoke 也就是说明 基本上所有
的委托都可以实现异步方法`
```c#
static void Main(string[] args)
{
    Console.WriteLine("Start World!");
    //给委托赋予一个方法
    Func<int, int> a = (val) =>
    {
        Console.WriteLine($"input value:{val}");
        return val % 5;
    };
    // 开始执行方法
    // 倒数第二个参数 为回调函数 有一个参数 IAsyncResult 为线程状态信号作为参数填充到 IAsyncResult的
    // AsyncState 属性中
    IAsyncResult ar = a.BeginInvoke(93, null, null);

    //主线程不断检测 委托线程的执行状态是否完成
    //
    while (ar.IsCompleted == false)
    {
        Thread.Sleep(10);
        Console.WriteLine("等待中...");
    }
    //执行完毕后 执行EndInvoke 传递信号量,得到返回值
    var reval = a.EndInvoke(ar);
    Console.WriteLine($"Value： {reval}");
    Console.WriteLine("End World!");
    Console.ReadKey();
}
```
`BeginInvoke`:<br/>
* `第一个参数是调用work方法的参数`
* `第二个是回调函数`
* `第三个是需要传到回调函数里的参数可以是Object类型`
#####  :octocat: [4. 基于事件的异步模式 EAP](#top) <b id="event"></b> 
[`官网资料`](https://docs.microsoft.com/zh-cn/dotnet/standard/asynchronous-programming-patterns/event-based-asynchronous-pattern-eap)
* `基于事件的异步模式具有多线程应用程序的优点，同时隐藏了多线程设计中固有的许多复杂问题。 使用支持此模式的类，你将能够：`
    * `“在后台”执行耗时任务（例如下载和数据库操作），但不会中断你的应用程序。`
    * `同时执行多个操作，每个操作完成时都会接到通知。`
    * `等待资源变得可用，但不会停止（“挂起”）你的应用程序。`
    * `使用熟悉的事件和委托模型与挂起的异步操作通信。 若要详细了解如何使用事件处理程序和委托，请参阅事件。`
* `支持基于事件的异步模式的类将具有一个或多个命名为 MethodNameAsync 的方法。 这些方法可能会创建同步版本的镜像，这些同步版本会在当前线程上执行相同的操作。 该类还可能具有 MethodNameCompleted 事件，并且可能会具有 MethodNameAsyncCancel（或只是 CancelAsync）方法。`
###### 简单解释一下
* `PictureBox 是一个支持基于事件的异步模式的典型组件。 你可以通过调用其 Load 方法来同步下载图像，但是如果图像很大，或者网络连接很慢，应用程序将停止（“挂起”），直到下载操作完成并且对 Load 的调用返回后才会继续执行。`

*  `如果你想要应用程序在加载图像时保持运行，你可以调用 LoadAsync 方法，处理 LoadCompleted 事件，这与你处理任何其他事件一样。 调用 LoadAsync 方法时，你的应用程序将继续运行，而下载操作将在另一个线程上（“在后台”）继续。 图像加载操作完成时，将调用你的事件处理程序，并且你的事件处理程序可以检查 AsyncCompletedEventArgs 参数以确定该下载是否已成功完成。`
###### 多用于 winFrom 那么懂它就行了

#####  :octocat: [5. 基于任务的异步模式](#top) <b id="task"></b> 
`来吧 先不要看什么概念,上面已经看够了吧,那么我们直接开始写代码,通过代码学习异步编程！`
* `Task 表示不返回值的单个操作。`
* `Task<T> 表示返回 T 类型的值的单个操作。`
##### 1. 利用Task 完成一个异步操作 报名字
```c#
static void Main()
{
    Action<String> action = (name)=>{
        Console.WriteLine($"Name:{ name}");
    };

    Task.Run(()=> {
        action("Kicker");
    });

    Console.WriteLine($"In Run at Here");

    Console.ReadKey();
}
```
##### 2. 调用异步方法
`体验一下 说 Hello  这个调用异步方法的时候 不需要异步方法有任何的 返回值`
```c#
static void Main()
{
    CallerSaidNameAsync();
    Console.ReadKey();
}

static Task SaidNameAsync(String name) {
    return Task.Run(()=>
    {
        Console.WriteLine($"Name:{name}");
    });
}

static async void CallerSaidNameAsync() {
    SaidNameAsync("Kicker");
    Console.WriteLine($"In Run at Here");
}
//SaidNameAsync("kicker"); 可以到 Main 直接调用
```
* `async`:` 只能修饰用于返回.NET 类型的Task 或者 void 方法 已经Windows运行库的IAsyncOperation 它不能用于程序的入口点,即 Main 方法
不能使用 async 修饰 await 只能用于 返回Task的方法`
* `await`: `运算符应用于异步方法中的任务，在方法的执行中插入挂起点，直到所等待的任务完成。 任务表示正在进行的工作。仅可用于由 async 关键字修改的异步方法中`
```c#
static void Main()
{
    CallerSaidNameAsync();
    Console.WriteLine("我最先执行哟");
    Console.WriteLine("我继续执行");
    Console.ReadKey();
}

static Task SaidNameAsync(String name) {
    return Task.Run(()=>
    {
        Console.WriteLine($"Name:{name}");
    });
}

static async void CallerSaidNameAsync() {
    await SaidNameAsync("Zlm");
    await SaidNameAsync("Ajak");
    Console.WriteLine($"In Run at Here");
}
```
##### 连续任务
`Task 类定义了一个 ContinueWith 方法[还有泛型支持] ContinueWith<TResult> 它定义了任务完成后就调用的代码,指派给ContinueWith方法的委托接受已完成的任务作为参数传入,使用Result属性可以访问任务返回的结果`
```c#
static void Main()
{
    CallerGetGreat();
    Console.WriteLine("It Run Here!");
    Console.ReadKey();
}

static Task<Int32> GetGreatAsync(int Number) {
    return Task<Int32>.Run(() =>
    {
        return Number * Number * Number * 3 * 2 * 1;
    });
}
static async void CallerGetGreat() {

    Tuple<Boolean, Int32> calResult = await GetGreatAsync(10).ContinueWith<Tuple<Boolean,Int32> >( (t)=> {
        Int32 result = t.Result;
        if (result > Int32.MaxValue)
        {
            return Tuple.Create(true,result);
        }
        else {
          return Tuple.Create(false, result);
      }
    });
    Console.WriteLine($"是否越界:{calResult.Item1} 结果值:{calResult.Item2}");
}
```
##### 按照顺序调用异步方法
`在一个异步方法里面,可以调用一个或多个异步方法,如何编程代码,取决于一个异步方法的结果是否依赖于另一个异步方法`
`使用await 将会按照顺序调用异步方法 当然这也会产生性能降低 因为一旦使用了这个 await 异步就编程同步了`
```c#
static async void call()
{
    var rs1 = await GetGreatAsync(20);
    var rs2 = await GetGreatAsync(25);
    Console.WriteLine($"{rs1}, {rs2}");
}
```
`var rs2 =  GetGreatAsync(21).Result; 一旦你需要依赖异步方法的返回值的时候,那么那一部分代码就会同步执行,因为 方法有变量依赖于异步方法的返回值
,说白了 我的 rs1 不赋值 我怎么接着执行 所以我要等 rs1 赋值完了 我再执行下一条语句。`
```c#
static async void call()
{
    var rs1 = GetGreatAsync(20).Result;
    var rs2 = GetGreatAsync(25).Result;
    Console.WriteLine($"{rs1}, {rs2}");
}
```
##### 使用组合器
`如果异步方法不依赖于其他异步方法,那么完全可以不使用 await 关键字，而是把每一个异步方法的返回结果赋值给Task 变量` `WhenAll WhenAny`
* `WhenAll`:`返回的是所有任务执行完后的Task 一个数组`
* `WhenAny`:`其中一个传入方法的任务完成了,就会返回Task`
```c#
//WhenAll 我等你都执行完毕 再说
static async void call()
{
    Task<Int32> rs1 =  GetGreatAsync(20);
    Task<Int32> rs2 =  GetGreatAsync(21);
    Task<Int32> rs3 = GetGreatAsync(20);
    Task<Int32> rs4 = GetGreatAsync(21);
    Int32[] vals = await Task.WhenAll(rs1, rs2, rs3, rs4);
    // r1,r2,r3,r4 执行顺序任意
    Console.WriteLine($"{vals[0]}, {vals[1]}");
}
```
`WhenAny 的意思是 任意一个 方法可用于异步等待多个表示为要完成的任务的异步操作之一。`
```c#
static async void call()
{
    Task<Int32> rs1 =  GetGreatAsync(20);
    Task<Int32> rs2 =  GetGreatAsync(21);
    Task<Int32> rs3 = GetGreatAsync(20);
    Task<Int32> rs4 = GetGreatAsync(21);
    Task<Int32> vals = await Task.WhenAny(rs1, rs2, rs3, rs4);
    Console.WriteLine($"{vals.Result}");
}
```
##### 将异步模式转换为基于任务的异步模式
`为什么要转换呢? 因为有些类并没有实现基于任务的异步方法`
```c#
static Func<Int32, Int32> GetFunc = (num) =>
{
    return num * num * num * 3 * 2 * 1;
};

static IAsyncResult BeginGetSum(Int32 number, AsyncCallback callback, object state) {
    return GetFunc.BeginInvoke(number, callback, state);
}

static Int32 EndGetSum(IAsyncResult ar) {
    return GetFunc.EndInvoke(ar);
}

private static async void CovertAsyncToTask() {
    Int32 res = await Task<Int32>.Factory.FromAsync<Int32>(BeginGetSum, EndGetSum, 32, null);
}
```
* `对于新的开发项目，建议采用 TAP 作为异步设计模式。`
* `这与异步编程模型（APM 或 IAsyncResult）模式和基于事件的异步模式 (EAP) 不同，APM 要求使用 Begin 和 End 方法，而 EAP 需要具有 Async 后缀的方法，还需要一个或多个事件、事件处理程序委托类型和 EventArg 派生类型。 `
* ` TAP 中的异步方法在操作名称后面添加 Async 后缀；例如，Get 操作变为 GetAsync, 如果要将 TAP 方法添加到一个类中，而该类中已包含带有 Async 后缀的相同方法名称，请改用后缀 TaskAsync。 例如，如果类中已有 GetAsync 方法，请使用名称 GetTaskAsync。`
* `TAP 方法返回Task 或 Task<TResult>，具体取决于相应同步方法返回的是 void 还是类型 TResult。`
