go中的常用知识点
### 1、go中如何安全的访问共享变量
	- 互斥锁
	- channel用于goroutine之间，这个也符合go的使用通信去共享，而不是通过共享而通信
	
### 2、无缓冲的chan接收和发送是否同步
	- 无缓冲的接收和发送是要同步的，不然会出现阻塞
	- 有缓冲的，当缓冲没有满的时候发送不会阻塞，当缓冲有内容时接收不会阻塞

### 3、go语言的并发机制和使用CSP并发模型
CSP并发模型：以通信的方式进行共享内存，而非传统的以共享内存进行通信。用来描述两个独立的并发实体之间通过共享的管道进行通信的并发模型
golang中channel是被单独创建出来的可以再并发实体之间传递信息，用于两个匿名的实体之间。channel类似于一个消息队列，并发实体之间通过给channel中产生消息和消费消息实现了解耦
goroutine是go中实际执行并发的实体，它的底层是通过协程实现并发，coroutine是运行再用户态的用户线程，go底层选择coroutine的出发点，它具备的特点：
- 用户空间，避免了内核态和用户态之间的切换导致的成本
- 可以由语言和框架层进行调度
- 更小的栈空间允许创建更多的实例
goroutine的特性：
go内部的三个对象： P(processer)代表上下文，也可以理解为cpu，M工作线程， G表示对象(gotoutine)
G：代表goroutine
M(Machine)：对内核线程的封装，对应真实的CPU数，真正干活的
P(Processer): G和M的中间调度，数量可以由GOMAXPROCS()来设置，默认为核心数







### 4、go中常用的并发模型
#### 使用Channel实现的并发模型


#### 使用sync的WaitGroup实现的并发模型
需要注意的是避免出现死锁
如：
```
func main(){
	wg := sync.WaitGroup{}
	for i := 0; i < 1; i++{
		wg.Add(1)
		go func(wg sync.WaitGroup, i int) {
			fmt.Println(i)
			wg.Done()
		}(wg, i)
	}
	wg.Wait()
}
```
[演示](https://play.golang.org/p/RY_1ChRH7zs)
会出现死锁的现象，因为传入的wg只是拷贝的副本， 使用指针传递就不会出现了，还可以不传人，由于go中支持闭包类型，因此在里面可以直接使用外面的变量

#### 使用context实现并发控制

### 5、json标准库对nil slice和空slice的处理一致吗
json对于`var tmp1 []int和 tmp2 := make([]int, 0)`的处理是不一样的
```
func main(){
	var tmp1 []int
	tmp2 := make([]int, 0)
	data1, err := json.Marshal(tmp1)
	if err != nil {
		log.Fatalf("marshal err: %v", err)
		return
	}
	fmt.Println(string(data1))
	fmt.Println(data1)
	data2, err := json.Marshal(tmp2)
	if err != nil {
		log.Fatalf("marshal err: %v", err)
		return
	}
	fmt.Println(string(data2))
	fmt.Println(data2)
}
```
[演示](https://play.golang.org/p/AzpiWvM8937)


### 6、进程，线程和协程的关系

#### 进程
进程是具有独立功能的程序，一个进程有一个或多个线程。系统分配资源和调度的最小单位，拥有独立的栈，内存空间等。相对比较重量上下文切换的耗费较大，但是相对来说比较稳定
#### 线程
是进程的实体，CPU调度和分派的基本单位，线程基本不拥有系统的资源，只有一些必须的如寄存器等。同一个进程中的线程可以通过共享进程的资源，通信可以使用共享内存，相对轻量，上下文切换快，资源开销小，适合较大量的并发
#### 协程
用户态的轻量线程，调度完全由用户控制。协程拥有自己的寄存器上下文和栈。在切换的时候会将寄存器上下文和栈保存到其他地方，当切换回来的时候在恢复现场，直接操作栈几乎没有内核切换的开销


### 7、互斥锁，读写锁，死锁问题怎么解决？
#### 互斥锁
互斥锁就是互斥变量，可以锁住临界区域
条件锁就是条件变量，当进程所需要的资源不能满足时，进程进入睡眠状态(被锁住)阻塞，当条件满足的时候进程将被唤醒，进程继续执行，读写锁也是类似的

#### 读写锁
是互斥锁的一种变型，可以进行并发的读



#### 死锁问题怎么解决



### 20、range和iteration的关系
```
// 对range的测试, 在range的使用过程中需要注意range是将当前的值copy了一份，因此在改的时候需要注意
// 如果需要在range中修改需要使用下标

type Animal struct {
	name string
	legs int
}

func main() {
	zoo := []Animal{
		Animal{"dog", 4},
		Animal{"panda", 4},
		Animal{"peacock", 2},
	}
	Read(zoo)
	fmt.Printf("read: %v\n", zoo)
	Write(zoo)
	fmt.Printf("wrote: %v\n", zoo)
}

func Read(zoo []Animal) {
	for _, a := range zoo {
		a.legs = 100
	}
	fmt.Printf("%v\n", zoo)
}
func Write(zoo []Animal) {
	for i := range zoo {
		zoo[i].legs = 100
	}
	fmt.Printf("%v ✅\n", zoo)
}
```
[演示](https://play.golang.org/p/nj4M9PsxXC0)
从上面的结果中可以看出来，range中取出的a只是一个临时变量，改变他的值是不会影响到slice中的值，使用下标是可以改变的
由于range取出来的是一个临时变量，因此在使用地址的时候也是需要注意的
```
type ID struct {
	id int
}

func main(){
	id1 := ID{1}
	id2 := ID{2}
	ids1 := []ID{id1, id2}
	ids2 := []*ID{}
	for _, id := range ids1{
		ids2 = append(ids2, &id)
	}
	for _, id := range ids2{
		fmt.Println((*id).id)
	}
}
```
[演示](https://play.golang.org/p/jGGMrAM7Yb2)



[面试](https://www.cnblogs.com/wpgraceii/p/10528183.html)

