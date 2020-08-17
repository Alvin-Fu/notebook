docker rm $(docker ps -aq)  删除docker中所有的容器
docker rmi $(docker images -q) 删除docker中所有的镜像


### docker中使用redis
`docker run -itd --privileged=treu --name redis -p 6379:6379 -v /home/appuser/docker/redis/conf/redis.conf:/etc/redis/redis.conf -v /home/appuser/docker/redis/data/:/data redis`
```
--privileged=true: 让容器内的root拥有真正的root权限，否则容器内的root只是外部的普通用户权限
-v /home/appuser/docker/redis/conf/redis.conf:/etc/redis/redis.conf：映射本地的配置文件，前面的表示本地的，后面的表示容器中的
```




[快速获取docker的镜像和周边的安装包](https://get.daocloud.io/)

