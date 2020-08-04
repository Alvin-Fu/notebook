## 多语言
使用generate和gotext实现多语言
首先在代码中需要添加generate的命令
```
// NOTE: change -lang option to add more languages support.
//go:generate gotext -srclang=en update -out=catalog.go -lang=en,zh-hans,zh-hant

gotext用于管理源码中的文本
	update用于更新翻译和generate输出的catalog文件，

-out是指定输出的.go文件的位置， -lang是指定语言的类型


```
只会生成代码中有的标签，需要有message.gotext.json这是放的翻译的内容
```
var acceptPrinter = make(map[string]*message.Printer)
func main(){
    for _, tag := range message.DefaultCatalog.Languages() {
       acceptPrinter[tag.String()] = message.NewPrinter(tag)
    }
    //p := GetPrinter(language.English.String())
    //p.Printf("login multiple times")
    //p.Println()
    //p.Printf("client request error")
    //p.Println()
    ph := GetPrinter("zh-Hans")
    ph.Printf("login multiple times")

}
// GetPrinter return a printer by language string, if printer not found, return a default printer.
func GetPrinter(lang string) *message.Printer {
    if len(lang) == 0 || lang == "default" {
        fmt.Println(lang)
        return acceptPrinter[ language.English.String()]
    }

    p, ok := acceptPrinter[lang]
    if !ok {
        fmt.Println(lang)
        return acceptPrinter[ language.English.String()]
    }
    return p
}
```


![代码的目录结构](../../../.local/static/2020/7/2/Snipaste_2020-08-04_18-23-02.1596536716616.png)