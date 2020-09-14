# HTTP

### HTTP 报文结构

HTTP 报文是简单的格式化数据块。包括以下 3 个部分：

   - 起始行（start line）
   - 首部（header）
   - 主体（body）

其中，起始行和首部是由行分隔的 ASCII 文本。

```
<start line>[CR][LF]
<header>[CR][LF]
[CR][LF]
<body>
```

其中，

   1. 回车符 [CR]，ASCII 码 13
   1. 换行符 [LF]，ASCII 码 10
   1. header 与 body 之间有一个空行


### URL

```
<scheme>://<user>:<password>@<host>:<port>/<path>;<param>?<query>#<frag>
```

#### 1. 协议
访问资源的协议。

#### 2. 主机和端口
http 服务默认使用 80 端口：`http://127.0.0.1:80/index.html`

#### 3. 用户名和密码

#### 4. 路径（path）
路径说明了资源在服务器上的位置，即资源的相对路径。

#### 5. 参数（param）
路径后可能带参数，与其他部分和参数之间都用逗号;隔开，形式 `key=value`。

例如，当浏览器禁用了 Cookie 时，开发人员在访问 Tomcat 服务时，可以将会话标识 jsessionid 作为参数加在 URL 上。

如：`http://localhost:8080/index.html;jsessionid=123`

#### 6. 查询字符串（query）
类似与查询条件，与参数部分用问号?隔开，查询键值间用&隔开，形式 `key=value`。

如：`http://localhost:8080/index?id=1&name=abc`

#### 7. 片段


### HTTP 状态码

| 类型 | 具体含义 | 常见状态码 |
| -- | -- | -- |
| 1xx | 提示信息，表示目前是协议处理的中间状态，还需要后续操作 | |
| 2xx | 成功，报文已经收到并被正确处理 | 200、204、206 |
| 3xx | 重定向，资源位置发送变动，需要客户端重新发送请求 | 301、302、304 |
| 4xx | 客户端错误，请求报文有误，服务器无法处理 | 400、403、404 |
| 5xx | 服务器错误，服务器在处理请求时内部发生了错误 | 200、204、206 |





### 请求方法

