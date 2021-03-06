[TOC]

---

### 进程与线程的区别

* 资源开销：
    * 每个进程都有独立的代码和地址空间，切换会有比较大的开销；
    * 线程可以看做轻量级的进程，同一类线程共享代码和地址空间，每个线程有自己的程序计数器和运行栈，线程间切换的开销比较小；
* 包含关系：
    * 一个进程可以有多个线程；
    * 线程是进程的一部分，也存在单线程进程；
* 影响关系：
    * 进程间相互独立，一个进程奔溃，不会对其他进程产生影响；
    * 一个线程崩溃，同一类线程都奔溃，导致整个进程都死掉，因此进程要比线程健壮；
* 执行过程:
    * 每个独立的进程都有程序的入口、执行顺序和程序出口；但线程不能独立运行，必须依存于进程中。

> 本质上，二者的根本区别在于：
>
> * 进程是 **操作系统** **资源分配** 的基本单位；
> * 线程是 **处理器** **任务调度和执行** 的基本单位。



### 进程间通信

> what?  how?

#### 管道

Linux命令：`netstat -tulnp | grep 8080`。

其中的竖线 `|` 即是管道，它将前一条命令的输出作为后一条命令的输入。由于竖线没有具体的名字，这种方式又叫 **匿名管道**。

这也意味着有 **具名（命名）管道**。创建方式如下：

````bash
mkfifo test
echo "this is a pip" > test
````

这条命令创建了一个名为 test 的管道，并且向其中写入了一些数据。

如果这个管道的内容没有被取出，那么这个命令就会一直停留在这里。取出操作为：

```bash
cat < test
```

可以看到test中的数据取出来了，上一条命令也结束了。

**缺点：**

* 单向；
* 效率低，比如test管道进程会一直停留到数据被取走才能返回。



#### 消息队列

将用于通信的数据放在内存某处后返回（类似于缓存）。

**缺点：**发送消息的操作即是拷贝的过程，如果数据比较大，那么读写将比较耗时。



#### 共享内存

调用操作系统的相应api（比如 `shmget`）将不同进程的虚拟内存空间对应到同一块物理映射。但存在内存竞争的问题。



#### 信号量

一个计数器（count为0|1），用来实现进程间的同步与互斥。

但内存空间被进程访问时，信号量置为0；此时有新的进程尝试访问时，发现信号量为0，即可知道该内存正被其他进程访问中。



#### Socket

不同主机上的进程通信。比如http。



### 线程

一个进程可以有多个线程，线程共享进程的地址空间（即没有自己的地址空间）。但每个线程都有自己的 程序计数器、堆栈、寄存器、状态。

通信方式：共享内存（进程的）、



### 软链接硬链接

* 软链接（符号链接）：
    * 文件用户数据块存放的是 另一文件的路径名引用
    * 可以是文件，也可以是目录；
    * 删除软链不影响指向的文件，但删除指向的文件，软链会变成死软链。
    * 应用：方便管理（复杂路径 - 简单路径）；解决文件系统空间不足的问题（软链到一个更大的空间）
* 硬链接：
    * 指向索引节点
    * 多个文件可以指向索引节点，删掉任意其中一个（即便是源文件），不影响其他
    * 不能对目录创建，而且只能对同一文件系统的文件创建。