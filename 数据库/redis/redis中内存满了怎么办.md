## redis占用内存大小
众所周知redis是基于内存的key-value结构的，由于系统内存的限制，在使用redis的时候我们可以配置redis能使用的最大内存大小

### 1、通过配置文件配置
通过redis的redis.conf文件可以配置内存大小 
`maxmemory 100mb  //设置为100M`

### 2、通过命令修改
在客户端``
