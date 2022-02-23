# binlog是什么
binlog：是mysql sever层维护的一种二进制日志，主要是用来记录对mysql数据更新或者潜在发生更新的sql语句，并以“事务”的形式保存在磁盘中。
主要作用：

- 主从同步，主开启binlog，并将binlog传递给从回放达到主从数据一致性的目的
- 数据恢复
- 增量备份



#资料
[binlog的介绍](https://zhuanlan.zhihu.com/p/33504555)
