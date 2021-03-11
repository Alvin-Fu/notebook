# go中的方法接收者
go中对于一个方法来说会有两种不同的接收者，值类型和指针类型的接收者
```go
package main

import "fmt"

type Student struct {
	Uid     int64
	ClassId int64
	Score   int64
}

func (s Student) Notify() {
	s.Score = 1
	fmt.Println("notify")
}

func (s *Student) Broadcast() {
	s.ClassId = 1
	fmt.Println("broadcast")
}

func main() {
	s := Student{
		Score:   2,
		ClassId: 2,
	}
	s.Notify()
	s.Broadcast()
	fmt.Println(s.ClassId, s.Score)
	s1 := &Student{
		Score:   2,
		ClassId: 2,
	}
	s1.Notify()
	s1.Broadcast()
	fmt.Println(s1.ClassId, s1.Score)
}
```
不管是值类型还是指针类型，调用者都可以使用方法，但是在值类型的方法中修改接收者的值是不会影响到外面的，作用域只能是方法里面

||值接收者|指针接收者|
|-|-|-|
|值调用者|使用调用这个的副本，想当于值传递|使用值的引用来调用方法，相当于引用传递|
|指针调用者|指针被解引用为值，实际是将具体中的值策划低进去|拷贝了一份指针进去，修改会影响到调用者本身|




















