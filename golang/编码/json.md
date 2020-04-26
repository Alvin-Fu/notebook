# go中使用json时需要注意的点

## 忽略某个字段
在marshal和unmarshal的时候忽略某个字段的方法
```
type People struct{
	Name string `json:""`
	Age int64
	Password string 
}
```
