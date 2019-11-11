linux中ulimit工具
ulimit用于系统中做相应的资源限制
# 如何使用ulimit
ulimit的命令格式： ulimit [options] [limit]
|options|含义|例子|
|-|-|-|
|-H|设置硬资源限制，一旦设置不能增加| ulimit -Hs 64,设置线程栈的大小为64K|
|-S|软设置，设置以后可以增加，但是不能超过应设置|ulimit -Sn 32,限制软资源，32个文件描述符|
|-a|显示当前所有的ulimit的信息|ulimit -a|
|-c|最大的core文件的大小，以blocks为单位|ulimit -c unlimited, 对生成的core文件大小不做限制|





[ulimit资料](https://www.ibm.com/developerworks/cn/linux/l-cn-ulimit/index.html)