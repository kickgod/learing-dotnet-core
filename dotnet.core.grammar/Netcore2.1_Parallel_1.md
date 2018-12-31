### [Net Core 任务与并行编程](#top) :grey_exclamation: <b id="top"></b>
:white_check_mark: `并行编程的目的是为了应对多线程中的数据共享,资源占用,争夺,进行线程之间同步的一种机制`

------

- [x] [`简单说明一下`](#target1)
- [x] [`Parallel`](#target2)
- [ ] [`title3`](#target3)

------

#####  :octocat: [1.简单说明一下](#top) <b id="target1"></b> 
`这里的API 可以完成许多的线程并行要求,但是如果需要更多控制并行性,例如设置优先级,那么就需要回到.NET3.5 的时代,使用Thread...`
* `涉及的命名空间`
  * `using System.Threading.Tasks;`

#####  :octocat: [2.Parallel](#top) <b id="target1"></b> 
`是对线程的一个很好的抽象,提供了很好的抽象,可以完成`
```c#
public static void Main(String[] args) {
    Parallel.For(0, 5, index =>
    {
        Console.WriteLine($"{index} Start: {Thread.CurrentThread.ManagedThreadId }");
        Task.Delay(100).Wait();
        Console.WriteLine($"{index} End: {Thread.CurrentThread.ManagedThreadId }");
    });

    Console.ReadKey();
}
```
#####  :octocat: [3.title1](#top) <b id="target1"></b> 
...




--------------------
`作者:` `模板` 
`完成时间`:`2018年12月31日18:33:38`
`备注信息`: `禁止转载` 
