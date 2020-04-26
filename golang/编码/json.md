# go中使用json时需要注意的点

## 忽略某个字段
在marshal和unmarshal的时候忽略某个字段的方法
```
type People struct{
	Name string `json:"name"`
	Age int64
	Password string  `json:"-"`
}
```
在tag中添加-这样在序列化和反序列化的时候将忽略Password字段

## 忽略空值
在序列化和反序列化的时候忽略空值
在通常使用中如果结构体的字段没有值的时候，matshal的时候会添加一个默认值而不会省略
```
type People struct {
	Name string `json:"name"`
	Age int64
	Password string
}

func main(){
	p := &People{
		Name: "tom",
		Age: 2,
	}
	data, err := json.Marshal(p)
	if err != nil {
		os.Exit(-1)
	}
	fmt.Printf("marshal data: %s", string(data))
}
```
结果：marshal data: {"name":"tom","Age":2,"Password":""} 从结果可以看出会有默认值
```
type People struct {
	Name string `json:"name"`
	Age int64
	Password string `json: "password,omitempty"`
}

func main(){
	p := &People{
		Name: "tom",
		Age: 2,
	}
	data, err := json.Marshal(p)
	if err != nil {
		os.Exit(-1)
	}
	fmt.Printf("marshal data: %s", string(data))
}
```



