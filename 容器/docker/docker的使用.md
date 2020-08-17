docker rm $(docker ps -aq)  删除docker中所有的容器
docker rmi $(docker images -q) 删除docker中所有的镜像


### docker中安装redis
```
docker run -itd --name redis -p 6379:6379 -v /home/appuser/docker/redis/conf/redis.conf:/etc/redis/redis.conf -v /home/appuser/docker/redis/data/:/data redis
```


[快速获取docker的镜像和周边的安装包](https://get.daocloud.io/)

