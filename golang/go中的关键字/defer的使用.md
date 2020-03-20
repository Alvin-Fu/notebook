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
预期的结果中我们想到的是
