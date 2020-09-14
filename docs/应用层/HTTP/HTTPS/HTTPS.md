# HTTPS

### 为什么需要 HTTPS？

因为 HTTP 不安全，明文传输，并且整个传输过程完全透明，可以轻松截获、修改或者伪造请求/响应报文。导致 3 种风险：

- 窃听风险
- 篡改风险
- 伪装风险（主要是伪装成服务端）

而 HTTPS 具有 3 种特性来保证安全性：

- 加密（防窃听）
- 校验（防篡改）
- 认证（防伪装）


### HTTPS 的实现基础

HTTPS 把下层的传输协议由 TCP 协议改成了 SSL/TLS 协议，由 SSL/TLS 协议实现安全通信。

![HTTPS分层](./../../../../assets/img/应用层/HTTP/HTTPS分层.png)


### HTTP 和 HTTPS 的区别？

| 区别 | HTTP | HTTPS |
| -- | -- | -- |
| 安全性 | 明文传输，不安全 | 密文，安全 |
| 端口 | 80 | 443 |
| 连接 | 仅 TCP 连接 | TCP 连接 + SSL/TLS 握手 |


### SSL/TLS 协议






### 参考

- [硬核！30 张图解 HTTP 常见的面试题 - 小林coding的文章 - 知乎](https://zhuanlan.zhihu.com/p/111716047)
- [看完还不懂HTTPS我直播吃翔 - winwill2012的文章 - 知乎](https://zhuanlan.zhihu.com/p/25976060)
- [SSL/TLS协议详解(下)——TLS握手协议](https://xz.aliyun.com/t/2531)