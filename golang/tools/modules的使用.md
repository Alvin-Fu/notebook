go modules的使用 go1.13版本
测试使用一个


### module和vendor的使用
在mod中默认的情况下不会将内容放到vendor的目录中，如果要使用vendor管理依赖项的时候需要使用：
	`go mod vendor  和 go build -mod vendor` 将内容指定到vendor中