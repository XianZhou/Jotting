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
  * 提交修改.
  * 在修改完成后，如果发现错误，可以撤回提交并再次修改并提交

## 3. Git工作区、暂存区和版本库
  * 工作区：电脑上能看到的目录
  * 暂存区：英文叫stage, 或index；一般存放在 ".git目录下" 下的index文件（.git/index）中，所以我们有时也把暂存区叫作索引（index）
  * 版本库：工作区有一个隐藏目录.git，这个不算工作区，而是Git1.

  ![git_flow](https://raw.githubusercontent.com/XianZhou/Jotting/master/img/git_flow.png)






### 参考
Git教程 http://www.runoob.com/git/git-tutorial.html <br/>
详细透彻解读Git与SVN的区别（集中式VS分布式）：<br/>
http://blog.csdn.net/hellow__world/article/details/72529022 <br/>
如何理解 Git 的分布式？：https://www.zhihu.com/question/20093241
### 2018年3月6日 —— #3
