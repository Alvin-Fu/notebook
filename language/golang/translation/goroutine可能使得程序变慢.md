# goroutine可能使得程序变慢
## 如何使用goroutine才能使得你的CPU保持满负荷的运行。
下面我们将看到一个很大的for循环，我们将输入分成序列加入到goroutine中。
我敢打赌之前你会有这种情况(和感觉),但是这样做真的每次都能使得代码变快吗。这有一个简单的循环例子易于转化成并发的代码，但是我们将看到这个并发版本不仅仅是不快，实际上将花费两倍的时间。

## 串行循环
我们开始是一个简单的串行循环进行累加。
```go
ackage concurrencyslower
import (
"runtime"
"sync"
)

const (
    limit = 10000000000
)
func SerialSum() int {
    sum := 0
    for i := 0; i < limit; i++ {
        sum += i
    }
    return sum
}

```

## 并行版本 
显然，上面的循环只能使用一个逻辑CPU核心。因此一个Gopher很自然的会想到使用goroutine。示例代码中Goroutine是独立于其他代码运行，可以分布在所有的可用CPU上运行。 

```go
/*
ConcurrentSum试图使用所有的可用核心。
获取可用的逻辑核心的数量。这个通常是2*C，这里c是物理核心的数量，2是这个核心的超线程数。
我们需要在一个地方收集n个goroutine的结果。每个goroutine都有一个元素的全局片。
现在可以生成goroutine。WaitGroup帮助我们检测所有的goroutine都完成。
将输入分成n个不重叠的小块。
*/
func ConcurrentSum() int {
    n := runtime.GOMAXPROCS(0)
    sums := make([]int, n)
    wg := sync.WaitGroup{}
    for i := 0; i < n; i++ {
        wg.Add(1)
        go func(i int) {
            start := (limit / n) * i
            end := start + (limit / n)
            for j := start; j < end; j += 1 {
                sums[i] += j
            }
            wg.Done()
        }(i)
    }
    wg.Wait()
    sum := 0
    for _, s := range sums {
        sum += s
    }
    return sum
}

```


[原文](https://appliedgo.net/concurrencyslower/)
[译文](https://mp.weixin.qq.com/s/aolQTL-VOvNqEicC95kYZw)























