### [NetCore 异步编程](#top) :grey_exclamation: <b id="top"></b>
`NET Core 异步编程很强的哟,Net Core提供了非常舒服的方式用于异步编程,当然先把基础学了再说`:white_check_mark: 
[`官方文档`](https://docs.microsoft.com/zh-cn/dotnet/standard/async)

---
> - [x] [`1.搞清楚异步和多线程`](#know) 
> - [x] [`2.异步编程介绍`](#intro) 
>   - [`依赖命名空间`](#using)


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
* `普通的异步模式`
* `基于事件的异步模式`
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
#####  :octocat: [1.2 异步编程命名空间](#top) <b id="using"></b> 
* `System.Threading`
* `System.Threading.Tasks;`
