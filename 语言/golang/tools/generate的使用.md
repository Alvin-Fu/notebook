## 多语言
使用generate和gotext实现多语言
首先在代码中需要添加generate的命令
```
// NOTE: change -lang option to add more languages support.
//go:generate gotext -srclang=en update -out=catalog.go -lang=en,zh-hans,zh-hant

gotext用于管理源码中的文本
	update用于更新翻译和

```
