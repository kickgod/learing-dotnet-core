### [NetCore 异步编程](#top) :grey_exclamation: <b id="top"></b>
`NET Core 异步编程很强的哟,Net Core提供了非常舒服的方式用于异步编程,当然先把基础学了再说`:white_check_mark: 
[`官方文档`](https://docs.microsoft.com/zh-cn/dotnet/standard/async)

---
> - [x] [`1.搞清楚异步和多线程`](#know) 
> - [x] [`2.异步编程介绍`](#intro) 
>   - [`依赖命名空间`](#using)
> - [x] [`3.普通的异步模式`](#async)
> - [x] [`4.基于事件的异步模式`](#event)

----
#####  :octocat: [1.搞清楚异步和多线程](#top) <b id="know"></b>
`异步和多线程有什么区别？其实，异步是目的，而多线程是实现这个目的的方法。`
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
#####  :octocat: [4. 基于事件的异步模式](#top) <b id="event"></b> 
[`官网资料`](https://docs.microsoft.com/zh-cn/dotnet/standard/asynchronous-programming-patterns/event-based-asynchronous-pattern-eap)
* `基于事件的异步模式具有多线程应用程序的优点，同时隐藏了多线程设计中固有的许多复杂问题。 使用支持此模式的类，你将能够：`
    * `“在后台”执行耗时任务（例如下载和数据库操作），但不会中断你的应用程序。`
    * `同时执行多个操作，每个操作完成时都会接到通知。`
    * `等待资源变得可用，但不会停止（“挂起”）你的应用程序。`
    * `使用熟悉的事件和委托模型与挂起的异步操作通信。 若要详细了解如何使用事件处理程序和委托，请参阅事件。`
* `支持基于事件的异步模式的类将具有一个或多个命名为 MethodNameAsync 的方法。 这些方法可能会创建同步版本的镜像，这些同步版本会在当前线程上执行相同的操作。 该类还可能具有 MethodNameCompleted 事件，并且可能会具有 MethodNameAsyncCancel（或只是 CancelAsync）方法。`
###### 简单解释一下
`PictureBox 是一个支持基于事件的异步模式的典型组件。 你可以通过调用其 Load 方法来同步下载图像，但是如果图像很大，或者网络连接很慢，应用程序将停止（“挂起”），直到下载操作完成并且对 Load 的调用返回后才会继续执行。`
<br/>
`如果你想要应用程序在加载图像时保持运行，你可以调用 LoadAsync 方法，处理 LoadCompleted 事件，这与你处理任何其他事件一样。 调用 LoadAsync 方法时，你的应用程序将继续运行，而下载操作将在另一个线程上（“在后台”）继续。 图像加载操作完成时，将调用你的事件处理程序，并且你的事件处理程序可以检查 AsyncCompletedEventArgs 参数以确定该下载是否已成功完成。`
###### 多用于 winFrom 那么懂它就行了
