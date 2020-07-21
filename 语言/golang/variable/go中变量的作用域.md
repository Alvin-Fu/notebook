在golang中变量的作用域和函数的参数传递有时是非常迷惑的

## 变量的作用域
```
func main(){
	fmt.Println(watShadowedDefer(50))
}
// 注意go中变量的作用域，以及参数传递时的值传递和指针传递的区别
func watShadowedDefer(i int)(ret int){
	ret = i * 2
	if ret > 10{
		ret := 10
		defer func() {
			ret = ret + 1
		}()
	}
	add(ret)
	return
}

func add(i int){
	i = i + 2
}
```
上面的输出值是多少？
函数add中的是i是值传递
if里面的ret和外面的ret在函数中是两个变量，defer中修改ret的值不会影响外面的值
最终的结果是：100
