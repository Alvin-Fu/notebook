## 1、三次握手
### 服务端直接RST的真相
内核中处理TCP连接时维护两个队列SYN队列和accept队列，服务端在建立连接过程中内核的处理过程如下：
- 客户端使用connect调用向服务端发起TCP连接，内核将此连接信息放入SYN队列，返回SYN-ACK
- 服务端内核收到客户端的ACK后，将此连接从SYN对列中取出，放入ACCEPT队列
- 服务端使用accept调用将连接从ACCEPT队列取出
`查看SYN队列： cat /proc/sys/net/ipv4/tcp_max_syn_backlog`
[RST详细信息](https://github.com/Alvin-Fu/notebook/blob/master/system/linux%E5%91%BD%E4%BB%A4%E4%BD%BF%E7%94%A8/tcpdump%E5%92%8Cwireshark%E7%9A%84%E4%BD%BF%E7%94%A8.md)


## 2、四次挥手
知识点：
### 1、为什么要有time-wait
time-wait是指在tcp四次挥手的时候主动关闭方在发送完最后的ack=1包后主动关闭方的状态
time-wait的持续时间2MSL(`msl指的是报文最大生存时间`)，在工程中msl为2min即time-wait是4min
原因：
1. 为了保证ack=1得包可以到达服务端，当服务端没有收到ack=1的包时会进行超时重传fin包，客户端收到后会再次重传ack=1。如果客户端直接关闭会导致不识别fin包，导致客户端收到了一个异常的包，导致双方都异常，增加服务器的负担
2. 再四次挥手后，经过2MSL可以让再网络中的所有的报文都消失，不会影响到下一次连接

延申问题：在客户端和服务端建立了tcp连接后，一般需要客户端主动断开连接，而不是服务端？
	这个主要是因为在tcp断开连接得时候，主动断开得一方需要有time-wait(2MSL)等待,如果是服务端主动断开会导致服务端有大量得time-wait，会造成服务端得资源浪费

## 3、流量控制
主要需要做到的是控制发送端的发送速率，使得接收端可以及时接收（端到端的考虑）

## 4、拥塞控制
考虑的比较全面，

