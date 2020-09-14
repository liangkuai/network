# SSL/TLS

SSL，Secure Sockets Layer，安全套接层。HTTPS 最早使用的加密协议，TLS 的前身，后面被标准化为 TLS。

TLS，Transport Layer Security，运输层安全协议。

![](/assets/img/应用层/HTTP/SSL(TLS)时间轴.png)


### TLS 协议的组成
TLS 包含几个子协议，你也可以理解为它是由几个不同职责的模块组成，比较常用的有记录协议、警报协议、握手协议、变更密码规范协议等。

#### 1. 记录协议（Record Protocol）
规定了 TLS 收发数据的基本单位：记录（record）。它有点像是 TCP 里的 segment，所有的其他子协议都需要通过记录协议发出。但多个记录数据可以在一个 TCP 包里一次性发出，也并不需要像 TCP 那样返回 ACK。

#### 2. 警报协议（Alert Protocol）
警报协议的职责是向对方发出警报信息，有点像是 HTTP 协议里的状态码。比如，protocol_version 就是不支持旧版本，bad_certificate 就是证书有问题，收到警报后另一方可以选择继续，也可以立即终止连接。

#### 3. 握手协议（Handshake Protocol）
握手协议是 TLS 里最复杂的子协议，要比 TCP 的 SYN/ACK 复杂的多，浏览器和服务器会在握手过程中协商 TLS 版本号、随机数、密码套件等信息，然后交换证书和密钥参数，最终双方协商得到会话密钥，用于后续的混合加密系统。

#### 4. 变更密码规范协议（Change Cipher Spec Protocol）
非常简单，就是一个“通知”，告诉对方，后续的数据都将使用加密保护。那么反过来，在它之前，数据都是明文的。


### 连接过程

**HTTP 协议里，建立连接后，浏览器会立即发送请求报文。但现在是 HTTPS 协议，它需要再用另外一个“握手”过程，在 TCP 上建立安全连接，之后才是收发 HTTP 报文。**

这个“握手”过程与 TCP 有些类似，是 HTTPS 和 TLS 协议里最重要、最核心的部分。

#### 1. ECDHE 握手过程
|  | Client | Server |
| :---: | :---: | :---: |
| Client Hello | Client（通常是浏览器）先向 Server 发出加密通信的请求。

- 客户端使用的 TLS 协议的版本，如：TLS 1.2<br />- 一个客户端生成的随机数（Client Random）<br />- 密码套件列表<br /> |  |
| Server Hello |  | Server 收到 Client 请求后，向 Client 发出回应。<br />Server 的回应包含以下内容：<br />- 确认使用的 TLS 版本<br />- 一个服务端生成的随机数（Server Random）<br />- 服务器证书（Server Certificate）<br /> |
|  |  |  |


