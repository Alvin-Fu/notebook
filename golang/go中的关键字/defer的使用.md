在使用defer时会遇到的问题：
## 实际和预期不一致
```
	t := time.Now()
	defer fmt.Println("first: ",time.Since(t))
	defer func() {
		fmt.Println("func: ",time.Since(t))
	}()
	time.Sleep(2 * time.Second)
	结果：
		func:  2.0001454s
		first:  0s
```
预期的结果中我们想到的是打印的结果都是2，然而实际上并不是
原因：预计算参数，在defer调用的时候进行计算的，而不是main退出之前计算的
解决方法：
