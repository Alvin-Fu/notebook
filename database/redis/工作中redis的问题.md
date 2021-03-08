## 快照保存的问题
在set的时候遇到这个问题，之前都是正常的
```text
MISCONF Redis is configured to save RDB snapshots, but it is currently not able to persist on disk. Commands that may modify the data set are disabled, because this instance is configured to report errors during writes if RDB snapshotting fails (stop-writes-on-bgsave-error option). Please check the Redis logs for details about the RDB error.

```
由于没有redis的日志看不出来是系统导致的还是配置导致的，因此设置`config set stop-writes-on-bgsave-error no`

在网上看到有一种可能会导致这个情况，在进程的内存空间很大的时候进程进行fork的时候是一件很恐怖的事情。可能会报内存无法分配的。越是改动频繁的主机上fork也越频繁，这样导致的代价会很大。
Linux内核会根据参数 `vm.overcommit_memory`决定是否放行
如果vm.overcommit_memory = 1直接放行
vm.overcommit_memory = 0则会比较此次请求分配的虚拟内存大小和系统当前空闲的物理内存加上swap，决定是否放行
vm.overcommit_memory = 2则会比较进程所有已分配的虚拟内存加上此次请求分配的虚拟内存和系统当前的空闲物理内存加上swap，决定是否放行

redis的回写机制
两种异步和同步
- 同步回写即SAVE命令，主进程直接向磁盘回写数据。在数据大的情况下会导致系统假死很长时间，所以一般是不推荐的。
- 异步回写即BGSAVE命令，主进程fork后，复制自身并通过这个新的进程回写磁盘，回写结束后新进程自动关闭。由于这样主进程不需要阻塞，系统不会假死，一般默认会采取这个方法。

