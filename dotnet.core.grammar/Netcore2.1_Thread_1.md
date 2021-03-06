### [NetCore 多线程异步 ](#top) :grey_exclamation: <b id="top"></b>
:white_check_mark: `并行大话三万年` [`.NET 多线程发展简史`](https://www.cnblogs.com/MuNet/p/8545139.html) [`官方文档`](https://docs.microsoft.com/zh-cn/dotnet/standard/parallel-processing-and-concurrency)

---
> - [x] [`1.说说异步同步多线程和并发并行`](#know) 
> - [x] [`2.异步和多线程的区分`](#thread)
> - [x] [`3.高并发和多线程的关系`](#height)
> - [x] [`4.NET Core 中的并行处理、并发和异步编程`](#net)
> - [x] [`5.Thread 和 Task的区别`](#task)
> - [x] [`6.多线程`](#manythread)
> - [x] [`7.管道`](#pipe)
-----

> - [x] [`概念问答`](#exam)

----
#####  :octocat: [1.说说异步多线程和并发并行](#top) <b id="know"></b>
`很多人学习.NET 多线程 都很头晕,半天摸不着头脑,这大概是因为:`
* :one: `.NET 对于多线程编程推出了太多的API 和更新了数种异步编程的模式,.NET 太重视语言本身,而不类似于 Java 在Java的多线程中 学习内容少得多,他认为很多问题
不应该由编程语言本身来解决,而是应该由工程师借助更好的设计模式 和架构方法来解决问题,而微软更想帮你把问题解决了,在语法方面就避免一些多线程的问题,这导致了
多线程API 的急剧膨胀和学习成本的增高`
* :two: `但是虽然说微软写了很多的API 帮你解决问题,但是需要懂得概念,一个也不少,你任然要从头开始去了解多线程编程的每一点内容,和去明白微软为什么要这样设计,这就好像
自己写的代码都不想再看,看懂别人的代码,内心是何等的拒绝,还要明白微软为什么要这样设计？`
* :three: `多线程概念很多,容易混淆`

##### 多线程的概念
* :fast_forward:**`并发`**:`在操作系统中，是指一个时间段中有几个程序都处于已启动运行到运行完毕之间，且这几个程序都是在同一个处理机上运行。其中两种并发关系分别是同步和互斥`
* :fast_forward:**`并行`**:`在操作系统中，一组程序按独立异步的速度执行，无论从微观还是宏观，程序都是一起执行的。` <br/>
> * `1.来个比喻：并发和并行的区别就是一个人同时吃三个馒头和三个人同时吃三个馒头；[人:代指 CPU  馒头: 正在执行的任务]` <br/>

> * `2.在单CPU系统中，系统调度在某一时刻只能让一个线程运行，虽然这种调试机制有多种形式(大多数是时间片轮巡为主)，但无论如何，要
通过不断切换需要运行的线程让其运行的方式就叫并发(concurrent)。而在多CPU系统中，可以让两个以上的线程同时运行，这种可以同时让
两个以上线程同时运行的方式叫做并行(parallel)`

* :fast_forward:**`同步`**:`进程之间的关系不是相互排斥临界资源的关系，而是相互依赖的关系。进一步的说明：就是前一个进程的输出作为后一个进程的输
入，当第一个进程没有输出时第二个进程必须等待。具有同步关系的一组并发进程相互发送的信息称为消息或事件。`
* :fast_forward:**`异步`**:`异步和同步是相对的，同步就是顺序执行，执行完一个再执行下一个，需要等待、协调运行。异步就是彼此独立,在等待
某事件的过程中继续做自己的事，不需要等待这一事件完成后再工作。线程就是实现异步的一个方式。异步是让调用方法的主线程不需要同步
等待另一线程的完成，从而可以让主线程干其它的事情。`
> * `异步和同步的区别，  在io等待的时候，同步不会切走，浪费了时间。`
* :fast_forward:**`互斥`**:`进程间相互排斥的使用临界资源的现象，就叫互斥。`
* :fast_forward:**`多线程`**:`多线程是程序设计的逻辑层概念，它是进程中并发运行的一段代码。多线程可以实现线程间的切换执行。`

#####  :octocat: [2.异步和多线程的区分](#top) <b id="thread"></b>
`区分`: <br/>
* :arrow_lower_left: `异步和多线程并不是一个同等关系,异步是最终目的,多线程只是我们实现异步的一种手段。异步是当一个调用请求发送给被调用
者,而调用者不用等待其结果的返回而可以做其它的事情。实现异步可以采用多线程技术或则交给另外的进程来处理。`
* :arrow_lower_left: `多线程的好处，比较容易的实现了 异步切换的思想， 因为异步的程序很难写的。多线程本身程还是以同步完成，但是应该说比效率是比不上
异步的。 而且多线很容易写， 相对效率也高。`

-----
`线程的本质`<br/>
* `线程不是一个计算机硬件的功能，而是操作系统提供的一种逻辑功能，线程本质上是进程中一段并发运行的代码，所以线程需要操作系统投入CPU资源来运行和调度。`

----
`异步操作的优缺点`: <br/>
* :arrow_right_hook: `因为异步操作无须额外的线程负担，并且使用回调的方式进行处理，在设计良好的情况下，处理函数可以不必使用共享变量（即使无法完全
不用，最起码可以减少共享变量的数量），减少了死锁的可能。当然异步操作也并非完美无暇。编写异步操作的复杂程度较高，程序主要使用回
调方式进行处理，与普通人的思维方式有些初入，而且难以调试。`
* :arrow_right_hook: `多线程的优点很明显，线程中的处理程序依然是顺序执行，符合普通人的思维习惯，所以编程简单。但是多线程的缺点也同样明显，线程的
使用（滥用）会给系统带来上下文切换的额外负担。并且线程间的共享变量可能造成死锁的出现。`

#####  :octocat: [3.高并发和多线程的关系](#top) <b id="height"></b>
`高并发是短时间内大量访问和请求 --例如12306抢票和天猫双11活动`

* `想系统能够适应高并发状态，则需要全面优化优化，包括，硬件、网络、系统架构、开发语言的选取、数据结构的运用、算法优
化、数据库优化……而多线程只是其中解决方法之一。`
* `多线程在解决高并发问题中所起到的作用就是使计算机的资源在每一时刻都能达到最大的利用率，不至于浪费计算机资源使其闲置`

#####  :octocat: [4.NET Core 中的并行处理、并发和异步编程](#top) <b id="net"></b>
`C# 中开发中,不推荐直接使用多线程编程由开发人员自己直接开启线程来自己处理线程的并行异步资源,而是把多线程编写进一步细化为 异步编程,并行编
程 用于处理多线程 线程无关异步 线程有关并行 提供了专门的API`
* `异步编程`:`.NET 推荐 基于任务的异步模式 (TAP)（建议用于新开发） 异步编程和核心是  它是使用回调的方式进行处理的`
* `并行编程`:`.NET 推荐 任务并行库 (TPL) `

-----
`C# 对于异步编程具有很多的思考,设计了非常多的模式 推荐你一定要看 顶部的链接 .NET 多线程发展简史`

#####  :octocat: [5.Thread 和 Task的区别](#top) <b id="task"></b>
* `NET Framework中的线程分为两类：1.前台线程；2.后台线程。`
  * `前台线程`:`主线程必须等待这个线程结束完了,主线程才能够结束`
  * `后台线程`:`我主线程已经完了，那么我结束`

* `多线程的最老版本：Thread（好像是2.0的时候出的 ） 推荐使用新的Task 来进行多线程编程 也就是说 Task 代替了 Thread`
#####  :octocat: [6.多线程](#top) <b id="manythread"></b>
`多线程的优点：可以同时完成多个任务；可以使程序的响应速度更快；可以让占用大量处理时间的任务或当前没有进行处理的任务定期将处理时间让给别的任务；可以随时停止任务；可以设置每个任务的优先级以优化程序性能。`
* `为什么可以多线程执行呢？总结起来有下面两方面的原因：`
   * `1、CPU运行速度太快，硬件处理速度跟不上，所以操作系统进行分时间片管理。这样，从宏观角度来说是多线程并发的，因为CPU速度太快，察觉不到，看起来是同一时刻执行了不同的操作。但是从微观角度来讲，同一时刻只能有一个线程在处理。`
   * `2、目前电脑都是多核多CPU的，一个CPU在同一时刻只能运行一个线程，但是多个CPU在同一时刻就可以运行多个线程。然而，多线程虽然有很多优点，但是也必须认识到多线程可能存在影响系统性能的不利方面，才能正确使用线程。不利方面主要有如下几点：`
      * `（1）线程也是程序，所以线程需要占用内存，线程越多，占用内存也越多。`
      * `（2）多线程需要协调和管理，所以需要占用CPU时间以便跟踪线程。`
      * `（3）线程之间对共享资源的访问会相互影响，必须解决争用共享资源的问题。`
      * `（4）线程太多会导致控制太复杂，最终可能造成很多程序缺陷。`

#####  :octocat: [7.管道](#top) <b id="pipe"></b>
`管道是Linux支持的最初UnixIPC形式之一，具有以下特点：管道是半双工的，数据只能向一个方向流动；需要双方通信时，需要建立起两个管道；只能用于父子进程或者兄弟进程之间（具有亲缘关系的进程）；单独构成一种独立的文件系统：管道对于管道两端的进程而言，就是一个文件，但它不是普通的文件，它不属于某种文件系统，而是自立门户，单独构成一种文件系统，并且只存在与内存中。数据的读出和写入：一个进程向管道中写的内容被管道另一端的进程读出。写入的内容每次都添加在管道缓冲区的末尾，并且每次都是从缓冲区的头部读出数据。`
* [`System.IO.Pipes Namespace `](https://docs.microsoft.com/zh-cn/dotnet/api/system.io.pipes?view=netframework-4.7.2)

##### 管道的工作机制
> `管道就是指用于连接一个读进程和一个写进程,以实现它们之间通信的共享文件,又称pipe文件。Linux中实现了两种管道,一种是无名管道,一种是命名管道。无名管道没有磁盘节点,它仅作为一个内存对象存在,用完后就销毁了。因为没有文件名和路径,也没有磁盘节点,因此无名管道没有显式的打开过程,实际上它是在创建时就自动打开的,并且生成内存inode节点、dentry目录项对象和两个文件结构对象(一个读操作、一个写操作),其内存对象和普通文件的一致,所以读写操作使用的是同样的文件接口,当然读写函数是专用的。因为无名管道不能显式打开,因此只能由父子进程之间、兄弟进程之间或者其他有亲缘关系并且都继承了祖先进程的管道文件对象的两个进程间通信使用。命名管道是有文件名和磁盘i节点的,因此可由任意两个或多个进程间通信使用,它的使用方法和普通文件类似,都遵循打开、读、写、关闭这样的过程,但是读写的内部实现和普通文件不同,而和无名管道一样`


##### 匿名管道工作方式
> `管道以先进先出方式保存一定数量的数据。使用管道的时候一个进程从管道的一端写,另一个进程从管道的另一端读。在主进程中利用fork()函数创建一个子进程,这样父子进程同时拥有对同一管道的读写句柄,因为管道没有提供锁定的保护机制,所以必须决定数据的流动方向,然后在相应进程中关闭不需要的句柄。这样,就可以使用read()和write()函数来对它进行读写操作了。使用无名管道进行进程间通信的步骤概述如下: `
* `①创建所需的管道;`
* `②生成(多个)子进程;`
* `③关闭/复制文件描述符,使之与相应的管道末端相联系;`
* `④关闭不需要的管道末端;`
* `⑤进行通信活动;`
* `⑥关闭所有剩余的打开文件描述符`
* `⑦等待子进程结束。`

##### 命名管道工作方式
> `无名管道应用的一个重大限制是只能用于具有亲缘关系的进程间通信,在命名管道提出后,该限制得到了克服。命名管道提供一个路径名与之关联,以FIFO的文件形式存在于文件系统中。这样,即使与FIFO的创建进程不存在亲缘关系的进程,只要可以访问该路径,就能够彼此通过FIFO相互通信。因此,通过FIFO,不相关的进程也能交换数据。FIFO管道的打开方式与普通管道有所不同,普通管道包括两个文件数据结构:对应的VFS索引节点以及共享数据页,在进程每次运行时都会创建一次,而FIFO是一直存在的,需要用户打开和关闭。Linux必须处理读进程先于写进程打开管道、读进程在写进程写入数据之前读入这两种情况。除此之外,FIFO管道的使用方式与普通管道完全相同,都使用相同的数据结构和操作。`


##### 管道的不足
`管道提供了从一种进程向另一种进程传输数据的有效方法,但是,管道还是存在一些不足:`
* `①因为读数据的同时也将数据从管道移去,因此管道不能用来对多个接受者广播数据。`
* `②如果一个管道有多个读进程,那么写进程不能发送数据到指定的读进程。同样,如果有多个写进程,那么没有方法来判别是它们中的哪一个发送的数据。`



####  :octocat: [8.概念问答](#top) <b id="exam"></b>

##### 1.线程同步的目的是什么？和异步有什么区别！
> :one: `保证一次只有一个线程访问和改变共享状态[数据] 线程同步相对于共享状态而言的` <br/>

> :two:`线程异步,是指使用的多个线程之间没有任何关系,不需要调整执行顺序,不需要考虑状态共享问题,自己完成自己的任务！完成后就释放！` `从某种意义来说 线程同步只是多线程异步编程的一种特殊情况！`<br/>
 [`比喻`: `线程异步 我要人不管男人女人,一起上 线程同步,我要人,只要女人,一次只能上一个 上一个结束 下一个才开始`]<br/>

