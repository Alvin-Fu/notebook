[原文地址](https://dave.cheney.net/2018/07/12/slices-from-the-ground-up)
## 数组
每次关于go切片讨论的都是从讨论一些不是切片的东西开始的，就是go的数组类型。数组在go中有两个特性：
1. 他们有固定的大小；[5]int即是一个