# go调度的详解
## 概要
go的调度指的是goroutine的调度过程。和系统中的线程的调度有着相似的地方，我们先思考四个问题。
- 并行，可扩展且公平
- 每个进程应该可以扩展数百万个goroutine
- 内存利用率高
- 系统调用不应导致性能下降(最大吞吐量，最小化等待时间)
## goroutine调度程序模型
**一个线程执行一个goroutine局限性：**

- 并行可扩展(真并行假的可扩展)
- 每个进程不能扩展到数百万个goroutine

**M:N线程**
M个内核线程执行N个goroutine，内核线程的创建都是很昂贵的，因此是让goroutine去内核线程上运行。内核线程我们是不能控制的，但是goroutine的是可以控制的并且开销很小的。
内核不知道当前goroutine的状态，需要有一个调度器根据goroutine状态进行调度。与内核线程的状态信息比起来goroutin的保存的很少了，这个使得goroutine的切换开销很小
```text
- 正在运行(Grunning): 当前在内核线程上运行的goroutine
- 可运行(Grunnable): 等待内核线程来运行的goroutine,一般在创建后被加入到P的私有队列，阻塞任务唤醒被加入队列，从Grunning和Gsyscall状态转换过来的
- 系统调用(Gsyscall): 在任务执行OS系统调用前，先会调用runtime.entersyscall函数将
   	状态设置为Gsyscall，当系统调用是阻塞式的或在执行过久，M和P分离，返回后M会重
	新获取P，没有任务抢占的时候进入Grunning状态，否则进入Grunnable状态
- 等待（Gwaiting）：当任务所需的资源或运行条件不能被满足是，进入这个状态(runtime.park函数设置)
- 死亡(Gdead): 当任务执行结束后，调用runtime.goexit结束生命并设置为Gdead状态，然后将G链到当前P的空闲连表中
```
goroutine的状态转换图
![title](https://raw.githubusercontent.com/Alvin-Fu/notebook/master/images/2021/03/12/gpm-status-1615547108823.jpeg?token=AJEDOD2KT2L5W67IEL4VN6TAJLNK2)



## 参考
很清晰的GPM模型讲解
[G-P-M模型](https://my.oschina.net/aom/blog/4279175)
[goroutine的状态转换](https://cloud.tencent.com/developer/article/1486609)


















