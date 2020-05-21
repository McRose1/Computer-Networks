# TCP
传输控制协议 TCP 简介：
- 面向连接的、可靠的、基于字节流的传输层通信协议
- 将应用层的数据流分割成报文段并发送给目标节点的 TCP 层
- 数据包都有序号，对方收到则发送 ACK 确认，未收到则重传
- 使用校验和来检验数据在传输过程中是否有误

## TCP Flags
- URG：紧急指针标志
- ACK：确认序号标志
- PSH：push 标志
- RST：重置连接标志
- SYN：同步序号，用于建立连接过程
- FIN：finish 标志，用于释放连接

## TCP 三次握手
第一次握手：建立连接时，客户端发送 SYN 包（syn=j）到服务器，并进入 SYN_SEND 状态，等待服务器确认；

第二次握手：服务器收到 SYN 包，必须确认客户的 SYN（ack=j+1），同时自己也发送一个 SYN 包（syn=k），即 SYN+ACK 包，此时服务器进入 SYN_RECV 状态；

第三次握手：客户端收到服务器的 SYN+ACK 包，向服务器发送确认包 ACK（ack=k+1），此包发送完毕，客户端和服务器进入 ESTABLISHED 状态，完成三次握手。

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
第一次挥手：Client 发送一个 FIN，用来关闭 Client 到 Server 的数据传送，Client 进入 FIN_WAIT_1 状态；

第二次挥手：Server 收到 FIN 后，发送一个 ACK 给 Client，确认序号为收到序号+1（与 SYN 相同，一个 FIN 占用一个序号），Server 进入 CLOSE_WAIT 状态；

第三次挥手：Server 发送一个 FIN，用来关闭 Server 到 Client 的数据传送，Server 进入 LAST_ACK 状态；

第四次挥手：Client 收到 FIN 后，Client 进入 TIME_WAIT 状态，接着发送一个 ACK 给 Server，确认序号为收到序号+1，Server 进入 CLOSED 状态，完成四次挥手。

**为什么会有 TIME_WAIT 状态**？
- 确保有足够的时间让对方收到 ACK 包
- 避免新旧连接混淆

**为什么需要四次握手才能断开连接**？
因为全双工，发送方和接收方都需要 FIN 报文和 ACK 报文

**服务器出现大量 CLOSE_WAIT 状态的原因**？

对方关闭 socket 连接，我方忙于读或写，没有及时关闭连接
- 检查代码，特别是释放资源的代码
- 检查配置，特别是处理请求的线程配置












