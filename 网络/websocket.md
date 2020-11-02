

---

WebSocket是一种应用层协议，它基于TCP，并且复用HTTP的握手过程，在握手完成后升级协议（状态码为`101 Switch`，头部携带 `Upgrade:  websocket`）。

与 http1.1 的长连接不同，ws不需要一问一答的请求响应过程，服务器可以在需要的时候主动推送。

