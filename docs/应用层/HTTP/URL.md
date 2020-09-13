# URL

### URL 结构

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
