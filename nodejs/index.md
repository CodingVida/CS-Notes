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

