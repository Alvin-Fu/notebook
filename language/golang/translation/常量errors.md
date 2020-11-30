 sentinel error: 字面意思是哨兵错误，其实本质就是这里有一个错误了需要马上停止。
[原文地址](https://dave.cheney.net/2016/04/07/constant-errors)
## 常量err
这是关于go的哨兵错误值的思想实验。
sentinel err是不好的，他引来了代码和与运行时的强耦合，但是有时候又是必要的，如io.EOF就是这样的一个哨兵值。理想情况下这个哨兵值应该表现为一个常量，也就是说他是不可变的和可替代的。
第一个问题就是io.EOF是一个公共变量所有引入了io包都可以改变io.EOF的值。事实上他在大多时候不是一个大的问题，但是在调试时是一个令人迷惑的问题。
```go
fmt.Println(io.EOF == io.EOF) // true
x := io.EOF
fmt.Println(io.EOF == x)      // true
	
io.EOF = fmt.Errorf("whoops")
fmt.Println(io.EOF == io.EOF) // true
fmt.Println(x == io.EOF)      // false
```
第二个问题是io.EOF表现的像一个单例，不是一个常量。即使我们按照io包使用的方式去创建我们自己的EOF的值，他们也是不相等的。
```go
err := errors.New("EOF")   // io/io.go line 38
fmt.Println(io.EOF == err) // false
```
结合这些属性，你就会产生一系列奇怪的行为，这些来自有





## 深入阅读
[go err的突围](https://www.mdeditor.tw/pl/pml6)



