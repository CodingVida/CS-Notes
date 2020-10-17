[TOC]

---

> 记录一些零散的内容。



### 清理 require的缓存

require会自动缓存导入的内容，可以通过 `require.caceh[absolute path]` 获取。

清理方法为：`delete require.cache[require.resolve('./tools')]`。

