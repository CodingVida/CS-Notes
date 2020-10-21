[TOC]

---



### 1. 请求方法

**方法列表**：

* GET：通常用来获取资源

* HEAD：请求头部信息（即不返回消息体）

* POST：提交数据

* PUT：修改数据

* DELETE：删除数据

* CONNECT：建立连接隧道，用于代理服务器

* OPTIONS：列出对资源可行的请求方法，用来跨域请求

* TRACE：服务器通信路径。

    

> GET 和 POST 的区别：

* 语义角度：GET用于请求资源，POST用于提交数据
* 参数角度：GET请求的参数放在URL中，POST放在请求体中（更适合传输敏感信息）；同时GET接受参数的大小要比POST小得多。
* 编码角度：GET请求只能接受ASCII编码，需要进行URL编码；POST没有限制
* 缓存角度：GET请求会被浏览器缓存下来，POST不会
* 幂等性角度：GET请求是幂等性的，POST不是（幂等：执行相同的操作，结果也相同）。
* TCP角度：GET请求会把请求报文一次性发出去，POST则分为两个TCP报文：第一个用于请求头部，如果服务器响应100（Continue），则发送body部分。



### 2. 状态码

| 状态码 |               类别               |            含义            |
| :----: | :------------------------------: | :------------------------: |
|  1xx   |   Information（信息性状态码）    |        请求正在处理        |
|  2xx   |      Success（成功状态码）       |      请求正常处理完毕      |
|  3xx   |   Redirection（重定向状态码）    | 需要进行附加操作已完成请求 |
|  4xx   | Client Error（客户端错误状态码） |     服务器无法处理请求     |
|  5xx   | Server Error（服务端错误状态码） |     服务器处理请求出错     |



#### 2.1 1xx

* 100 Continue：到目前为止都是正常的，可以继续发送请求或者忽略这个响应。
* 101 Switching Protocol：协议切换（比如：http -> websocket）

#### 2.2 2xx

* 200 OK：
* 204 No Content：请求已经正常处理完成，但不需要返回数据
* 206 Partial Content：客户端进行了范围请求（比如大文件请求，分段范围请求）

#### 2.3 3xx

* 301 Moved Permanently：永久重定向，默认可缓存。

* 302 Found：临时重定向，响应中指定了Cache-Control或Expires时可缓存。

* 303 See Other：临时重定向，但明确要求使用GET方法访问重定向的资源。

    > 大多数浏览器会对 301、302、303请求的post方法改为 get方法。

    

* 304 Not Modified：缓存响应

* 307 Temporary Redirect：同302一样，但浏览器不会将post方法改为get方法。

#### 2.4 4xx

* 400 Bad Request
    * 语义有误
    * 参数有误
* 401 Unauthorized：
    * 此前未发送过请求，表明该请求需要有验证信息
    * 此前发送过请求了，表明验证失败
* 403 Forbidden：请求被拒绝
* 404 Not Found

#### 2.5 5xxx

* 500 Internal Server Error：服务器在执行请求时发生错误
* 503 Service Unavailable：服务器暂时处于超负荷或者维护状态，现在无法处理请求。



### 3. HTTP首部

有四种类型的首部字段：

* 通用首部字段：
    * Cache-Control：控制缓存行为
    * Date：创建报文日期
    * ...
* 请求首部字段：
    * Accept
    * Accept-Chartset
    * Accept-Language
    * If-Match
    * If-Modified-Since
    * User-Agent
    * Range：范围请求
    * ...
* 响应首部字段：
    * Age
    * ETag
    * Accept-Ranges：范围请求响应。（配合 `Content-Type: multipart/byteranges; boundary=00000010101`使用）
        * `Accept-Ranges: none`
        * `Accept-Ranges:bytes`
    * ...
* 实体首部字段：
    * Allow：资源可支持的http方法
    * Content-Length：资源主体的大小
    * Content-Type：资源主体的媒体类型
    * Expires：资源的过期时间
    * Last-Modified：资源的最后修改时间

### 4. HTTP的特性与缺点

#### 4.1 特性

* 灵活可拓展。语义上，只规定了基本格式；传输内容上，不仅可传输文本，还能传输图片、视频等。
* 可靠。继承了TCP的特性。
* 请求应答模式。一发一收，有来有回。
* 无状态。

#### 4.2 缺点

* 无状态：如果是需要长连接的场景，需要保存一些上下文信息。
* 明文传输：协议报文是文本格式的。
* HTTP队头阻塞：
    * 在开启长连接的情况下，共用一个TCP，同一时刻只能处理一个请求，当前请求处理时间过长的情况下，其他请求只能处于阻塞状态。
    * 解决方法：
        * 并发连接，比如对于一个域名，Chrome最多同时支持 6个 TCP长连接。
        * 域名分片：多分几个域名，比如 content1.test.com/content2.test.com



### 5. 数据传输相关问题

#### 5.1 定长不定长数据

* 定长：`Content-Length：10`
* 不定长：使用http头部 `Transfer-Encoding: chunked`
    * `Content-Length`会被忽略
    * 基于长连接持续动态推送内容：`res.write`

#### 5.2 大文件传输

> **范围请求**：Range 和 Accept-Ranges

* **客户端**发起范围请求，请求头中携带字段Range：

    * 单段请求：`Range: bytes=0-9`
    * 多段请求：`Range: bytes=0-9, 30-39`

* 服务器响应请求，响应中返回：

    * 单段请求：

        ```http
        
        HTTP/1.1 206 Partial Content
        Content-Length：10
        Accept-Ranges： bytes
        Content-Ranges：bytes 0-9/100
        ```

    * 多段请求：

        ```http
        
        HTTP/1.1 206 Partial Content
        Content-Length：xx
        Accept-Ranges：bytes
        Connection：keep-alive
        Content-type：multipart/byteranges；boundary=0000010101
        ```

        * 分割符：--[boundary]
        * 结尾符：---[boundary]--

#### 5.3 表单数据

体现在两种不同的 `Content-Type`取值：

* `application/x-www-form-urlencoded`：
    * 数据会被转换成以 `&` 分隔的键值对
    * 字符以URL编码方式编码
* `multipart/form-data`：数据会被分为多个部分。



### 6. HTTP代理

作为代理的服务器具有客户端和服务器的双重身份：

* 对于客户端而言，它作为服务器进行响应。
* 对于源服务器而言，它是发送请求的客户端。

作用：

* 负载均衡。代理服务器通过特定算法（随机算法、轮训、LRU等）做分发。
* 保障安全。比如过滤IP；利用心跳监控后台服务器状态，发现问题则将其从集群中去掉，同时上报。
* 缓存代理。将内容缓存到代理服务器，使得客户端可以直接从代理服务器上获取而不用到源服务器。

相关字段：

* Via：代理服务器在http传输中留下自己的印记：`Via: proxy_server1, proxy_server2`

* X-Forwarded-For：为谁转发。（存在修改报文的问题，解决方法是 `代理协议`，在http请求行上边写入代理信息）

    ```http
    
    // PROXY + TCP4/TCP6 + 请求方地址 + 接收方地址 + 请求端口 + 接收端口
    PROXY TCP4 0.0.0.1 0.0.0.2 1111 2222
    GET / HTTP/1.1
    ...
    ```

* X-Real-IP：客户端真实IP

* X-Forwarded-Host：客户端域名（不包括代理服务器）

* X-Forwarded-Proto：客户端协议名（不包括代理服务器）

代理缓存问题？

> ??



### 7. 跨域

> 跨域的请求是在响应的阶段被浏览器拦截下来的：当在跨域的响应中，`Access-Control-Allow-Origin`字段的内容中没有发送请求时浏览器自动添加上的 `Origin`字段内容，该请求响应就被拦截下来。

解决方法：

#### 7.1 CORS

w3c的一个标准，全称是 **跨站资源共享**，需要浏览器和服务器同时支持。

其具体工作流程区分两种请求类型：简单请求  和 非简单请求。

* 简单请求
    * 需要满足两个条件：
        * 方法为 GET、HEAD 或者 POST
        * 请求头字段的范围为：Accept、Accept-Language、Content-Language 和 Content-Type（限定为 纯文本 和表单数据格式共三种格式：`application/x-www-form-urlencoded、multipart/form-data、text/plain`）
    * 发送请求前，浏览器自动添加 `Origin` 字段，说明这个请求的源头，会和响应中的 `Access-Control-Allow-Origin` 字段做检验，如果不在其中，则拦截这个响应。
    * 如果需要携带Cookie，前端需要设置 ajax属性 `withCredentials=true`，并且响应中需要设置头部字段 `Access-Control-Allow-Credentials: true`。
* 非简单请求
    * 跟简单请求相比，非简单请求的不同在于 **预检请求** 和 **响应头**。
    * 预检请求是指，真正的请求发送之前会先发送 `OPTIONS`请求，同时携带两个关键字段：
        * `Access-Control-Request-Method`：列出真正请求中的请求方法
        * `Access-Control-Request-Headers`：列出真正请求中会使用到的请求头字段。
    * 预检请求响应中会返回支持的方法和头部字段等信息，如果符合条件，真正的请求就会发送，其过程跟简单请求一样。



#### 7.2 JSONP

* 序列化参数
* 构造script标签，插入文档
* 在window上新增回调。
* 返回响应内容为：回调函数，数据作为参数传入。
* 回调执行，删除script标签。

#### 7.3 Nginx

反向代理。



### 8. HTTP/2 的改进

#### 8.1 头部压缩

使用压缩算法 `Hpack`：

* 在服务器和客户端之间建立头部字段的 **哈希表**，二者通信只需传回 索引（比如0，1）即可
* 对内容进行哈夫曼编码。（哈弗曼编码原理：首先为所有出现的字符创建索引表，然后让出现次数多的字符的索引尽可能短。传输的时候就是传输这些索引，可以达到很高的压缩率）。



#### 8.2 多路复用

明文解析对于机器来是比较麻烦的（比如回车换行符号是内容还是分隔符），也比较耗性能。

HTTP/2 将报文转换为 **二进制帧**，其中 Headers帧用来存放头部字段，Data帧用来存放实体数据。

TCP通道上，客户端和服务器都可以给对方发送二进制帧，这种二进制帧的传输序列，就形成了 **流**（双向的）；HTTP2使用流在TCP通道上进行多个二进制帧的通信，就叫 **多路复用**。（解决了HTTP头部阻塞，但解决不了TCP头部阻塞：第一个流阻塞了，其他的也没法发送）。



#### 8.3 服务器推送

HTTP2中，服务器可以新建流推送给客户端。比如浏览器请求了HTML，服务器可以在返回HTML的基础上，把CSS文件主动推送给客户端，减少客户端的等待时间。比如Nginx上设置http2_push字段：

```Nginx
server {
    listen 443 ssl http2;
    ···//省略
    location / {
        ···//省略
        http2_push /style.css；
        http2_push /example.png;
    }
}
```



### 9.0 HTTP/3

Google开发的QUIC协议（Quick Udp Internet Connection）：基于UDP传输。存在的问题是，无状态（不知道什么时候连接，什么时候断开）。