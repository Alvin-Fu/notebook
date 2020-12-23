[原文地址](https://dave.cheney.net/2018/07/12/slices-from-the-ground-up)
## 数组
每次关于go切片讨论的都是从讨论一些不是切片的东西开始的，就是go的数组类型。数组在go中有两个特性：
1. 他们有固定的大小；[5]int即是一个包含5个int的数组，又不同于[3]int
2. 他们是值类型；考虑下面的例子
```go
package main

import "fmt"

func main() {
        var a [5]int
        b := a
        b[2] = 7
        fmt.Println(a, b) // prints [0 0 0 0 0] [0 0 7 0 0]
}
```
这个表达式b:=a声明了一个新的变量[5]int的变量b并且将a的内容拷贝到b。更新b不会影响a的内容，因为a和b是独立的值。

