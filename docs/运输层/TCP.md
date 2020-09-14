# TCP

Transmission Control Protocol，传输控制协议。数据单元：TCP 报文段。

TCP 是面向连接的、可靠的、基于字节流的运输层协议。

- 面向连接，一对一，全双工
- 可靠，保证报文段一定能够到达接收端
- 字节流


### 首部

![TCP首部](./../../assets/img/运输层/TCP首部.png)

- 源端口号，16 位
- 目的端口号，16 位

- 序号（seq），32 位
    - 表示本报文段数据载荷的第一个字节的序号
    - 建立连接时，生成随机数作为初始值
    - 用来解决报文段乱序问题

- 确认号（ack），32 位
    - 表示下次期望收到的报文段的 seq
    - 发送方收到这个确认号，就可以认为这个确认号之前的数据都被对方正常接收了

- 控制字段

    - ACK（确认位）：ACK = 1，表示确认号（ack）有效；在 TCP 连接建立后，所有报文段都必须把 ACK 置 1

    - RST（复位）：RST = 1，表示 TCP 连接中出现异常，必须释放连接，然后重连（还可以用来拒绝非法报文段或拒绝打开连接）

    - SYN（同步位）：SYN = 1，表示是一个连接请求/连接接受报文段

    - FIN（终止位）：FIN = 1，表示这个报文段发送方数据已发送完，要求释放连接

- 窗口（rwnd），16 位
    - 表示这个报文段的发送方的接收窗口，也就是允许对方发送的数据量
    - 作为发送方设置其发送窗口的参考值
    - 以接收方的接收能力来控制发送发的发送能力，称为流量控制


### TCP 连接管理 - 建立连接 - 三次握手

> 建立 TCP 连接要解决的三大问题：
>
> 1. 让 TCP 双方能够确知对方的存在
> 2. 让 TCP 双方能够协商一写参数；如：最大报文段长度 MSS、是否使用窗口扩大选项和时间戳选项等
> 3. 让 TCP 双方能够对系统资源进行分配；如：缓存、变量

![三次握手](./../../assets/img/运输层/TCP-三次握手.png)

**假设 A 为 TCP Client，B 为 TCP Server；Server 现处于 `LISTEN` 状态。**

1. 第一次

    - **Client 向 Server 发送「 TCP 连接请求报文段」，并进入 `SYN_SEND` （同步已发送）状态。**
    - 「 TCP 连接请求报文段」中，`SYN=1`，`seq=x`。

> TCP 规定，SYN 报文段不能携带数据，但需要消耗掉一个序号。

2. 第二次

    - **Server 收到「 TCP 连接请求报文段」后，如果同意建立连接，就向 Client 发送「 TCP 连接请求确认报文段」，并进入 `SYN_RCVD` （同步已接收）状态。**
    - 「 TCP 连接请求确认报文段」中，`SYN=1`，`ACK=1`，`seq=y`，`ack=x+1`。

3. 第三次

    - **Client 收到「 TCP 连接请求确认报文段」后，还要向 Server 发送一个普通的 TCP 确认报文段，并进入 `ESTABLISHED` （连接已建立）状态。**
    - 这个普通的 TCP 确认报文段中，`ACK=1`，`seq=x+1`，`ack=y+1`，**可携带数据**。
    - Server 收到确认报文段后，也进入 `ESTABLISHED` （连接已建立）状态。

> TCP 规定，ACK 报文段可以携带数据，但是如果不携带数据则不消耗序号。

#### 为什么最后还要发送一次确认（能否改为两次握手）？

**1. 为了防止旧的重复连接初始化造成混乱和浪费资源**

如果使用两次握手，有这样一种场景，

- Client 发送了第一个连接请求报文段 r1，因为在网络结点中滞留的时间太长，Client 迟迟没有收到连接请求确认报文段，以为 Server 没有收到，此时重新向 Server 发送第二个连接请求报文段 r2，然后 Client 和 Server 经过两次握手完成连接，传输数据，然后关闭连接。

- 此时之前滞留的第一个连接请求报文段 r1，终于到达了 Server，这个报文段本该是失效的，但是两次握手的机制会让 Server 再次进入 `ESTABLISHED` 状态，这将导致不必要的错误和资源的浪费。

使用三次握手，就算是第一次失效的连接请求报文段传送过来了，Server 接收到了并且回复了连接请求确认报文段，但是 Client 已关闭不会再次发出确认报文段。由于 Server 收不到确认，就知道 Client 并没有请求连接。

**2. 让双方确认自己与对方的发送与接收是正常的**

| 次序 | Client | Client 状态 | Server | Server 状态 |
| --- | --- | --- | --- | --- | 
| 第一次 |  | CLOSE -> SYN_SEND | Server 接收正常<br>Client 发送正常 | LISTEN |
| 第二次 | Client 发送、接收正常<br>Server 发送、接收正常 | SYN_SEND | Server 为 TCP 连接分配缓存、变量 | LISTEN -> SYN_RCVD |
| 第三次 | Client 为 TCP 连接分配缓存、变量 | SYN_SEND -> ESTABLISHED | Server 发送正常<br>Client 接收正常 | SYN_RCVD -> ESTABLISHED |


### TCP 连接管理 - 释放连接 - 四次挥手

![四次挥手](./../../assets/img/运输层/TCP-四次挥手.jpeg)

**假设 A 为 TCP Client，B 为 TCP Server。**

1. 第一次

    - Client 应用进程主动关闭 TCP 连接。（主动关闭连接）
    - **Client 发送「 TCP 连接释放报文段」，并进入 `FIN_WAIT_1` （终止等待1）状态。**
    - 「 TCP 连接释放报文段」中，`FIN=1`，`ACK=1`，`seq=u`，`ack=v`。

> TCP 规定，FIN 报文段不携带数据也要消耗掉一个序号。

2. 第二次

    - **Server 收到「 TCP 连接释放报文段」后，会发送一个普通的确认报文段，并进入 `CLOSE_WAIT` （关闭等待）状态。**
    - 确认报文段中，`ACK=1`，`seq=v`，`ack=u+1`。
    - TCP Server 通知应用进程：Client 要断开 TCP 连接。
    - **此时 TCP 连接处于半关闭状态，Client 不能再发送数据，但 Server 还可以继续发送数据。**
    - Client 收到确认报文段后进入 `FIN_WAIT_2`（终止等待2）状态，等待 Server 发出的「 TCP 连接释放报文段」。

3. 第三次

    - 当应用进程没有数据要发送了，就通知 TCP Server 释放连接。（被动关闭连接）
    - **Server 发送「 TCP 连接释放报文段」，并进入 `LAST_ACK` （最后确认）状态。**
    - 「 TCP 连接释放报文段」中，`FIN=1`，`ACK=1`，`seq=w`，`ack=u+1`。

4. 第四次

    - **Client 收到「 TCP 连接释放报文段」后，发送一个普通的确认报文段，并进入 `TIME_WAIT` （时间等待）状态，在等待 2 MSL（最大报文端存活时间）后才进入 `CLOSE` 状态**。
    - 确认报文段中，`ACK=1`，`seq=u+1`，`ack=w+1`。
    - Server 收到确认报文段后，进入 `CLOSE` 状态。

#### 为什么释放连接需要四次挥手？

建立连接时，Server 在 `LISTEN` 状态下收到建立连接请求的 SYN 报文段后，把 ACK 和 SYN 放在一个报文段里发送给 Client。

**但是在释放连接时，Server 收到 FIN 报文段，只是表示 Client 不再发送数据，但是还能接收数据，而且 Server 可能还有数据没发送完。所以 Server 先发送一个 ACK 报文段，等到数据发送完才发送 FIN 报文段给 Client。**

所以被动关闭连接的一方的 ACK 和 FIN 报文段一般会分开发送，这就是为什么至少要四次挥手。

#### 为什么需要 `TIME_WAIT` 状态？

> 主动发起关闭连接的一方，才会有 `TIME-WAIT` 状态。

**1. 保证最后的 ACK 报文段能被「被动关闭方」接收，正常关闭连接**

如果没有 `TIME-WAIT` 状态，Client 在发送完最后一个 ACK 报文段之后就进入 `CLOSE` 状态。

- 如果这个 ACK 报文段丢失，Server 就会一直处于 `LAST_ACK` 状态，并超时重传 FIN 报文段，浪费资源。
- 接着 Client 发起新的建立连接请求，Server 会返回 RST 报文段给 Client，新的连接建立的过程就会被终止。

有了 `TIME_WAIT` 状态，在最后一个 ACK 报文段丢失，Server 超时重传 FIN 报文段的情况下，Client 就会重传最后一个 ACK 报文段，并重置 2MSL 计时器。

**2. 防止本次连接的数据出现在以后的新连接中**

进入 `TIME_WAIT` 状态，经过 2MSL 时间，足以让本次连接内所产生的所有报文段从网络中消失，这样新的连接中不会出现旧连接的报文段。

#### 为什么 `TIME_WAIT` 等待的时间是 2MSL？


### 保活计时器

每当收到数据，就重置保活计时器。

如果保活计时器定时周期内，没有收到对方的数据，计数器到期后就向对方发送一个「探测报文段」，如果连续几个探测报文都没有得到响应，就认为当前的 TCP 连接已经死亡，系统内核将错误信息通知给上层应用程序，关闭连接。


### 参考

- [5.3.1 TCP协议特点和TCP报文段格式 - 王道论坛 - bilibili](https://www.bilibili.com/video/BV19E411D78Q?p=63)
- [5.9 TCP报文段的首部格式 - 湖科大教书匠 - bilibili](https://www.bilibili.com/video/BV1c4411d7jb?p=66)
- [5.3.2 TCP连接管理 - 王道论坛 - bilibili](https://www.bilibili.com/video/BV19E411D78Q?p=64)
- [5.8.1 TCP的运输连接管理 - 湖科大教书匠 - bilibili](https://www.bilibili.com/video/BV1c4411d7jb?p=64)
- [两张动图-彻底明白TCP的三次握手与四次挥手](https://blog.csdn.net/qzcsu/article/details/72861891)
- [35 张图解：被问千百遍的 TCP 三次握手和四次挥手面试题 - 小林coding的文章 - 知乎](https://zhuanlan.zhihu.com/p/125715300)