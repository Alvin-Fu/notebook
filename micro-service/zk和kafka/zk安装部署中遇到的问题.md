### 配置是使用的主机名映射有问题
`[myid=1]/0:0:0:0:0:0:0:0:2181:QuorumPeer$QuorumServer@190] - Failed to resolve address: slave-03`
这是是配置中使用的主机名的映射不存在
需要添加主机名的映射
```
vim /etc/hosts
添加格式**ip  hostname**
如：
127.0.0.1   master

检测
ping master
```

### 地址被使用
`2020-07-21 11:10:19,084 [myid:1] - ERROR [main:QuorumPeerMain@92] - Unexpected exception, exiting abnormally java.net.BindException: Address already in use`
这个是2181的端口被占用了，`netstat -atunlp|grep 2181`使用这个去查看

### 连接被拒绝的情况
```
2020-07-21 12:13:42,324 [myid:1] - WARN  [WorkerSender[myid=1]:QuorumCnxManager@584] - Cannot open channel to 2 at election address master/127.0.0.1:3889
java.net.ConnectException: Connection refused (Connection refused)
```
这种说明server1被选择为follower
这时应该启动server2这样会发现server2被选举为leader


