[TOC]

---

### 洋葱模型的原理

洋葱模型是koa执行中间件的流程模型。

在 app.use 挂载中间件的时候，内部函数将中间件存储到内部维护的一个队列中；在响应请求的回调函数内，通过`compose`函数对这些中间件进行编排，返回一个接收 `context` 和 `next` 作为参数 、返回值为 `promise` 的函数；该函数在内部递归地调用中间件。



 ### 中间件错误

中间件错误会被 ctx.onerror 捕获，然后通过 `this.app.emit`分发事件，可以通过 `app.onerror` 进行监听。



### co原理

不断调用 generator的 next方法到达自动执行的目的。async、await类似。



