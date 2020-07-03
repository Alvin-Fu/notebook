go modules的使用 go1.13版本
测试使用一个


### module和vendor的使用
在mod中默认的情况下不会将内容放到vendor的目录中，如果要使用vendor管理依赖项的时候需要使用：
	`go mod vendor  和 go build -mod vendor` 将内容指定到vendor中

### 更新Go Module项目的依赖
如何一键升级项目中的所有依赖到最新版本
引申的问题：
- 如何直接更新依赖
- 如何间接更新依赖
- 如何更新所有的依赖

当遇到基础问题时**通过golang-nuts邮件组搜索问题**， 还可以在**go仓库的issue中搜索**




[详细的介绍了module的使用](https://roberto.selbach.ca/intro-to-go-modules/)