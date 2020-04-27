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
	Password string `json:"password,omitempty"`
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
marshal data: {"name":"tom","Age":2} 结果中就没有了空值

## 忽略嵌套的空结构体
在使用的过程经常会碰到结构体的嵌套
```
type User struct {
	Name string `json:"name"`
	Age int64
	Password string `json:"password,omitempty"`
	Profile  
}

type Profile struct {
	Website string `json:"site"`
	Slogan string `json:"slogan"`
}

func main(){
	u := &User{
		Name: "tom",
		Age: 2,
		Password: "12345",
	}
	data, _ := json.Marshal(u)
	fmt.Printf("marshal data: %s", string(data))
}
// marshal data: {"name":"tom","Age":2,"password":"12345","site":"","slogan":""}
```

```

type User struct {
	Name string `json:"name"`
	Age int64
	Password string `json:"password,omitempty"`
	Profile  `json:"profile"`
}

type Profile struct {
	Website string `json:"site"`
	Slogan string `json:"slogan"`
}

func main(){
	u := &User{
		Name: "tom",
		Age: 2,
		Password: "12345",
	}
	data, _ := json.Marshal(u)
	fmt.Printf("marshal data: %s", string(data))
}
// marshal data: {"name":"tom","Age":2,"password":"12345","profile":{"site":"","slogan":""}}
```
通过两个例子可以看到有tag和没有tag两个的序列化是不一样的

```
type User struct {
	Name string `json:"name"`
	Age int64
	Password string `json:"password,omitempty"`
	Profile  `json:"profile, omitempty"`
}

type Profile struct {
	Website string `json:"site"`
	Slogan string `json:"slogan"`
}

func main(){
	u := &User{
		Name: "tom",
		Age: 2,
		Password: "12345",
	}
	data, _ := json.Marshal(u)
	fmt.Printf("marshal data: %s", string(data))
}
// marshal data: {"name":"tom","Age":2,"password":"12345","profile":{"site":"","slogan":""}}
```
可以看到使用omitempty在空结构体中是没有用的
```
type User struct {
	Name string `json:"name"`
	Age int64
	Password string `json:"password,omitempty"`
	*Profile  `json:"profile,omitempty"`
}

type Profile struct {
	Website string `json:"site"`
	Slogan string `json:"slogan"`
}

func main(){
	u := &User{
		Name: "tom",
		Age: 2,
		Password: "12345",
	}
	data, _ := json.Marshal(u)
	fmt.Printf("marshal data: %s", string(data))
}
// marshal data: {"name":"tom","Age":2,"password":"12345"}
```


## 不修改原结构体忽略控制字段
在序列化的时候如果不想将结构体中的某个字段进行序列化，并且不想改结构体，可以新创建一个结构体并将原结构体匿名引用，并同时指定那个字段没结构体指针类型
```
type User struct {
	Name string `json:"name"`
	Age int64
	Password string `json:"password"`

}

type Profile struct {
	*User
	Password *struct{} `json:"password,omitempty"`
}


func main(){
	u := &User{
		Name: "tom",
		Age: 2,
		Password: "12345",
	}
	p := &Profile{User:u}
	data, _ := json.Marshal(p)
	fmt.Printf("marshal data: %s", string(data))
}
// marshal data: {"name":"tom","Age":2}
```


## 如何优雅的处理数字格式的字符串
在使用的过程中经常会出现字符串类型的数字，结构体的tag中添加string进行转化
```

type Number struct {
	ID int64 `json:"id,string"`
	Chip float64 `json:"chip,string"`
}

func main(){
	jsonStr := `{"id":"1234", "chip":"87.9"}`
	n := new(Number)
	err := json.Unmarshal([]byte(jsonStr), n)
	if err != nil {
		os.Exit(-1)
	}
	fmt.Printf("unmarshal number: %+v", n)
}
// unmarshal number: &{ID:1234 Chip:87.9}
```

## 整数变浮点数
在json协议中没有去区分
```
func main(){
	var tmp1 = make(map[string]interface{})
	tmp1["count"] = 1  // int
	m1, _ := json.Marshal(tmp1)
	fmt.Printf("marshal: %s\n", string(m1))
	var tmp2 = make(map[string]interface{})
	json.Unmarshal(m1, &tmp2)
	fmt.Printf("value: %v\n", tmp2["count"])
	fmt.Printf("type: %T\n", tmp2["count"])
}
/*
marshal: {"count":1}
value: 1
type: float64
*/
从结果可以看出来赋值的时候使用的是int，在解码的时候就变成了浮点型了
```






参考
[上](https://mp.weixin.qq.com/s/47VgCbuI2TbQ2G385vazVw)
[下](https://mp.weixin.qq.com/s/jUIXmYl5735LqK2oL6jMRg)










