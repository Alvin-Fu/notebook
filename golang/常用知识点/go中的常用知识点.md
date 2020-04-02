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
GPM模型





### 4、go中常用的并发模型



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

