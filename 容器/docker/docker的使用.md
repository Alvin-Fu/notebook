docker rm $(docker ps -aq)  删除docker中所有的容器
docker rmi $(docker images -q) 删除docker中所有的镜像

