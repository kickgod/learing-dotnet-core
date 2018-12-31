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
`是对线程的一个很好的抽象,提供了很好的抽象,提供了数据和任务的并行性 Parallel定义了并行的 for()和forEach() 静态方法,循环从一个线程中运行,Parallel类使用多个任务,因此使用多个线程来完成这个任务！` 
* `for() Foach() 每次在迭代中调用`
* `静态方法 Invoke() 方法允许同时调用不同的方法用于任务的并行性`
* `静态方法 ForEach() 用于数据并行性 `
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

/*
Task 1 Start: 1
Task 3 Start: 5
Task 4 Start: 4
Task 2 Start: 3
Task 5 Start: 7
Task 4 End: 4
Task 6 Start: 4
Task 3 End: 5
Task 7 Start: 5
Task 9 Start: 6
Task 10 Start: 8
Task 1 End: 1
Task 8 Start: 10
Task 9 End: 6
Task 2 End: 3
Task 7 End: 5
Task 5 End: 7
Task 10 End: 8
Task 6 End: 4
Task 8 End: 10
*/
```
`上面执行十个任务 开了七个线程,说明不一定一个任务就是一个线程 线程也可以被不同的任务复用`


#####  :octocat: [3.title1](#top) <b id="target1"></b> 





--------------------
`作者:` `KickGod` 
`完成时间`:`2018年12月31日18:33:38`
`备注信息`: `任意复制` 
