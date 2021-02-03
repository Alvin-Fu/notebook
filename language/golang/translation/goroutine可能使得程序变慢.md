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

 

[原文](https://appliedgo.net/concurrencyslower/)
[译文](https://mp.weixin.qq.com/s/aolQTL-VOvNqEicC95kYZw)











