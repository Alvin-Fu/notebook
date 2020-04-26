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
type People struct{
	Name string `json:"name"`
	Age int64
	Password string  
}


```
