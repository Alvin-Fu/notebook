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
reids中定以了几种策略来处理这种情况：

- noeviction(默认策略)： 对于写请求不在进行处理
- allkeys-lru: 从所有key中使用LRU算法进行淘汰
- volatile-lru: 从设置了过期时间的key中使用LRU算法进行淘汰
- allkeys-random：从所有的key中随机进行删除
- volatile-random：从设置了过期时间的key中随机删除
- volatile-ttl：在设置了过期时间的key中，越早过期的优先被淘汰

查看淘汰策略`config get maxmemory-policy`
设置淘汰策略`config set maxmemory-policy allkeys-lru`

## LRU算法
### 什么是LRU算法
LRU(Least Recently Used)，最近最少使用，是一种缓存置换算法。
redis种使用的是一种近似LRU算法，使用随机采样的方式淘汰数据，每次随机出5(默认)个key，从里面淘汰掉最近最少使用的一个key
可以修改采样数量 `maxmemory-samples 10`,采样数越多，结果越接近真实的LRU算法

在redis3.0中对于近似LRU算法进行了优化：新算法会维护一个候选池(大小为16)，池中的数据根据访问时间进行排序，第一次是随机选取，随后每次随机选取的key只有访问时间小于最小的时间才会放入池中，知道池子满了，如果有新的要放入了，则将池中最后访问时间最大(最近被访问)的移除

## LFU算法
LFU(Least Frequently Used)算法是redis4.0中新加的一种淘汰策略,核心就是根据key的最近被访问的频率进行淘汰，很少被访问的优先被淘汰
可以很好的展示一个key被访问的热度，可以解决LRU算法中一个很久不访问的key被偶尔访问一次导致被认为是热点数据
LFU中的策略：

- volatile-lfu：在设置了过期时间的key中使用LFU算法淘汰key
- allkeys-lfu：在所有的key中使用LFU算法淘汰数据


