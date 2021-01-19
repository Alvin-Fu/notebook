# channel的学习
通过实践和结合channel的源码学习channel的知识。
## 发送数据
### 空的channel
向一个空的channel中写数据的时候会导致这个goroutine停止不能被唤起
```go
package main

import (
	"fmt"
	"time"
)

type NilChan struct {
	c chan int
}

func main() {
	c := NilChan{}
	go func() {
		// 这些打印都不会被执行
		defer fmt.Println("kk")
		c.c <- 1
		fmt.Println("hello")
	}()
	time.Sleep(1 * time.Second)
	c.c = make(chan int)
	go func() {
		c.c <- 1
	}()
	go func() {
		fmt.Println(<-c.c)
		fmt.Println(<-c.c)
	}()
	time.Sleep(5 * time.Second)
}
```

## 读取数据