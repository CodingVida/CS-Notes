[TOC]

---

> 记录一些零散的内容。



### 清理 require的缓存

require会自动缓存导入的内容，可以通过 `require.caceh[absolute path]` 获取。

清理方法为：`delete require.cache[require.resolve('./tools')]`。



### 内存泄漏

* 可能的原因
    * 全局变量：使用未声明的变量会直接绑定到全局对象（global、window）上
    * 闭包引用
    * 事件绑定（没有解绑）
    * 缓存爆炸）
* 使用 `Chrome Dev Tools` 和 `heapdump`工具库
    * 使用 `heapdump`生成快照文件
    * 在 Chrom Dev Tools 中选择memory tab项，然后 load快照文件。



### cluster

cluster使得nodejs具备使用多核cpu的能力（**多进程**）。

* cluster 允许设置一个 master进程 和 若干 worker进程。master 负责监控和协调 worker进程的运行；
* worker之间采用进程间通信（IPC）；
* cluster内置一个负载均衡器，采用 `round-robin`（轮训）算法进行负载均衡。

### child_process

提供了衍生子**进程**的能力。