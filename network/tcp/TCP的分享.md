# 概要
主要分享以下几个部分：
1. tcpdump抓包
2. TCP连接的建立和断开
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

### 抓包异常数据解读
#### 1、[TCP Previous segment not captured]丢包
TCP传输过程中数据包应该是连续的，后一个包的seq(n)=seq(n-1) + len(n-1),如果seq(n)>seq(n-1) + len(n-1)说明中间有一段数据丢失了。
[TCP Out-of-Order]表示包乱序了，比如seq(n) < seq(n-1)表示包的顺序错了
#### 2、[TCP Dup ACK x#y]
当乱序或在丢包发生时，接收方会收到一个比期望大的seq号。这时接收方会告知发送方当前他想要的包的seq
#### 3、[TCP Spurious Retransmission] 丢包重传
#### 4、[TCP Fast Retransmission] 快速重传
当发送方收到三个以上的[TCP Dup ACK]，就说明之前法的包可能丢了，于是快速重传他(RFC的规定)
#### 5、[TCP Retransmission]超时重传
如果一个包真的丢了，又没有后续包可以在接收方触发[Dup Ack],就不会快速重传，只能超时重传。
这时接收方和发送方之间没有其他包的往来，导致ACK和seq不能更新。
#### 6、[TCP ACKed unseen segment]
这个标志表示抓包发现被ack的包但是没有被抓到
42号包是序号27741 + 1460 = 29201 
说明29201到33581之间的数据没有抓到但是被确认了
![title](../../.local/static/2021/0/1/Snipaste_2021-01-11_10-52-17.1610362412869.png)
## TCP连接的建立和断开
看一个TCP连接使用的过程
1. 建立连接
2. 发送方发送数据包，接收方收到后返回确认包，如果没有重新发，有就发送下一个
3. 接收方等待数据包，接收到时对数据包进行校验，符合就返回(ACK)确认包，并继续等待，直到收到FIN
4. 中止连接
![TCP的状态迁移图](../../.local/static/2020/11/5/110924s3zzfzfff8y1ht6x.1608834077043.png)
**图中的其他状态**
- LISTEN--->SYN_SENT: 这个表示服务端有时候也时需要建立连接的
- SYN_SENT--->SYN收到：客户端和服务端如果在SYN_SENT状态时如果收到了SYN，都需要回复SYN的ACK，然后将自己调整到ESTABLIDSHED状态
- SYN_SENT--->CLOSED: 发送超时的情况下需要进入CLOSED
- SYN收到--->LISTEN: 表示收到了RST包，然后返回到LISTEN(当一方断电等情况，另一方不知道的时候，进行发包的时候另一端会返回一个RST包)
- SYN收到--->FIN_WAIT_1：在建立连接后不进行到ESTABLIDSHED状态，然后连接被关闭了
### 连接的建立

#### 同时打开
![title](../../.local/static/2021/0/2/52im_net_27.1610387708470.png)
双方都发送，可以看到多了一个确认包，因为双方即是客户端又是服务端
### 连接的断开
四次挥手，主动断开的一方会进入到TIME_WAIT状态，一般都是客户端主动断开，防止服务端有过多的TIME_WAIT状态
FIN_WAIT_2状态，当进入这个状态的时候一直没有收到对方的FIN时，是不是就一直在这个状态呢
#### 半关闭状态
当一端发送完数据以后，还能收到另一端的数据
![title](../../.local/static/2021/0/2/52im_net_12.1610382277419.png)
一般直接使用close是不会出现这种情况的，这个是需要socket那块支持提供给应用程序使得可以发送FIN而不是直接调用close
#### 同时关闭
当双方数据都发送完毕的时候，同时发送FIN，双方都会进入到TIME_WAIT状态，并且双方不会进入FIN_WAIT_2状态
![title](../../.local/static/2021/0/2/52im_net_31.1610387477948.png)
#### RST的情况
通过抓包有时是可以看到出现RST包，RST包也是关闭连接的一种方式。
业务中遇到的就是客户端出现了网络问题，导致客户端将连接关闭，然后服务端没有感知到，并一直给客户端发包，当客户端恢复的时候服务端就收到了RST包
![title](../../.local/static/2021/0/2/rst.1610448461773.png)

















