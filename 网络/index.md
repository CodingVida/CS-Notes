[TOC]

### 1. 浏览器的缓存机制

<img src="https://upload-images.jianshu.io/upload_images/3174701-8e74b69ad9376710?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp" alt="深入理解浏览器的缓存机制"  />



#### 1.1 缓存位置

> 浏览器查找缓存文件之前（第一次请求后，携带回缓存标识和规则），会依次查找以下位置，在都没有命中的情况下，才会发起请求。
>
> ![](https://upload-images.jianshu.io/upload_images/3174701-de3d6e025582103a?imageMogr2/auto-orient/strip|imageView2/2/w/670/format/webp)

* service worker：相当于浏览器侧的服务代理，可由程序员自由控制缓存哪些内容，但只能用于https协议中，且firefox不支持。
* memory cache：关闭tab页后销毁。
* disk cache：大容量、时效长。
* push cache：HTTP/2的内容，在 session会话中存在，局限比较大。

#### 1.2 缓存策略

##### 1.2.1 强缓存

​	**不会向服务器发送请求**，直接从缓存中读取。返回状态码为 200；size 显示 `from memory cache` 或者 `from disk cache`。

​	通过设置两种 HTTP Header 实现：`Expires` 和 `Cache-Control`

* Expires

    * 缓存过期时间，一个服务器端的具体时间，Expires = max-age + 请求时间。
    * 配合 Last-Modified 使用
    * HTTP 1.0 的产物，会受到本地时间的影响，如果修改可能会影响缓存失效。

* Cache-Control

    * 优先级更高

    * 可以在请求头 或 响应头 中使用，并且可以组合多个指令。

    * ![](https://upload-images.jianshu.io/upload_images/3174701-8ff981603cdfded0?imageMogr2/auto-orient/strip|imageView2/2/w/562/format/webp)

    * no-cache比较有误导性，不是不缓存，而是每一次在使用缓存之前先跟服务器进行确认（即进行协商缓存）

    * 如何使用这些指令？

        ![](https://upload-images.jianshu.io/upload_images/3174701-3fa81f5e9efac5af?imageMogr2/auto-orient/strip|imageView2/2/w/820/format/webp)

    

##### 1.2.2 协商缓存

​	协商缓存就是在强缓存失效的情况下，由浏览器携带缓存标识向服务器发起请求，由服务器根据这些标识决定是否使用缓存的过程。

​	有两种情况：协商缓存生效，返回 304 和 Not Modified；协商缓存失效，返回200 和 请求结果。

​	通过设置两种 HTTP Header 实现：Last-Modified 和 ETag

* Last-Modified 和 If-Modified-Since
    * 文件在服务器上的最后修改时间
    * 弊端：精确到秒，服务器在秒级内改动文件，不可感知。
* ETag 和 If-None-Match
    * 服务器生成的文件唯一标识，资源变动，则ETag改变
    * 优先级高于Last-Modified，但性能要稍低一些（hash计算）

#### 1.3 缓存机制

![](https://upload-images.jianshu.io/upload_images/3174701-9d9e8b52a18ed35a?imageMogr2/auto-orient/strip|imageView2/2/w/519/format/webp)

使用两种缓存策略，整个缓存机制则为：强制缓存策略先于协商缓存，在强制缓存生效的情况下，直接使用缓存内容；失效的情况下再进行协商缓存，由服务器确定是否生效，生效则返回304(Not Modified)，继续使用缓存；失效则返回200、请求的内容和缓存标识，再存入浏览器中。

> 如果没有设置缓存策略，浏览器采用一个启发式的算法，通常使用响应中的 Date 减去 Last-Modified 的 10% 作为缓存时间。

#### 1.4 实际场景

* 时常变动的资源：Cache-Control：no-cache
* 不常变动的资源：Cache-Control：max-age=42342

#### 28.5 用户行为对浏览器缓存的影响

* 打开网页，地址栏输入地址： 查找 disk cache 中是否有匹配。如有则使用；如没有则发送网络请求。
* 普通刷新 (F5)：因为 TAB 并没有关闭，因此 memory cache 是可用的，会被优先使用(如果匹配的话)。其次才是 disk cache。
* 强制刷新 (Ctrl + F5)：浏览器不使用缓存，因此发送的请求头部均带有 `Cache-control: no-cache`(为了兼容，还带了 `Pragma: no-cache`),服务器直接返回 200 和最新内容。



### 2. 浏览器同源策略

* 同源 = 协议 + 域名 + 端口 均相同。 （域名指向 IP）
* 目的是为了保证用户的信息安全，只是对js进行限制，其他诸如img、script脚本资源请求都不会（字体文件也有限制）。
* 主要限制三方面：
    * js无法访问其他域下的cookie、localStorage
    * js无法访问其他域下的DOM
    * ajax无法发送跨域请求。

### 3. 跨域

[常见跨域方案](https://segmentfault.com/a/1190000011145364)

* jsonp
* cors: “跨域资源访问”，需要 服务器 和 前端 同时支持，
* document.domain：同主域
* window.name
* postMessage
* websocket：没有跨域限制
* nginx或node中间层

### 4. cookie

* 是由服务器提供，存储在浏览器，用于维护会话状态信息的数据。

* 服务器通过 `Set-Cookie`响应头部设置cookie

* 属性：

    * name/value

    * Expires

        * 一个具体的过期时间
        * 如果缺省，则为会话cookie，保存在浏览器内存中。

    * Max-Age：如果与Expires同时存在，优先级更高。

        * 正数：过期秒数
        * 负数：会话Cookie，保存在内存中。
        * 0：立即删除

    * Domain

    * Path（同Domain一起组合成Cookie的作用域）

    * Secure：标识只应通过HTTPS请求发送给服务器。

    * HTTPOnly：不允许js访问。

    * SameSite：上下文限定。

        * Strict：只在第一方上下文中发送，不会与第三方网站发起的请求一起发送。
        * Lax：允许第三方网站发起的GET请求一起发送，浏览器的默认值。
        * None：在所有上下文中发送，即允许跨域发送（以前的默认值）。

        

