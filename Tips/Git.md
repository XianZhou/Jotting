# Git的用法

## 1. Git介绍
Git是目前世界上最先进的分布式版本控制系统，是 Linus Torvalds 为了帮助管理 Linux 内核开发而开发的一个开放源码的版本控制软件，采用了分布式版本库的方式，不必服务器端软件支持
####  Git和SVN的比较
在整理Git介绍的时候看到了很多文章将Git与SVN进行对比，虽然我之前并没有接触过SVN，但是从这些比较中能够比较好的了解到Git的一些特性，于是将其主要区别汇总如下：
  * Git是分布式的，而SVN是集中式
    * Git没有中心服务器，每个人的机器上都是一个完整的库
    * Git中的绝大多数操作都只需要访问本地文件和资源，不必联网就可以看到所有的历史版本记录
  * 本地提交
    * SVN断开网络或者断开VPN就无法commit代码，但是Git可以先commit到本地仓库，且无须别人给你授权
  * 分支策略
    * SVN是复制一份代码到分支目录，Git则是在分支点做一下标记
    * SVN新建的分支会影响到所有用户，Git新建的分支，只要不提交，不会对其他用户产生影响
  * 其他
    * Git只关心文件数据的整体是否发生变化，而SVN这类版本控制系统则只关心文件内容的具体差异
    * Git克隆一个完整项目的速度非常快，SVN非常慢
    * GIT的内容完整性要优于SVN

## 2. Git工作流程
  * 克隆 Git 资源作为工作目录
  * 在克隆的资源上添加或修改文件
  * 如果其他人修改了，你可以更新资源
  * 在提交前查看修改
  * 提交修改
  * 在修改完成后，如果发现错误，可以撤回提交并再次修改并提交

## 3. Git工作区、暂存区和版本库
  * **工作区**：电脑上能看到的目录
  * **暂存区**：英文叫stage, 或index；一般存放在 ".git目录下" 下的index文件（.git/index）中，所以我们有时也把暂存区叫作索引（index）
  * **版本库**：工作区有一个隐藏目录.git，这个不算工作区，而是Git版本库

  ![git_flow](https://raw.githubusercontent.com/XianZhou/Jotting/master/img/git/git_flow.jpg)
    * 上图左边区域为工作区，右侧是版本库，其中index表示暂存区，objects为Git对象库，包含各种对象及内容
  * 说明
    1. 当工作区文件发生修改时，执行 ```git add``` 命令，会按指令更新暂存区的目录树，可通过 ```git status``` 查看前后变化。工作区修改的文件内容会被写入到对象库的一个新对象中，该对象ID被记录在暂存区的文件索引中
    2. 执行 ```git commit``` 将暂存区的目录树写到对象库中，并且master会相应更新，master指向的目录树就是提交时暂存区的目录树
    3. ```git reset HEAD``` 将暂存区的目录树重写为master分支指向的目录树，工作区不受影响
    4. ```git checkout HEAD``` 会将master分支上的全部文件或部分文件替换暂存区和工作区的文件，非常危险，不仅清除工作区上未提交的改动，也清楚暂存区未提交的改动
  * 暂存区的使用场景：```git stash```

## 4. Git基本命令
### 4.1 git init
  使用当前目录作为Git仓库，是Git的第一个命令,会生成.git目录，包含资源的元数据
### 4.2 git add
  将目录下的文件添加入暂存区 ```git add Tips/Git.md``` <br/>
  或者通过 ```git add .``` 将当前所有项目文件加入暂存区
### 4.3 git clone
  拷贝一个Git仓库到本地 ```git clone https://github.com/XianZhou/Jotting.git``` <br/>
  自己命名目录 ```git clone https://github.com/XianZhou/Jotting.git Jottings ```
### 4.4 git status
  查看上次提交之后是否有修改，  ```git status  -s``` 会简短输出结果
### 4.5 git diff
  查看git status的详细信息，默认为尚未缓存的改动<br/>
  查看已缓存的改动 ```git diff --cached```, 查看已缓存和未缓存的改动 ```git diff HEAD```
### 4.6 git commit
  将缓存区内容添加到版本库中，加提交信息 ```git commit -m "description"``` <br>
  将工作区内容添加到版本库中，省略git add ```git commit -am "description"```
### 4.7 git reset HEAD
  用于取消存在于暂存区的内容，``` git reset HEAD Tips/Git.md```<br/>
  相当于git add的逆操作
### 4.8 git mv
  重命名 ```git mv README  README.md```

## 5. Git进阶命令
### 5.1 分支管理
  * ```git branch``` 列出本地分支 ```git branch -a``` 列出本地和远程分支
  * ```git branch dev``` 创建新分支dev ```git branch -d dev``` 删除分支dev
  * ```git checkout dev``` 切换到分支dev，用该分支的 **最后提交** 的快照替换你的工作目录的内容
  * ```git checkout -b dev``` 新建dev分支并立即切换到该分支下
  * ```git merge dev``` 将dev分支合并到当前分支，合并时遇到的冲突问题见下一个章节

### 5.2 提交历史
  * ```git log``` 查看提交历史 ```git log --oneline``` 简介版
  * ```git log --oneline --graph``` 查看历史的分支、合并信息，拓扑图
  * ```git log --oneline --before={2.weeks.ago} --after={2017-02-11} --no-merges``` <br/> 查看 Git 项目中两周前且在2017年2月11日之后的所有提交

### 5.3 标签
  * ```git tag -a v1``` 为当前版本建立一个名为 **v1** 的标签，会要求输入注释（类似commit）
  * ```git tag -a v2 16ccb91``` 为16ccb91版本建立一个名为 **v2** 的标签
  * ```git tag``` 查看所有标签

### 5.4 储藏 stash
  当工作进行到一半时，需要pull最新代码，但是因为工作未完成，并不想新commit；或者工作进行一半，需要对原代码进行紧急bug修复，均可通过此命令，回到上一次commit的状态，相当于将当前代码储藏在暂存区，而工作区回到commit时状态
  * ```git stash``` 暂存当前正在进行的工作，工作区回到上一次commit时状态
  * ```git stash save "it has message"``` 暂存并且将本次暂存记录为description，方便管理
  * ```git stash list``` 打印stash栈中所有的信息
  ```
  stash@{0}: On master: it has message
  stash@{1}: WIP on master: daff907 git
  ```
  * ```git stash pop``` 从stash栈中恢复第一个stash，并且会在list中删除本次stash信息
  * ```git stash apply stash@{1} ``` 从list中找到stash版本，恢复这个stash，不会删除list里的信息
  * ```git stash clear``` 清空stash栈

### 5.5 github
  远程仓库，实现共享和共同开发
  * ``` git remote add test https://github.com/XianZhou/test.git``` 为当前本地仓库添加一个新的远程仓库，指定一个简单的别名 **test**，以便将来引用
  * ```git remote -v``` 查看当前仓库配置了哪些远程仓库，-v参数可以看到每个别名的实际链接地址
  ```
  origin	https://github.com/XianZhou/Jotting.git (fetch)
  origin	https://github.com/XianZhou/Jotting.git (push)
  test	https://github.com/XianZhou/test.git (fetch)
  test	https://github.com/XianZhou/test.git (push)
  ```
  另外，```git remote rm test``` 删除远程仓库 **test**
  * ```git pull origin master``` 从远程仓库 **origin** 拉取master分支并且合并到当前的工作区，相当于 ```git fetch``` + ```git merge```
  * ```git push origin master``` 将当前已提交的代码推送到远程仓库 **origin** 的master分支
  * ```git rebase``` 用于合并两个分支，与 ```git merge``` 类似，不同之处在于：
    *


## 6. Git相关场景说明
### 6.1 冲突解决
在多人共同操作一个分支的时候，本地提交后push到远程仓库时会出现冲突，例如：A完成本地commit，并且将代码push到远程仓库的dev分支；然后B也完成了本地commit，准备将代码push到远程仓库的dev分支，根据以上场景进行如下讨论：
  * 首先，B需要从远程拉去该分支的最新代码，进行git pull操作
  * git pull = git fetch + git merge FETCH_HEAD
  * 进行git pull操作时，会直接拉取成功，进行了auto-merging，并且成功~~~
  ```
  ➜  Jotting git:(master) git pull origin master
remote: Counting objects: 4, done.
remote: Compressing objects: 100% (4/4), done.
Unpacking objects: 100% (4/4), done.
remote: Total 4 (delta 2), reused 0 (delta 0), pack-reused 0
From https://github.com/XianZhou/Jotting
 * branch            master     -> FETCH_HEAD
   766ad71..2cca594  master     -> origin/master
Auto-merging Tips/Git.md
Merge made by the 'recursive' strategy.
 Tips/Git.md | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
  ```

    * 不会aut-merging的情况，加上 ```:branch``` 似乎就不行了...

  ```
 ➜  Jotting git:(master) git pull origin master:master
remote: Counting objects: 4, done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 4 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (4/4), done.
From https://github.com/XianZhou/Jotting
 ! [rejected]        master     -> master  (non-fast-forward)
   63c9a5c..a59780f  master     -> origin/master
   ```

  * 当两次commit同时修改了某一内容时，会出现一下提示，这时需要我们手动对这些内容进行修改
  ```
  CONFLICT (content): Merge conflict in Tips/Git.md
  Automatic merge failed; fix conflicts and then commit the result.
  ```
    * 找到这些有冲突的文件，对相应内容进行修改
    * ```git add Tips/Git.md``` 表示冲突已解决
    * ```git commit``` 重新提交

### 6.2 版本回滚
如果需要回滚到之前提交的版本，步骤如下：
  * 通过 ``` git reflog ``` 查看所有分支的操作记录，包括commit和reset操作
  ```
  2883e5c (HEAD -> master) HEAD@{0}: commit: need reset
  1af7672 HEAD@{1}: commit: begin
  ```
  通过 ```git log``` 查看如下
  ```
  commit 2883e5cdbf3391b442025e0e4b97445c3d0fa761 (HEAD -> master)
  Author: xx <xxx.xxxx@xxxxx.com>
  Date:   Thu Mar 22 18:47:59 2018 +0800

      need reset

  commit 1af7672fee2adb7558bd2143d246705613287fbb
  Author: xx <xxx.xxxx@xxxxx.com>
  Date:   Thu Mar 22 18:47:37 2018 +0800

      begin
  ```
  * ``` git reset --hard 1af7672``` 回滚到commit为begin的提交状态，查看日志

    * ```git reflog```
    ```
    1af7672 (HEAD -> master) HEAD@{0}: reset: moving to 1af7672
    2883e5c HEAD@{1}: commit: need reset
    1af7672 (HEAD -> master) HEAD@{2}: commit: begin
    ```
    * ```git log```
    ```
    commit 1af7672fee2adb7558bd2143d246705613287fbb (HEAD -> master)
    Author: xx <xxx.xxxx@xxxxx.com>
    Date:   Thu Mar 22 18:47:37 2018 +0800

        begin
    ```
    通过比较可以看出 ```git reflog``` 能够完整的看到commit和reset的日志，回滚时推荐使用，因为可以显示reset前后的commit id，可以回滚来再回滚去～
  * 上传到线上仓库 ```git push -f origin master``` ，不加参数会报错

  遗留一个问题：[公共远程分支版本回滚问题](http://blog.csdn.net/fuchaosz/article/details/52170105)

## 7. 总结
  最后附上琪栋大神给我的Git命令总结图，感谢大佬对我的很多帮助～

  ![git_summary](https://raw.githubusercontent.com/XianZhou/Jotting/master/img/git/git_summary.jpeg)











### 参考
Git教程 http://www.runoob.com/git/git-tutorial.html <br/>
详细透彻解读Git与SVN的区别（集中式VS分布式）：<br/>
http://blog.csdn.net/hellow__world/article/details/72529022 <br/>
如何理解 Git 的分布式？：https://www.zhihu.com/question/20093241
git merge 和 git rebase 小结 https://blog.csdn.net/wh_19910525/article/details/7554489
### 2018年3月6日 —— #3
