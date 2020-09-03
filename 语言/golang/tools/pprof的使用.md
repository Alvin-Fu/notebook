**在使用pprof过程中的总结，排查内存泄露,内存泄露的主要原因就是goroutine没有退出，因此主要是要排查goroutine是否正常退出**
**注意:** 在golang中使用goroutine的时候一定要注意这个goroutine如何退出，如果不能考虑到如何退出，大概率会出现goroutine的泄露

### pprof的使用
![web界面图](../../../.local/static/2020/8/4/Snipaste_2020-09-03_11-17-28.1599103068236.png)


#### goroutine的使用
`http://127.0.0.1:8080/debug/pprof/goroutine?debug=1` 在web界面使用debug=1和debug=2是会有不同的
![debug=1](../../../.local/static/2020/8/4/Snipaste_2020-09-03_11-21-30.1599103306017.png)
![debug=2](../../../.local/static/2020/8/4/Snipaste_2020-09-03_11-21-10.1599103326063.png)
图1是debug=1的情况，前面的1表示当前有多少个goroutine在运行这个调用栈
图2是debug=2的情况，数字表示goroutine的id，[]里面的表示是怎么阻塞的，和等待的时间，同时也可以看到当前停在了哪一行

#### heap的使用
**生成正在使用的内存**
`go tool pprof -inuse_space http://127.0.0.1:8080/debug/pprof/heap` 获取的是当前还在使用的内存
`go-torch -inuse_space http://127.0.0.1:8080/debug/pprof/heap --colors=mem` 生成火焰图
**生成分配的内存相关的**
`go tool pprof -alloc_space http://127.0.0.1:8080/debug/pprof/heap` 获取的是当前还在使用的内存
`go-torch -alloc_space http://127.0.0.1:8080/debug/pprof/heap --colors=mem` 生成火焰图

```
heap profile: 3190: 77516056 [54762: 612664248] @ heap/1048576
1: 29081600 [1: 29081600] @ 0x89368e 0x894cd9 0x8a5a9d 0x8a9b7c 0x8af578 0x8b4441 0x8b4c6d 0x8b8504 0x8b2bc3 0x45b1c1
#    0x89368d    github.com/syndtr/goleveldb/leveldb/memdb.(*DB).Put+0x59d
#    0x894cd8    xxxxx/storage/internal/memtable.(*MemTable).Set+0x88
#    0x8a5a9c    xxxxx/storage.(*snapshotter).AppendCommitLog+0x1cc
#    0x8a9b7b    xxxxx/storage.(*store).Update+0x26b
#    0x8af577    xxxxx/config.(*config).Update+0xa7
#    0x8b4440    xxxxx/naming.(*naming).update+0x120
#    0x8b4c6c    xxxxx/naming.(*naming).instanceTimeout+0x27c
#    0x8b8503    xxxxx/naming.(*naming).(xxxxx/naming.instanceTimeout)-fm+0x63

......

# runtime.MemStats
# Alloc = 2463648064
# TotalAlloc = 31707239480
# Sys = 4831318840    从系统获取的虚拟地址空间
# Lookups = 2690464
# Mallocs = 274619648
# Frees = 262711312
# HeapAlloc = 2463648064
# HeapSys = 3877830656
# HeapIdle = 854990848
# HeapInuse = 3022839808
# HeapReleased = 0
# HeapObjects = 11908336
# Stack = 655949824 / 655949824
# MSpan = 63329432 / 72040448
# MCache = 38400 / 49152
# BuckHashSys = 1706593
# GCSys = 170819584
# OtherSys = 52922583
# NextGC = 3570699312
# PauseNs = [1052815 217503 208124 233034 1146462 456882 1098525 530706 551702 419372 768322 596273 387826 455807 563621 587849 416204 599143 572823 488681 701731 656358 2476770 12141392 5827253 3508261 1715582 1295487 908563 788435 718700 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
# NumGC = 31   gc的
# DebugGC = false
```
heap的信息主要分为两部分，
第一部分主要是打印为通过runtime.MemProfile()获取的runtime.MemProfileRecord记录
第二部分主要是打印的是通过runtime.ReadMemStats()读取的runtime.MemStats信息


