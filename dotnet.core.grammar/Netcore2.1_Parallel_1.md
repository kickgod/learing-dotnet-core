### [Net Core 任务与并行编程](#top) :grey_exclamation: <b id="top"></b>
:white_check_mark: `并行编程的目的是为了应对多线程中的数据共享,资源占用,争夺,进行线程之间同步的一种机制`

------

- [x] [`1.简单说明一下`](#target1)
- [x] [`2.Parallel`](#target2)
- [x] [`3.提前停止 Parallel.For()`](#target3)
- [x] [`4.Parallel.For 的初始化`](#target4)
- [x] [`5.Parallel.ForEach() 方法循环`](#target5)
- [x] [`6.Parallel.Invoke()`](#target6)
- [x] [`7.启动任务`](#target7)

------

#####  :octocat: [1.简单说明一下](#top) <b id="target1"></b> 
`这里的API 可以完成许多的线程并行要求,但是如果需要更多控制并行性,例如设置优先级,那么就需要回到.NET3.5 的时代,使用Thread...`
* `涉及的命名空间`
  * `using System.Threading.Tasks;`

#####  :octocat: [2.Parallel](#top) <b id="target2"></b> 
`是对线程的一个很好的抽象,提供了很好的抽象,提供了数据和任务的并行性 Parallel定义了并行的 for()和forEach() 静态方法,循环从一个线程中运行,Parallel类使用多个任务,因此使用多个线程来完成这个任务！` 
* `For() ForEach() 每次在迭代中调用`
* `静态方法 Invoke() 方法允许同时调用不同的方法用于任务的并行性`
* `静态方法 ForEach() 用于数据并行性 `

##### 循环一下
* `下面的代码我们完成一个循环，为什么要这样做呢！你想想你平时用 for循环的时候 它是怎么运行的,普通的 for(var index =0;index < end;index++) 它是单线程运行的,也就是 执行了对 index =0 的操作后再执行 index = 1 的操作也就是说  0 操作没有完成,就不能够执行 index =1 的操作。`
* `有时候我们可能 后一个index 的任务并不依赖于 前一个index的任务,那么为什么我们不能并行操作呢？ 启动多个线程来完成一个循环,特别是面对循环次数特别多的时候 这就是 parallel的 For() ForEach() 由来`
```c#
public static void Main(String[] args) {
    Parallel.For(0, 10, index =>
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

##### 实现一个累计
`常规就是 从第一个加到最后一个的 for循环,但是你仔细想一想,那个先加那个后加,真的是你需要关心的吗？,我们最终的目的只是让所有的数都累加到
一个结果上面而已,这才是目的`
```c#
public static Int32[] list = {50,50,23,10,26,15,89,100,12,10 };

public static void Main(String[] args) {

  int sum = 0;

  Parallel.For(0, list.Length, index =>
  {
    Console.WriteLine($"Task {index +1 } {list[index]}  Start: {Thread.CurrentThread.ManagedThreadId }");
    sum += list[index];
    Console.WriteLine($"Task {index +1 } {list[index]}");
  });

  Console.WriteLine(sum);
  Console.ReadKey();
}
/*
Task 1 50  Start: 1
Task 1 50
Task 4 10  Start: 1
Task 3 23  Start: 5
Task 3 23
Task 6 15  Start: 5
Task 6 15
Task 7 89  Start: 5
Task 7 89
Task 8 100  Start: 5
Task 8 100
Task 9 12  Start: 5
Task 9 12
Task 10 10  Start: 5
Task 10 10
Task 2 50  Start: 3
Task 4 10
Task 5 26  Start: 4
Task 5 26
Task 2 50
385
*/
```
`意思一共使用了 四个线程  10个任务,任务之间是可以复用线程的哟,比你自己去操作线程要方便多了,如果自己开启线程可能会导致,线程过多卡死,线程复用的原因是
线程池！类似于连接池一样的东东`
* `For 方法会等待他创建的任务完成,但是不等待其他后台活动`

#####  :octocat: [3.提前停止Parallel.For()](#top) <b id="target3"></b> 
`有时候我们需要提前中断 Parallel.For,而不是要完成所有迭代，For方法的一个重载版本 接受了 Action<int,ParallelLoopState> 类型的第三个参数`
* `ParallelLoopState 方法`
  * `Break()`:`结束了`
  * `Stop()`
```c#
public static Int32[] list = {50,50,23,10,26,15,89,100,12,10 };

public static void Main(String[] args) {

    int sum = 0;

    Parallel.For(0, list.Length, (index,ParallelLoopState) =>
    {
        Console.WriteLine($"Task {index +1 } {list[index]} 
              Start: {Thread.CurrentThread.ManagedThreadId }");

        if (index > 4)
        {
            ParallelLoopState.Break();
            Console.WriteLine("End Parallel");
        }
        else {
            sum += list[index];
        }
        Console.WriteLine($"Task {index +1 } {list[index]}");
    });
    Console.WriteLine("It Run Here");
    Console.WriteLine(sum);
    Console.ReadKey();
}
```
#####  :octocat: [4.Parallel.For 的初始化](#top) <b id="target4"></b> 
`For 方法通过创建数个线程来执行循环,如果需要对每个线程进行初始化,就可以使用 Parallel<TLocal>() 方法.除了from和to对应的值之外,For方法的泛型版本
还接受三个委托参数。`
* `第一个参数的类型是 Func<TLocal> 返回TLocal类型的值` 
* `第二参数 委托 Func<int,ParallelLoopState,TLocal,TLocal> 返回 TLocal类型的值 和 Parallel<TLocal>()类型对应`
* `第三个参数 是用来接受 Func<TLocal>(TLocal) TLocal是 Func<int,ParallelLoopState,TLocal,TLocal> 的返回值`
```c#
public static void Main(String[] args) {

    Parallel.For<String>(0, 10, () =>
    {
        var index = Thread.CurrentThread.ManagedThreadId;
        Console.WriteLine($"init Thread { index }");
        return $"ThreadID {index}";
    },
    (index, ParallelLoopState, str) =>
    {
        Console.WriteLine($"{index} I am Task{Task.CurrentId}  Body ");
        Task.Delay(20).Wait();
        return $"index {index}";
    },
    (str) =>
    {
        Console.WriteLine($"finally: {str} Task:{Task.CurrentId}
           Thread:{Thread.CurrentThread.ManagedThreadId}");
    }
    );

}
```
#####  :octocat: [5.Parallel.ForEach() 方法循环](#top) <b id="target5"></b>
`使用Parallel.ForEach() 方法便利 IEnumrable的集合,其使用方式类似与 foreach，差别在于 它是基于异步方式便利,而foreach 是同步的单线程的`
```c#

public static String[] data = {"kicker","Tome","Twkc", "Goer", "Javak", "Mike", "Qitance", "Lutesu", "mitus" };

public static void Main(String[] args) {
    //直接循环版本
    Parallel.ForEach(data, item =>
    {
        Console.WriteLine(item.ToLower());
    });
    //需要所有 和取消参数 版本
    Parallel.ForEach(data, (item,ParallelLoopState,index) =>
    {
        Console.WriteLine(index);
        if (item.ToUpper() == "MITUS") {
            Console.WriteLine($"{item.ToLower()} {index}");
            ParallelLoopState.Stop();
        }

    });

}
```
#####  :octocat: [6.Parallel.Invoke()](#top) <b id="target6"></b>
`如果多个任务将要并行运行,可以使用Invoke() 方法它提供了任务的并行性模式,方法允许传递一个Action委托的数组`
* `方法参数`:`parames Action action[]`
```c#
public static String[] data = {"kicker","Tome","Twkc", "Goer", 
     "Javak", "Mike", "Qitance", "Lutesu", "mitus" };

public static void Main(String[] args) {
    Parallel.Invoke(getNameList,ForEr); //parames Action action[]
}
public static void getNameList() {
    foreach (var val in data) {
        Console.WriteLine(val);
    }
}

public static void ForEr() {
    Int32 Sum = 0;
    for (Int32 i = 0; i < 10; i++) {
        Console.WriteLine(Sum);
        Sum += i;
    }
    Console.WriteLine(Sum);
}
```
#####  :octocat: [7.启动任务](#top) <b id="target7"></b>
`为了更好的控制并行操作,可以使用Task类，表示将要完成的某个工作单元,这个工作单元可以在线程中运行,也可以以同步的方式启动一个任务,这需要等待主调线程
。使用任务不仅仅可以获得一个抽象层,还可以对底层线程进行很多控制。`

##### 启动任务
`启动任务,有两种方式,1.直接启动一个任务,2 通过线程池启动任务,线程池是很有用的,在多线程里面`
* `通过线程池启动任务 它会自动管理线程，根据需求增加或者减少线程池的线程数量,线程池中的线程用完后,任然返回线程池`
  * `TaskFactory`:`通过实例访问 或者通过Task的静态属性 访问 StartNew 都可以`
  ```c#
    var tf = new TaskFactory();
    Task t1 = tf.StartNew(getNameList);
    Task t2 = Task.Factory.StartNew(ForEr);
  ```
##### `Task 类`
* `RunSynchronsly() 方法将会使得Task 同步运行 也就是不适用多线程,直接使用方法 在主线程运行`

##### Future --任务的结果
`我们将一些会返回结果的任务叫做 Feture 她会将一些有用 的内容信息写道内存对象中去,这个内存对象必须是线程安全的, 他的API为` `Task<TResult>`
`TResult 可以是任意类型,如果你觉得它无法满足你,那么你可以使用元祖,或者使用对象`

##### 创建任务选项 `TaskCreationOptions`
|`值`|`说明`|
|:---|:----|
|`None`|`指定应使用默认行为`|
|`PreferFairness`|`以一种尽可能公平的方式安排任务，这意味着较早安排的任务将更可能较早运行，而较晚安排运行的任务将更可能较晚运行。`|
|`LongRunning`|` 指定任务将是长时间运行的、粗粒度的操作，涉及比细化的系统更少、更大的组件。它还将提示任务计划程序：该任务需要附加线程，以使任务不阻塞本地线程池队列中其他线程或工作项的向前推动。`|
|`AttachedToParent`|`指定将任务附加到任务层次结构中的某个父级`|
|`DenyChildAttach`|`指定任何尝试作为附加的子任务执行的子任务都无法附加到父任务，会改成作为分离的子任务执行。`|
|`HideScheduler`|` 防止环境计划程序被视为已创建任务的当前计划程序。 这意味着像 StartNew 或 ContinueWith 创建任务的执行操作将被视为 System.Threading.Tasks.TaskScheduler.Default`|
|`RunContinuationsAsynchronously`|`强制异步执行添加到当前任务的延续任务。`|

```c#
Task t2 = Task.Factory.StartNew(ForEr,32,TaskCreationOptions.PreferFairness);
```

##### 任务的层次
`一个任务启动另一个任务,那就启动了一个父子的层次结构。`

##### 一些方法的区别
* `Task.Wait() 等待任务的结束 可以不在 没有 async 标记的方法里面使用 有标记可以使用 await`
* `Task.WaitAll()`:`等待所有任务的结束,他可以在没有 async 标记的方法里面使用 WhenAll 也是一样的但是 WhenAll 会返回一个任务 从而运行 async修饰`
* `Task.Delay()`:`指定这个方法返回的任务完成前要等待的毫秒数`
* `Task.Yield()`:`释放CPU 让其他任务运行 没有其他任务 那么当前任务立即执行` 


--------------------
`作者:` `KickGod` 
`完成时间`:`2018年12月31日18:33:38`
`备注信息`: `任意复制` 
