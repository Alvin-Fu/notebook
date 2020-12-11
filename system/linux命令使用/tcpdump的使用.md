## 简介
tcpdump是一个网络数据包截获分析工具
## 常用的格式
### 指定网卡
`tcpdump -i en0`
**指定协议**
TCP/UDP
只想监听TCP `tcpdump tcp`
### 指定ip和端口
**指定特定主机**
`tcpdump host 182.123.13.1`  会监听本机和主机之间的所有通信包
**指定来源**
`tcpdump src host 182.123.13.1`
**指定目标**
`tcpdump dst host 182.123.13.1`
**指定端口**
`tcpdump port 8000`
## 例子
**and的使用**
`tcpdump ip host 182.123.13.2 and ! 182.123.13.1`表示182.123.13.2除了和182.123.13.1之外的主机的通信
没有！表示他们之间的通信
**详细的例子**
`tcpdump tcp -i en0 -t -s 0 -c 100 and dst port ! 22 and src net 182.123.13.0/24 -w ./target.cap`
- tcp: ip,icmp,tcp,udp等这些选项需要是第一个
- -i en0: 表示只抓经过en0的包
- -t： 表示不显示时间戳
- -s 0：表示抓取全部的包，默认是抓取68个字节
- -c 100: 表示抓取100个包
- dst port ! 22: 表示不抓取目标端口是22的包
- src net 182.123.13.0/24: 数据包的源网络地址为182.123.13.0/24
- -w ./target.cap: 保存成cap文件，方便分析

























