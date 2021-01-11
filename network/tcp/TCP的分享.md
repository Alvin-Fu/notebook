# 概要
主要分享以下几个部分：
1. tcpdump抓包
2. TCP连接的建立和端开
3. 交互数据流
## tcpdump抓包
`tcpdump tcp -i en0 -t -s 0 -c 100 and dst port ! 22 and src net 182.123.13.0/24 -w ./target.cap`
- tcp： 表示协议类型，总是在第一个，也可以是udp，ip等
- -i en0：表示只抓经过en0的包
- -t： 表示不显示时间戳，会显示具体时间
- -s 0: 表示抓取全部的包，默认是抓68个字节
- -c 100: 表示抓取100个包然后停止
- dst port ! 22: 表示不抓取目标端口是22的包
- src net 182.123.13.0/24: 数据包的源网络格式是182.123.13.0/24
- -w ./target.cap： 保存成cap文件
[wireshark中的提示](https://github.com/Alvin-Fu/notebook/blob/master/system/linux-command/tcpdump-wireshark.md)

