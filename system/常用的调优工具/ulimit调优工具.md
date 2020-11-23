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
|-d|进程最大的数据段的大小，以Kbytes为单位|ulimit -d unlimited,对进程的数据段大小进行限制|
|-f|进程可以创建文件的最大值，以blocks为单位|ulimit -f 2048,限制进程可以创建的最大文件大小为2048|
|-l|最大可以加锁内存大小，以Kbytes为单位|ulimit -l 32，限制最大可锁内存大小为32kbytes|
|-m|最大内存大小，为Kbytes为单位|ulimit -m unlimited, 对最大内存不进行限制|
|-p|管道缓冲区的大小，Kb为单位|ulimit -p 512, 限制大小为512kb|
|-s|线程栈大小，以KB为单位|ulimit -s 512， 限制线程栈的大小为512|
|-t|最大的CPU占用时间， 单位S|ulimit -t unlimited, 对cpu占用时间不做限制|
|-u|用户的最大可用进程数|ulimit -u 64，限制用户最多可以使用为64个进程|
|-v|进程最大可用的虚拟内存，以Kbytes为单位|ulimit -v 200000，限制最大可用的虚拟内存为200000Kbytes|

## 使用ulimit的方式
1. 在用户的启动脚本中`在bash下的.bashrc文件中添加ulimit命令`
2. 在应用程序的启动脚本中，可以对应用程序进行限制
3. 直接在控制台输入

# 用户进程的有效范围
## 进程的限制
ulimit作为限制资源的命令，那么ulimit的有效范围是什么呢。单个用户，单个进程，还是整个系统
当打开两个shell在当前的shell中进行ulimit操作，不会影响另一个shell
## 用户的限制
要想对某个具体用户资源进行限制是可以通过修改系统的/etc/security/limits.conf文件进行限制
文件中格式：
	<domain> <type> <item> <value>
- domain 表示用户或者组的名字，还可以使用通配符*
- type   有两种类型soft和hard表示软限制和硬限制
- item   表示需要限定的资源
- value  表示限定的值
如：
`* hard nofile 100  表示任意用户所能创建的最大文件数是100，且为硬限制`

## 系统的限制
可以对/proc目录下包含的很多系统状态参数进行限制
如：
	/proc/sys/kernel/pid_max等文件

# ulimit管理系统资源的例子
使用ls命令为例
```
[appuser@fu src]$ echo "test ulimit" > test
[appuser@fu src]$ ls test -l
-rw-rw-r-- 1 appuser appuser 12 Nov  9 13:52 test
[appuser@fu src]$ ulimit -d 1000 -m 1000 -v 1000
[appuser@fu src]$ ls test -l
Segmentation fault

[root@fu ~]# ll /lib64/libc.so.6 
lrwxrwxrwx 1 root root 12 Jul 11 10:58 /lib64/libc.so.6 -> libc-2.17.so
[root@fu ~]# ll /lib64/libc-2.17.so 
-rwxr-xr-x 1 root root 2151672 Jul  3 21:52 /lib64/libc-2.17.so
[root@fu ~]# ll /lib64/libc-2.17.so -h
-rwxr-xr-x 1 root root 2.1M Jul  3 21:52 /lib64/libc-2.17.so
可以看到ll这个命令调用libc库时的内存超过了限制

```













[ulimit资料](https://www.ibm.com/developerworks/cn/linux/l-cn-ulimit/index.html)