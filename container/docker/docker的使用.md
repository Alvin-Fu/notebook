# docker中的命令
docker rm $(docker ps -aq)  删除docker中所有的容器
docker rmi $(docker images -q) 删除docker中所有的镜像

# docker网络
获取容器的ip
docker inspect --format='{{.NetworkSettings.IPAddress}}' 9a1b68b6cf54

# docker中特殊命令的使用
## systemd
使用systemctl时会报错
```shell
[root@9a1b68b6cf54 system]# systemctl daemon-reload
Failed to get D-Bus connection: Operation not permitted
```

[解决方式](https://plutoacharon.github.io/2020/02/23/Docker%E5%AE%B9%E5%99%A8%E5%87%BA%E7%8E%B0%E4%BD%BF%E7%94%A8systemctl%E9%97%AE%E9%A2%98%EF%BC%9AFailed-to-get-D-Bus-connection-Operation-not-permitted/)
在docker中是不运行后台服务的，docker本身就是一个宿主机上的独立主进程。而systemd维护系统服务程序，它需要特权访问linux内核，因此需要使用特权模式运行容器
在创建容器的时候使用`# docker run -tid --name centos_1 --privileged=true centos:latest /sbin/init`



[快速获取docker的镜像和周边的安装包](https://get.daocloud.io/)

