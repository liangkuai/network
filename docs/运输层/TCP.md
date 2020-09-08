# TCP

Transmission Control Protocol，传输控制协议


### 首部字段

- 16 位，源端口号
- 16 位，目的端口号

- 32 位，序号（seq）

- 32 位，确认号（ack）

- 控制字段

    - ACK（确认位）：ACK = 1，表示确认号（ack）有效；在 TCP 连接建立后，所有报文段都必须把 ACK 置 1

    - FIN（终止位）：FIN = 1，表示这个报文段发送方数据已发送完，要求释放连接

    - SYN（同步位）：SYN = 1，表示是一个连接请求/连接接受报文段

- 窗口（rwnd）：表示这个报文段的发送方的接受窗口，也就是允许对方发送的数据量


### TCP 连接 - 三次握手

![三次握手](./../../assets/img/运输层/三次握手.png)

假设 A 为 Client，B 为 Server。

1. 第一次

    - 首先 Server 处于 `LISTEN`（监听）状态，等待客户的连接请求。
    - Client 向 Server 发送连接请求报文，`SYN=1`，`ACK=0`，选择一个初始的序号 x。

2. 第二次

    - Server 收到连接请求报文，如果同意建立连接，则向 Client 发送连接确认报文，`SYN=1`，`ACK=1`，确认号为 x+1，同时也选择一个初始的序号 y。

3. 第三次

    - Client 收到 Server 的连接确认报文后，还要向 Server 发出确认，`ACK=1`，确认号为 y+1，序号为 x+1。
    - Server 收到 Client 的确认后，连接建立。

#### 为什么要三次握手？

三次握手的目的是建立可靠的通信信道，说到通讯，简单来说就是数据的发送与接收，而三次握手最主要的目的就是双方确认自己与对方的发送与接收是正常的。

| 次序 | Client | Client 状态 | Server | Server 状态 |
| --- | --- | --- | --- | --- | 
| 第一次握手 |  | SYN_SEND | 自己接受正常<br>对方发送正常 | LISTEN |
| 第二次握手 | 自己发送、接收正常<br>对方发送、接收正常 | SYN_SEND |  | SYN_RCVD |
| 第三次握手 |  | ESTABLISHED | 自己发送正常<br>对方接收正常 | ESTABLISHED |

所以三次握手就能确认双发收发功能都正常，缺一不可。


### TCP 断开连接 - 四次挥手

![四次挥手](./../../assets/img/运输层/四次挥手.jpeg)

假设 A 为 Client，B 为 Server。

1. 第一次

    - Client 发送连接释放报文，`FIN=1`。

2. 第二次

    - Server 收到之后发出确认，此时 TCP Server 能向 Client 发送数据但是 Client 不能向 Server 发送数据。

3. 第三次

    - 当 Server 不再需要连接时，发送连接释放报文，`FIN=1`。

4. 第四次

    - Client 收到后发出确认，进入 `TIME-WAIT` 状态，等待 2 MSL（最大报文存活时间）后释放连接。
    - Server 收到 Client 的确认后释放连接。








### 参考

- [计算机网络常见面试题 - Snailclimb - GitHub](https://github.com/Snailclimb/JavaGuide/blob/master/docs/network/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C.md)

- [两张动图-彻底明白TCP的三次握手与四次挥手](https://blog.csdn.net/qzcsu/article/details/72861891)