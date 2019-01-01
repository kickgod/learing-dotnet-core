### [Net Core 任务/多线程同步](#top) :grey_exclamation: <b id="top"></b>
`这里将会说明如何在多个线程之间同步的问题,你别看了,这是超级重要的`:fire: [`官网资料`](https://docs.microsoft.com/zh-cn/dotnet/standard/threading/)

------

- [x] [`1.说明一下问题的关键性`](#target1)
- [x] [`2.争用条件`](#target2)
- [x] [`3.争用条件 加锁`](#target3)
- [x] [`4.死锁`](#target4)
- [x] [`5.Interlocked`](#target5)
- [x] [`6.Semaphore 信号量机制`](#target6)
- [x] [`7.Timer 类`](#target7)

------

#####  :octocat: [1.说明一下问题的关键性](#top) <b id="target1"></b> 
`只要你敢在多线程之间共享数据,那么就一定并且肯定会发生线程同步的问题,除非你不在多个线程之间使用共享数据,当然这是不可能的,所以我们还是好好学吧！线程同步
的目的是什么？ 他和线程异步 并行的 并发有什么区别` <br/>
`线程之间如果不考虑同步,可能出现争用条件和死锁问题,一个不注意就发生各种问题！   学到这里,你一定要好好看看集合内容中的`,:fire: **`并发集合`** 
##### `重点概念`
:star: [**`线程同步的目的是`: `保证一次只有一个线程访问和改变共享状态[数据]` `线程同步相对于共享状态而言的`** ](#top)

##### 线程的主要问题
* `争用条件`:`此线程争用的变量,在另一个线程充当 条件,例如 if else的条件变量,修改后,导致一些列问题`
* `陷入死锁`:`两个和多个线程锁定相互依赖的共享状态,都等着对方释放共享状态,产生死锁`


#####  :octocat: [2.争用条件](#top) <b id="target2"></b> 
`争用条件就是`：`一个线程 执行到 if(_state == 5 ) 然后 发现 _state == 5那么 进入if 内部,然后这时另一个 线程也 执行到 _state 发现也等于5
, 然后_state ++.前面那个线程 也 ++ 。最后到断言处发现  我去 _state == 7 怎么回事儿？ 然后报错了。`
```c#
class StateObject
{
    private int _state = 5;

    public void ChangeState(int loop) {
        if (_state == 5) {
            _state++;
            Trace.Assert(_state == 6, $"Race Condition occerred {loop} loops");
        }
        _state = 5;
    }

}

class SampleTask
{

    public static void Main(String[] args) {
        var state = new StateObject();

        Parallel.For(0, 3, (index) =>
        {
            SampleTask sample = new SampleTask();
            sample.RaceCondition(state);
        });

    }

    public void RaceCondition(object o) {
        Trace.Assert(o is StateObject ,"parame must is StateObject Type");

        var state = o as StateObject;

        int i = 0;

        while (true) {
            state.ChangeState(i++);
        }

    }
}
```
`我大约循环了6293此 然后才报断言错误,CPU数量越多越容易报错！如果你运行了很久都没有报错,那么可以增大线程数量 单核CPU 因为线程调度是抢占式的,也
会出现这种问题,只是没有那么频繁`
```c#
//增加线程数量 加大发生错误的纪律
Parallel.For(0, 6, (index) =>
{
    SampleTask sample = new SampleTask();
    sample.RaceCondition(state);
});
```



#####  :octocat: [3.争用条件 加锁](#top) <b id="target3"></b> 
`那么怎么办呢！ 我们加锁,一个线程访问的时候,加锁，进制其他线程访问,其他线程只有等解锁后才能争用！`
```c#
public void RaceCondition(object o) {
  Trace.Assert(o is StateObject ,"parame must is StateObject Type");

  var state = o as StateObject;

  int i = 0;

  while (true) {
      lock (state) {
          state.ChangeState(i++);
      }

  }

}
```
`这样的化每一次 while循环 访问到的 state 都是对其他线程禁止的 只有跳出 lock{} 之后也就是 解除锁定之后才能够争用！`
##### 注意
`lock` `只能作用在引用类型上面,对于那些 int long float 是不能加锁的,这个时候我们可以采用替代方法 `
```c#
class StateObject
{
    private int _state = 5;

    private object sync = new object(); //我们对他加锁  来间接锁定 _state
    public void ChangeState(int loop) {
        lock (sync) {
            if (_state == 5)
            {
                Console.WriteLine($"Run it {Thread.CurrentThread.ManagedThreadId}");
                _state++;
                Trace.Assert(_state == 6, $"Race Condition occerred {loop} loops");
            }
            _state = 5;
        }
    }
}
```
`锁的本质什么？ 并不是共享状态,而是通知,并非共享状态才能做通知,其他的任意引用变量都可以的！`
#####  :octocat: [4.死锁](#top) <b id="target4"></b> 
`过多的锁也会有麻烦，在死锁中至少有两个线程被挂起,并等待对方解除锁定,由于两个线程都在等待对方,就出现了死锁,线程将无限等待下去`

```c#
public voie DeadLock1(){
  int i = 0;
  while(true){
    lock(_s1){
        lock(_s2){
          ....//一些操作 
        }
    }
  }
}

public voie DeadLock2(){
  int i = 0;
  while(true){
    lock(_s2){
        lock(_s1){
          ....//一些操作 
        }
    }
  }
}
```
[`当我们在两个线程调用这个两个方法,那么很容易 一个线程锁住 _s1 一个线程锁定 _s2 陷入死锁`](#top)

##### lock 语句和线程安全
`C# 为多线程提供了lock 关键字。lock语句是设置锁定和解除锁定的一种简单方式`
* `谨记`:`lock 语句只能锁定引用类型,锁定值类型这是锁定了一个副本,是没有意义的！`

```c#
lock (sync) {
    if (_state == 5)
    {
        Console.WriteLine($"Run it {Thread.CurrentThread.ManagedThreadId}");
        _state++;
        Trace.Assert(_state == 6, $"Race Condition occerred {loop} loops");
    }
    _state = 5;
}
```
#####  :octocat: [5.Interlocked](#top) <b id="target5"></b> 
`Interlocked类用于使变量的简单语句原子化,i++ 是线程不安全的,它的操作包括从内存中获取一个值,给改值递增1 然后再把它存储回内存,这
些操作都可能会被线程调度器打断,InterLock类提供了以线程安全的方式递增，递减,交换和读取值的方法,与其他同步进行来说,使用 InterLock 会快很多。
`
##### `使用方法`
* `但是他只用于简单的同步问题`
```c#
lock(this){
   if(_someState == null){
        _someState = newState
   }
}

...


lock(obj){
  return ++ obj._state;
}


```
```c#
Interlocked.CompareExchange<SomeState>(ref someState,newState,null)

Interlocked.Increment(ref _state);
```
#####  :octocat: [6.Semaphore 信号量机制](#top) <b id="target6"></b> 
`信号量机制用来处理什么？ 比如如果 系统有三个物理端口可用,但是有7个线程需要访问IO端口,这信号量的使用就是 我先让三个线程使用IO端口,其他四个线程陷入
等待，等待前三个线程释放资源！释放一个资源,吧可用资源 +1 然后其他线程争用,每次被使用一个就 减少1 如此循环`
* [`Semaphore`](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.semaphore?view=netframework-4.7.2) 
`和` [`SemaphoreSlim`](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.semaphoreslim?view=netframework-4.7.2) `是C# 提供的使用信号量机制的类`
* `System.Threading.Semaphore 类表示一个命名（系统范围内）或本地信号量。 它是环绕 Win32 信号量对象的精简包装器。
Win32 信号量是计数信号量，该可用于控制对资源池的访问。`
* `SemaphoreSlim 类表示一个轻量、快速的信号量，可在等待时间预计很短的情况下用于在单个进程内等待  
SemaphoreSlim 尽可能多地依赖公共语言运行时 (CLR) 提供的同步基元。 但是，它还提供延迟初始化、基于内核的等待句柄，
作为在多个信号量上进行等待的必要支持。 SemaphoreSlim 也支持使用取消标记，但不支持命名信号量或使用用于同步的等待句柄。`

```c#
static void Main(String[] args) {
    int taskCount = 6; //有多少个任务/线程
    int semaphoreCount = 3; //资源只有三个

    var semaphore = new SemaphoreSlim(semaphoreCount, semaphoreCount);//初始化数量 和最大的资源数量

    var tasks = new Task[taskCount];

    for (var i = 0; i < taskCount; i++) {
        tasks[i] = Task.Run(()=>{
            TaskMain(semaphore);
        });
    }

    Task.WaitAll(tasks);
    Console.WriteLine("All task Fininsh");
}

public static void TaskMain(SemaphoreSlim semaphore) {
    bool isCompleted = false;

    while (!isCompleted) {
        // 如果没有那么我等600 毫秒
        if (semaphore.Wait(600))
        {
            try
            {
                Console.WriteLine($"{Task.CurrentId} lock the semaphore");
                Task.Delay(2000).Wait();
            }
            finally
            {
                //释放资源
                Console.WriteLine($"{Task.CurrentId} Release the semaphore");
                semaphore.Release();
                isCompleted = true;
            }
        }
        else {

            Console.WriteLine($"Timeout for task {Task.CurrentId} ; wait again!");
        }

    }

}



/*
1 lock the semaphore
3 lock the semaphore
2 lock the semaphore
Timeout for task 4 ; wait again!
Timeout for task 5 ; wait again!
Timeout for task 6 ; wait again!
Timeout for task 5 ; wait again!
Timeout for task 4 ; wait again!
Timeout for task 6 ; wait again!
Timeout for task 4 ; wait again!
Timeout for task 5 ; wait again!
Timeout for task 6 ; wait again!
3 Release the semaphore
2 Release the semaphore
5 lock the semaphore
4 lock the semaphore
1 Release the semaphore
6 lock the semaphore
6 Release the semaphore
4 Release the semaphore
5 Release the semaphore
All task Fininsh
*/
```

#####  :octocat: [7.Timer 类](#top) <b id="target7"></b> 
`使用计数器,可以重复调用方法,System.Threading。Timer`
[`Timer API 信息`](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.timer?view=netframework-4.7.2#%E6%9E%84%E9%80%A0%E5%87%BD%E6%95%B0)



--------------------
`作者:` `模板` 
`完成时间`:`2018年12月31日18:33:38`
`备注信息`: `禁止转载` 
