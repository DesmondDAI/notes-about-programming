## HTTP与HTTPS协议要点汇总

### HTTP

- 组成：**状态行**（status-line），**头信息**（header），**主体信息**（body）
<br><br>
- 状态行和头信息编码方式：**ASCII**
<br><br>
- 头信息参数：
  - `Content-Type: text/plain` --> 主体信息的格式，使用**MIME type**
  - `Accept: text/plain` （客户端） --> 客户端可以接受的`Content-Type`
  - `Content-Encoding: gzip` --> 发送数据的压缩方式
  - `Accept-Encoding: gzip, deflate` （客户端）--> 客户端可以接受的压缩方式，逗号分隔多个值
  - `Content-Length: 3485` --> 声明本次回应的数据长度，单位是Byte，用以在同一个TCP连接中区分不同数据包
<br><br>
- **管道机制（pipelining）**：指同一个TCP连接里，客户端可以一次发送多个请求，服务器按照应有顺序（不是到达顺序）做出回应
  - 缺点：“队头堵塞”（Head-of-line blocking），即前面回应特别慢的话后面的请求就需要排队等着
<br><br>
- **分块传输编码**：目的是针对一些耗时的动态操作（如：视频），为了避免服务器等待过长时间以拿到`Content-Length`，分块传输的处理方式是服务器产生一块数据就发送一块，采用“流模式”（stream）取代“缓存模式”（buffer）。
  - 做法：可以不使用`Content-Length`字段，取而代之`Transfer-Encoding`字段（请求或回应都可以），表明回应由数量未定的数据组成
  - 例子：`Transfer-Encoding: chunked` --> 每个非空数据块之前会有一个16进制的数值，表示这个块的长度，跟随一个CRLF，然后是数据本身。最后是一个大小为0的块，表示数据发送结束。
  - 例子：<br>
    ```
    HTTP/1.1 200 OK
    Content-Type: text/plain
    Transfer-Encoding: chunked

    25
    This is the data in the first chunk

    1C
    and this is the second one

    3
    con

    8
    sequence

    0
    ```
  - **疑问**：中间的块有头信息吗？

### HTTP/2

- 彻底的二进制，包括头信息和数据体，并且统称为“帧”（frame）：**头信息帧**和**数据帧** --> 好处：可以定义额外的帧，为将来的拓展打好基础
<br><br>
- 多工（Multiplexing）：复用的TCP连接不用按顺序回应请求，解决“队头堵塞”问题。如：收到A请求，但处理A请求比较耗时，期间收到B请求，服务器会先发送A请求已完成的部分，接着回应B请求，完成后在发送A请求剩下的部分。
<br><br>
- 数据流：每个请求或回应的所有数据包，称为一个**数据流（stream）**。因为HTTP/2的数据包不是按顺序发送，因此需要标记来区分不同的回应。因此每个数据流都有各自的编号。规定：客户端数据流ID一律为奇数，服务器的为偶数。数据流发送到一半时，客户端和服务器都可以发送信号`RST_STREAM`帧，以取消数据流。HTTP/1.1的取消办法是关闭TCP连接，因此会导致其他的请求回应也被终止。
- 头信息压缩：重复的字段，如`cookie`和`User-Agent`，每个请求都附带会浪费带宽，因此HTTP/2会使用`gzip`或`compress`压缩后再发送；另一方面，客户端和服务器同时维护一张头信息表，所有字段会存入表里，生成一个索引号，以后发送索引号即可。
- 服务器推送：主动向客户端发送资源

### HTTPS

- 为解决以下风险而诞生：
  - **窃听**（eavesdropping） --> **信息加密传播**
  - **篡改**（tampering） --> **校验机制**
  - **身份冒充** （pretending） --> **证书**
<br><br>
- SSL/TLS位于HTTP与TCP协议层之间
<br><br>
- 安全连接建立过程简述：
  - 客户端向服务器索要证书并验证公钥
  - 双方协商生成“对话密钥”（session key）
  - 双方使用“对话密钥”进行加密通信
<br><br>
- 每次连接需要经过**四步握手**过程建立SSL/TLS安全连接：
  - **客户端-->服务器：** 支持的加密协议版本和加密方法(supported cipher suites)，随机数(client random)，索要证书(cert)
  - **服务器-->客户端：** 确认加密协议版本和加密方法（若不支持就关闭加密通信），随机数(server random)，发送服务器证书
  - **客户端-->服务器：** 验证证书后使用包含的公钥加密第三个随机数(pre-master secret，有用生成“对话密钥”的关键)，hash所有内容用于校验
  - **服务器-->客户端：** 用私钥解开加密取得随机数，从而生成“对话密钥”，按照先前商定的加密方法和此密钥来加密内容进行通信，hash所有内容用于校验

#### SSL/TLS证书

- 证书有很多类型，首先分三个级别：
  - **域名认证**（domain validation）: 最低级别认证，可以确认申请人拥有此域名。浏览器对于此证书会显示为一把锁
  - **公司认证** （company validation）: 确认域名所有人是哪一家公司，证书里包含公司信息
  - **扩展认证** （extended validation）: 最高级别认证，浏览器地址栏会显示公司名
  <br><br>
- 证书还分三种覆盖范围：
  - **单域名证书：**只能用于单一域名，如foo.com的证书不能用于www.foo.com
  - **通配符证书：**可以用于某域名下的子域名，如*.foo.com的可以用于foo.com或www.foo.com
  - **多域名证书：**可以用于多个不同域名，如foo.com和bar.com
<br><br>
- 认证级别越高，覆盖范围越广的证书，价格越贵
<br><br>
