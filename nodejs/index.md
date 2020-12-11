[TOC]

---

> 记录一些零散的内容。



### require引入的查找逻辑

​	当 node 遇到 `require(x)` 时，区分三种情况讨论如下：

1. x 是内置模块，比如 `http`，直接返回不再查找。
2. x 是以 **路径分隔符** 开头的，比如 `./` ：首先根据 x 所在的父模块确定绝对路径，然后分两种情况依次处理：
    1. 先当做文件，依次查找：`x`、`x.js`、`x.json`、`x.node` 文件；
    2. 找不到则当做目录，依次查找：
        * `x/package.json(用于读取其中main字段)`
        * `x/index.js`
        * `x/index.json`
        * `x/index.node`
3. x 不带路径：从父模块开始，依次确定安装目录（node_modules）

如果上述三种情况都未能找到符合条件的文件，则抛出 `Not Found`错误。



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



### path

* 文件拓展名：`path.extname(filename)`
* 文件名（带后缀）：`path.basename(filename)`
* 纯文件名：`path.basename(filename, ext)`



### npx

5.2版本开始支持：

* 调用项目安装的模块：

    ```bash
    node_modules/.bin/mocha --version
    npx mocha --version
    ```

* 避免全局安装模块

    ```bash
    npx create-react-app my-react-app
    ```

    上边的代码运行时，npx将 `create-react-app`下载到一个临时目录，使用后再删除。

* ...