### 配置是使用的主机名映射有问题



### 地址被使用
`2020-07-21 11:10:19,084 [myid:1] - ERROR [main:QuorumPeerMain@92] - Unexpected exception, exiting abnormally java.net.BindException: Address already in use`
这个是2181的端口被占用了，`netstat -atunlp|grep 2181`使用这个去查看

