mysql的使用应该注意的点
# mysql的性能优化
在mysql的使用过程中学会使用explain去优化sql语句
在日常使用中mysql的性能优化：
##1、添加索引
给搜索字段加上索引，建立索引的查询效率是不建立索引的四倍左右，可以通过explain进行比较
##2、join表
join表时应该注意的地方，join字段应该是建立了索引的，并且为了提高效率，join的字段应该是类型相同的，相同的字符集，mysql会为此建立索引
##3、为表设置ID
为每张表设置一个ID，作为主键，类型最好为INT型，并且将ID设为自增ID（AUTO_INCREMENT标志）
在Mysql中使用其他类型作为主键可能会导致性能下降，如使用varchar。
主键的设置在Mysql中是很有用处的，比如：
	`mysql中的分区， 集群等`

# Mysql中主键的作用
为什么在InnoDB中需要一个和业务无关的自增Id作为主键？
原因：
1. InnoDB存储引擎采用了聚集的方式，因此每张表的存储都是以主键的顺序进行存放的。如果表中没有指定主键，InnoDB存储引擎会为每行数据生成一个ROWID，并以此作为主键
2. 在InnoDB存储引擎中，具体的数据放在了B+树的叶子节点上，保证数据的有序有助于按顺序将数据添加到当前的索引后面

# Mysql查询时的点
在查询时where后面的条件顺序对于查询是不影响的，因为在查询的时候mysql会自动将条件的顺序调整为适合于索引的顺序

# like

参考资料：
[mysql的性能优化](https://www.cnblogs.com/pengyunjing/p/6591660.html )
[关于主键的作用](https://ruby-china.org/topics/26352)
[阿里分库分表](https://mp.weixin.qq.com/s/YpirU0zbZfKNxDSQSTNaxQ)
[mysql性能优化必备技能](https://mp.weixin.qq.com/s/UkQ31grdLZJttlNAPFs9cg)




