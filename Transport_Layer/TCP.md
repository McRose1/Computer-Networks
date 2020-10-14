# TCP
传输控制协议 TCP 简介：
- 面向连接的、可靠的、基于字节流的传输层通信协议
- 将应用层的数据流分割成报文段并发送给目标节点的 TCP 层
- 数据包都有序号，对方收到则发送 ACK 确认，未收到则重传
- 使用校验和来检验数据在传输过程中是否有误

## TCP Header
![TCP Header](/images/TCP_Header.jpeg)



### TCP Flags
- URG：紧急指针标志
- ACK：确认序号标志
- PSH：push 标志
- RST：重置连接标志
- SYN：同步序号，用于建立连接过程
- FIN：finish 标志，用于释放连接

![connect](/images/handshake.jpeg)

## TCP 三次握手
第一次握手：建立连接时，客户端发送 SYN 包（syn=j）到服务器，并进入 **SYN_SEND** 状态，等待服务器确认；

第二次握手：服务器收到 SYN 包，必须确认客户的 SYN（ack=j+1），同时自己也发送一个 SYN 包（syn=k），即 **SYN+ACK 包**，此时服务器进入 **SYN_RECV** 状态；

第三次握手：客户端收到服务器的 SYN+ACK 包，向服务器发送确认包 ACK（ack=k+1），此包发送完毕，客户端和服务器进入 **ESTABLISHED** 状态，完成三次握手。

**为什么要三次握手？**

目的是为了防止已失效的连接请求报文段突然又传送到了服务端，因而产生错误，为了解决网络中存在延迟的重复分组的问题。

假定一种情况：client 发出的第一个连接请求报文段并没有丢失，而是在某个网络结点长时间的滞留了，以致延误到连接释放以后的某个时间才到达 server。本来这是一个早已失效的报文段，但 server 收到失效的连接请求报文段后，就误以为是 client 再次发出的一个新的连接请求。于是就向 client 发出确认报文段，同意建立连接。假设不采用“三次握手”，那么只要 server 发出确认，新的连接就建立了。由于现在 client 并没有发出建立连接的请求，因此不会理睬 server 的确认，也不会向 server 发送数据。但 server 却以为新的运输连接已经建立，并一直等到 client 发送数据。这样，server 的很多资源就白白浪费掉了。所以三次握手防止了服务器端的一直等待而浪费资源。


### 首次握手的隐患 —— SYN 超时
问题起因分析：
- Server 收到 Client 的 SYN，回复 SYN-ACK 的时候未收到 ACK 确认
- Server 不断重试直至超时，Linux 默认等待 63 秒（5 次时间间隔，1+2+4+8+16+32）才断开连接

针对 SYN Flood 的防护措施
- SYN 队列满后，通过 top_syncookies 参数回发 SYN Cookie
- 若为正常连接则 Client 会回发 SYN Cookie，直接建立连接

建立连接后，Client 出现故障怎么办？

保护机制：
- 向对方发送保活探测报文，如果未收到响应则继续发送
- 尝试次数达到保活探测数仍未收到响应则中断连接


## TCP 四次挥手
第一次挥手：Client 发送一个 FIN，用来**关闭 Client 到 Server 的数据传送**，Client 进入 **FIN_WAIT_1** 状态，这表示 Client 没有数据要发送给 Server 了；

第二次挥手：Server 收到 FIN 后，发送一个 ACK 给 Client，确认序号为收到序号加1（与 SYN 相同，一个 FIN 占用一个序号），Server 进入 **CLOSE_WAIT** 状态，相当于 Server 告诉 Client 我“同意”你的关闭请求，Client 进入 **FIN_WAIT_2** 状态；

第三次挥手：Server 发送一个 FIN，用来**关闭 Server 到 Client 的数据传送**，Server 进入 **LAST_ACK** 状态；

第四次挥手：Client 收到 FIN 后，发送一个 ACK 给 Server，确认序号为收到序号加1，接着 Client 进入 **TIME_WAIT** 状态，Server 收到 Client 的 ACK 报文段以后，进入 **CLOSED** 状态；此时，Client 等待 2MSL 后依然没有收到回复，则证明 Server 端已正常关闭，Client 也关闭连接，完成四次挥手。

- FIN_WAIT_1：其实 FIN_WAIT_1 和 FIN_WAIT_2 状态的真正含义都是表示等待对方的 FIN 报文。而这两种状态的区别是：FIN_WAIT_1 实际上是当 socket 在 established 状态时，它想主动关闭连接，向对方发送了 FIN 报文，此时该 socket 即进入 FIN_WAIT_1 状态。而当对方回应 ACK 报文后，则进入到 FIN_WAIT_2 状态，当然在实际的正常情况下，无论对方何种情况下，都应该马上回应 ACK 报文，所以 FIN_WAIT_1 状态一般是比较难见到的，而 FIN_WAIT_2 状态还有时常常可以用 netstat 看到。（主动方）
- FIN_WAIT_2：实际上 FIN_WAIT_2 状态下的 socket，表示半连接，也即有一方要求 close 连接，但另外还告诉对方，我暂时还有点数据需要传送给你（ACK 信息），稍后再关闭连接。（主动方）
- CLOSE_WAIT：这种状态的含义其实是表示在等待关闭。但对方 close 一个 socket 后发送 FIN 报文给自己，你系统毫无疑问会回应一个 ACK 报文给对方，此时则进入到 CLOSE_WAIT 状态。接下来呢，实际上你真正需要考虑的事情是察看你是否还有数据发送给对方，如果没有的话，那么你也既可以 close 这个 socket，发送 FIN 报文给对方，关闭连接。所以你在 CLOSE_WAIT 状态下，需要完成的事情是等待你去关闭连接。（被动方）
- LAST_ACK：它是被动关闭一方在发送 FIN 报文后，最后等待对方的 ACK 报文。当收到 ACK 报文后，也就可以进入到 CLOSED 可用状态了。（被动方）
- TIME_WAIT：表示收到了对方的 FIN 报文，并发送了 ACK 报文，就等 2MSL 后即可回到 CLOSED 可用状态了。如果 FIN_WAIT_1 状态下，收到了对方同时带 FIN 标志和 ACK 标志的报文时，可以直接进入到 TIME_WAIT 状态，而无需经过 FIN_WAIT_2 状态。（主动方）
- CLOSED：表示连接中断。

**为什么会有 TIME_WAIT 状态**？
- 确保有足够的时间让对方收到 ACK 包
- 避免新旧连接混淆

**为什么需要四次握手才能断开连接**？

因为 **TCP 是全双工模式**，这意味着，当发送方发出 FIN 报文段时，只是表示发送方已经没有数据要发送给接收方了；但是，这个时候发送方还是可以接受来自接收方的数据；当接收方返回 ACK 报文段时，表示它已经知道发送方没有数据发送了，但是接收方还是可以发送数据到发送方；当接收方也发送了 FIN 报文段时，这个时候就表示接收方也没有数据要发送了，所以发送方和接收方都需要 FIN 报文和 ACK 报文

**服务器出现大量 CLOSE_WAIT 状态的原因**？

对方关闭 socket 连接，我方忙于读或写，没有及时关闭连接
- 检查代码，特别是释放资源的代码
- 检查配置，特别是处理请求的线程配置

### RTT & RTO
- RTT：发送一个数据包到收到对应的 ACK，所花费的时间
- RTO：重传时间间隔

## TCP 流量控制机制
对发送方发送速率的控制，我们称之为流量控制。

为什么需要流量控制？
- 双方在通信的时候，发送刚的速率与接收方的速率是不一定相等的，如果发送方的发送速率太快，会导致接收方处理不过来，这时候接收方只能把处理不过来的数据存在缓存区里（失序的数据报也会被存放到缓存区里）。
- 如果缓存区满了发送方还在疯狂地发送数据，接收方只能把收到的数据报丢掉，大量的丢包会极大地浪费网络资源，因此，我们需要控制发送方的发送速率，让接收方与发送方处于一种动态平衡

如何控制？
- 接收方每次收到数据包，可以发送确定报文的时候，同时告诉发送方自己的缓存区还剩余多少是空闲的，我们也把缓存区的剩余大小称之为接收窗口大小
- 发送方收到之后，便会调整自己的发送速率，也就是调整自己发送窗口的大小，当发送方收到接收窗口大小为 0 时，发送方就会停止发送数据，防止出现大量丢包情况的发生

发送方何时再继续发送数据？
- 当接收方处理好数据，接收窗口大小 > 0 时，接收方发个通知报去通知发送方，告诉他可以继续发送数据了。当发送方收到窗口大于 0 的报文时，就继续发送数据。

不过这时候可能会遇到一个问题，假如接收方发送的通知报文，由于某种网络原因，这个报文丢失了，这时候就会引发一个问题：接收方发送了通知报文后，继续等待发送方发送数据，而发送方则在等待接收方的通知报文，此时双方会陷入一种僵局。

为了解决这个问题，我们采用了另外一种策略：当发送方收到接收窗口 = 0 时，此时发送方停止发送报文，并且同时开启一个定时器，每隔一段时间就发个测试报文去询问接收方，打听是否可以继续发送数据，如果可以，接收方就告诉他此时接收窗口的大小；如果接收窗口大小还是 0，则发送方再次刷新启动定时器。

### TCP 的滑动窗口
TCP 使用滑动窗口做流量控制与乱序重排
- 保证 TCP 的可靠性
- 保证 TCP 的流控特性

由于 TCP 支持全双工传输，因此通信的双方都拥有 2 个滑动窗口：
- 接收窗口：接收数据
- 拥塞窗口（发送窗口）：发送数据

窗口数据的计算过程：
- 发送端程序：
  - LastByteAcked
  - LastByteSent
  - LastByteWritten
- 接收端程序：
  - LastByteRead
  - NextByteExpected 
  - LastByteRcvd
- AdvertisedWindow = MaxRcvBuffer - (LastByteRcvd - LastByteRead)
- EffectiveWindow = AdvertisedWindow - (LastByteSent - LastByteAcked)

TCP 会话的发送方：
- Sent and Acknowledged 
- Sent But Not Yet Acknowledged 
- Not Sent, Recipient Ready to Receive 
- Not Sent, Recipient NOT Ready to Receive 

TCP 会话的接收方：
- Received and Acknowledged  
- Not Yet Received, Transmitter Permitted To Send 
- Not Yet Received, Transmitter May NOT Send








