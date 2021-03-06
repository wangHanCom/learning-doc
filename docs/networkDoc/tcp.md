# TCP
## TCP
- 面向连接
- 一对一通信
- 面向字节流
- 可靠传输，使用流量控制和拥塞控制
- 报头最小20字节，最大60字节

## UDP
无连接
支持一对一，一对多，多对一和多对多的通信
面向报文
不可靠传输，不使用流量控制和拥塞控制
报头开销小，仅8字节

## 三次握手
tcp连接是全双工的，数据在两个方向上能同时传递。
所以要确保双方，同时能发数据和收数据

- 第一次握手：建立连接。客户端发送连接请求报文段，将SYN位置为1，Sequence Number为x；然后，客户端进入SYN_SEND状态，等待服务器的确认；（客户端发送网络包，服务端收到了，服务端能得出结论：==客户端的发送能力、服务端的接收能力是正常的==。）
- 第二次握手：服务器收到SYN报文段。服务器收到客户端的SYN报文段，需要对这个SYN报文段进行确认，设置Acknowledgment Number为x+1(Sequence Number+1)；同时，自己自己还要发送SYN请求信息，将SYN位置为1，Sequence Number为y；服务器端将上述所有信息放到一个报文段（即SYN+ACK报文段）中，一并发送给客户端，此时服务器进入SYN_RECV状态；（服务端发包，客户端收到了。客户端得出结论：==服务端的接收、发送能力，客户端的接收、发送能力是正常的==）
- 第三次握手：客户端收到服务器的SYN+ACK报文段。然后将Acknowledgment Number设置为y+1，向服务器发送ACK报文段，这个报文段发送完毕以后，客户端和服务器端都进入ESTABLISHED状态，完成TCP三次握手。（客户端发包，服务端收到了。服务端就能得出结论：==客户端的接收、发送能力，服务端的发送、接收能力是正常的。==）
- 完成了三次握手，客户端和服务器端就可以开始传送数据。以上就是TCP三次握手的总体介绍。

总结如下：
1. 客户端发送SYN，表明要向服务器建立连接。同时带上序列号ISN
1. 服务器返回ACK（序号为客户端序列号+1）作为确认。同时发送SYN作为应答（SYN的序列号为服务端唯一的序号）
1. 客户端发送ACK确认收到回复（序列号为服务端序列号+1）

为什么需要三次握手，两次可以吗？
- 如果是两次握手，在第二次握手出现确认报文丢失，客户端不知道服务端是否准备好了，这种情况下客户端不会给服务端发数据，也会忽略服务端发过来的数据。
- 如果是三次握手，在第三次握手出现确认报文丢失，服务端在一段时间没有收到客户端的回复报文就会重新第二次握手，客户端收到重复的报文会再次给服务端发送确认报文。
- ==三次握手主要考虑是丢包重连的问题。==



## 四次挥手
- 第一次分手：主机1，设置Sequence Number和Acknowledgment Number，==向主机2发送一个FIN报文==段；此时，主机1进入FIN_WAIT_1状态；这表示主机1没有数据要发送给主机2了；（ ==主动关闭的一方发送FIN==，表示要单方面关闭数据的传输）
- 第二次分手：==主机2收到了主机1发送的FIN报文段==，==向主机1回一个ACK==报文段，Acknowledgment Number为Sequence Number加1；主机1进入FIN_WAIT_2状态；主机2告诉主机1，我“同意”你的关闭请求；（ ==服务端==收到FIN后，发送一个ACK作为确认）
- 第三次分手：==主机2向主机1发送FIN报文段==，请求关闭连接，同时主机2进入LAST_ACK状态；主机2将最后的数据发送完毕后，给主机1回复确认报文（带有FIN标识），这个才是通知主机1可以释放连接的报文；
- 第四次分手：==主机1收到主机2发送的FIN==报文段，==向主机2发送ACK==报文段，然后主机1进入TIME_WAIT状态；主机2收到主机1的ACK报文段以后，就关闭连接；此时，主机1等待2MSL后依然没有收到回复，则证明Server端已正常关闭，那好，主机1也可以关闭连接了。（==回复ACK以确认回复==）主机1收到主机2回复确认报文后，于是主机1回复确认报文给主机2。而主机2一旦收到主机1发出的确认报文就会立马释放TCP连接，所以主机2结束TCP连接的时间要比主机1早一些。

为什么握手需要三次，而挥手需要四次
- 服务端需要确保数据完整性，只能先回复客户端确认报文告诉客户端我收到了报文，进入关闭等待状态。服务端在数据发送完毕后，才回复FIN报文告知客户端数据发送完了，可以断开了，由此多了一次挥手过程。


## 标识说明
- URG：为1时，表示紧急指针有效
- ACK：确认标识，连接建立成功后，总为1。为1时确认号有效
- PSH：接收方应尽快把这个报文交给应用层
- RST：复位标识，重建连接
- SYN：建立新连接时，该位为0
- FIN：关闭连接标识

参考链接
- [TCP协议详解](https://juejin.im/post/6844903685563105293)
- [通俗大白话来理解TCP协议的三次握手和四次分手](https://github.com/jawil/blog/issues/14)
- - [金九银十，初中级前端面试复习总结「浏览器、HTTP、前端安全」](https://juejin.im/post/6869376045636648973?utm_source=gold_browser_extension)