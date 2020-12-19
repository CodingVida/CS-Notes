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
* 提交信息：
    * `git diff`：当前工作区改点（详细）
    * `git status`：当前修改文件
    * `git diff hash1 hash2`：两次提交的区别
    * `git diff branch1 branch2`：两个分支的区别
    * `git log`
    * `git log --pretty=oneline`：提交历史缩减到一行

#### 4.3 撤销操作

* 将本次提交合并到上一次：`git commit --amend --no-edit`
* 取消暂存的文件：`git reset filename`
* 撤销工作区文件修改：`git checkout filename`

#### 4.4 分支

* 列表：`git branch -al`
* 新建：`git branch branchName`
* 切换：`git checkout branchName`
* 新建并切换：`git checkout -b branchName`
* 删除：
    * `git branch -d branchName`：删除已经合并过的了
    * `git branch -D branchName`：强制删除本地分支
    * `git push --delete <remoteName><branchName>`：删除远程仓库上多余的分支
* 合并：
    * `git merge branchName`
        * `--no-ff`：保留分支的提交记录（默认）。
        * `--ff`（fast-forward，快进）：将分支提交也合并过来。
    * `git rebase`
* 推送：`git push origin`
* 拉取：
    * `git pull`：`git fetch + git merge`：会多出一次 无意义的 commit信息，表明merge了，提交线是分叉的。
    * `git pull --rebase`: `git fetch + git rebase`：合并回一条提交线。（好看，方便于code-review）

#### 4.5 忽略不跟踪文件

`.gitignore`



#### 4.6 用户配置

````bash
git config --list
git config --global user.name "yoto"
git config --global user.email "xxx@xx.com"
````



#### 4.7 回退

* reset：本质上是指针后移。
    * `--mixed`：默认方式，只回退commit信息，源代码不变，文件修改退回**工作区**。
    * `--soft`：只回退commit信息，源代码不变，文件修改退回**暂存区**。
    * `--hard`：回退整个版本（包括commit提交记录和源码都回退）。
* revert：本质上是指针后移再前移覆盖。
    * 先 revert
    * 再重新提交。
    * 如果merge到别的分支，

### 5. git 高阶操作

#### 5.1 三种合并方式

> merge、cherry-pick、patch

![image-20201026160445959](../images/git.md)



#### 5.2 删除git缓存文件

**情况：**有些情况开发者把原有不需要提交的代码提交到了远端仓库，再使用.gitignore忽略文件不生效。哪怕我们删除后再提交也没有办法忽略。这种情况下我们应该怎么解决？

**方法：**我们可以使用git rm —cache 删除原来git跟踪的文件缓存，再在.gitignore里面添加忽略文件

```bash
## 当我们需要删除暂存区或分支上的文件, 同时工作区也不需要这个文件了, 可以使用
git rm file_path 

## 当我们需要删除暂存区或分支上的文件, 但本地又需要使用, 只是不希望这个文件被版本控制, 可以使用
git rm --cached file_path       // PS: file_path 为文件路径
```



#### 5.3 强制提交

**情况**：对于多人同时开发，有些时候我们会遇到版本管理混乱的情况，远端版本错误了，但本地版本是正确的。 如何才能让强制更新远端版本，保持和本地工作区环境一样？

**方法**：强制push本地正确的版本，但是慎用。因为它是不可逆转的。

```bash
git push origin master --force
```



#### 5.4 版本回退

**情况**：有些时候开发者需要退回到某次正确的提交记录，有些时候开发者的commit错误了，这时候可以使用 git revert 和 git reset。

**方法**：revert | reset



```bash
## 强制回退到某次提交 				=> `a -> b -> c`
git reset --hard hash		# => `a -> b`
git push origin --force	// 此时是落后于线上版本的，无法直接推送，需要强制 --force

## 使用一个新的提交来覆盖回退到某提提交：保存提交commit记录, 重新commit
git revert hash 				# 后边的hash值是需要退回到的版本号，跟reset的选取不同。
git add .
git commit -m "."			  # => `a -> b -> c -> b'` 只是引入了更改，使得b‘的内容与b完全一样，
git push origin
```

#### 5.5 Tag

一个锚点（里程碑），永久地指向某次提交。

```bash
## 创建tag
git tag tagname hash(default: HEAD)
git tag -a daily/0.0.1 -m "add develop file"
git tag daily/0.0.1

## 分享tag到远端
git push origin [tagname]
git push origin --tags 

## 从某个tag创建分支
git checkout -b <newbranch> <tagname>
```



### 6.补充

#### 6.1 checkout

`git checkout`的操作意义，是移动 HEAD指针，比如：

* `git checkout bugFix`是将HEAD指针移动到`bugFix`指针（画图理解）
* `git checkout HEAD^（HEAD～num ｜ hash）`：移动到`HEAD`的前几个提交节点（或某个hash值节点）



#### 6.2 分支指针移动

```bash
git branch -f bugFix hash
git branch -f master HEAD~2
```

强制将分支指针 `bugFix`的指向 移动到某个节点.



#### 6.3 cherry-pick

```bash
git cherry-pick <提交号>...

# git cherry-pick hash1 hash2...
```

将一些提交复制到当前所在的位置（`HEAD`）下面。



#### 6.4 交互式rebase

场景：很多时候，我们并不清楚 hash记录。

```bash
git rebase --interactive(-i) hash
```

从起点hash节点开始，rebase一个分支出来。

```bash
git rebase master bugFix 
# equals 
git checkout bugFix
git rebase master

git rebase master (第二个参数默认为HEAD)
# equals 
git checkout HEAD
git rebase master
```



#### 6.5 ^num & ~num

* ^后边的数字是指第几个父节点，默认是第一个
* ～后边的数字是指回退几步

```bash
git checkout HEAD~^2~2
# 回退一步，再回退到第二个父节点，再回退两步。
```

