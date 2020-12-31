```
[root@localhost freedom]# go build
# runtime
/usr/local/go/src/runtime/stubs_x86.go:10:6: stackcheck redeclared in this block
	previous declaration at /usr/local/go/src/runtime/stubs_amd64x.go:10:6
/usr/local/go/src/runtime/unaligned1.go:11:6: readUnaligned32 redeclared in this block
	previous declaration at /usr/local/go/src/runtime/alg.go:321:40
/usr/local/go/src/runtime/unaligned1.go:15:6: readUnaligned64 redeclared in this block
	previous declaration at /usr/local/go/src/runtime/alg.go:329:40

```
在更新了go的新版本，使用1.13，进行go mod的时候出的问题，这是由于之前卸载go1.11的时候没有卸载干净导致代码重复出现的问题。
解决办法：将之前的卸载干净，重新安装就和好了
