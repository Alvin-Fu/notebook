## redis占用内存大小
众所周知redis是基于内存的key-value结构的，由于系统内存的限制，在使用redis的时候我们可以配置redis能使用的最大内存大小

### 1、通过配置文件配置
通过redis的redis.conf文件可以配置内存大小 
`maxmemory 100mb  //设置为100M`

### 2、通过命令修改
在客户端进行修改`config set maxmemory 100md`  `config get maxmemory`
如果不设置在64位操作系统中不限制内存，在32位操作系统中最多使用3GB内存

## redis的内存淘汰
当redis占用的内存达到了，最大内存的时候，继续向redis中设置数据会不会出现没有内存可用的情况呢？

