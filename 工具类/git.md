[TOC]

---



### 1.Git 简史

Linux的缔造者Linux Torvalds 吸取使用 BitKeeper 的经验教训开发。



### 2. Git 同其他版本管理系统的主要区别

* 分布式的：每个人的电脑上都有一个仓库
* 快照存储。
    * 每次提交更新，制作快照并保存这个快照的索引；
    * 如果文件没有改变，指向原来的文件；

### 3. Git的三种状态

仓库文件可能处在三种状态之中：

* 已提交（committed）：数据已经保存到仓库中；
* 已暂存（staged）：表示对一个已修改文件的当前版本做了标记，以便于生成相应快照；
* 已修改（modified）：表示文件已经修改了，但还未保存到仓库中。

由此引申出三个工作区域的概念：Git仓库、暂存区、工作区。



### 4. 操作

#### 4.1 远程仓库

* 关联：`git remote add origin <server>`，比如：`git remote add https://github.com/Snailclimb/test.git`
* 改动提交：`git push origin master`
* 重命名：`git remote rename oldname newname`
* 移除：`git remote rm origin`

#### 4.2 提交历史

* 日志：`git log`
    * 具体某个人的：`git log --author=name`
    * 参数：`--pretty=`，`--oneline`
* `git reflog`：记录了每一次git命令。

#### 4.3 撤销操作

* 将本次提交合并到上一次：`git commit --amend`
* 取消暂存的文件：`git reset filename`
* 撤销工作区文件修改：`git checkout filename`

#### 4.4 分支

* 新建：`git branch branchName`
* 切换：`git checkout branchName`
* 新建并切换：`git checkout -b branchName`
* 删除：`git branch -d branchName`
* 合并：`git merge branchName`
* 推送：`git push origin`

