# Gitbook Start

## 1. Gitbook相关支持
1. 下载[Homebrew](https://brew.sh/index_zh-cn.html)，这是macOS缺失的软件包管理器，类似于Ubuntu的apt-get，可以通过简单的命名行下载依赖库，以解决软件安装过程的依赖关系

  * 安装
  ```
  /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
  ```
  * 查看版本
  ```
  ➜  ~ brew -v      
Homebrew 1.5.8
Homebrew/homebrew-core (git revision c281; last commit 2018-03-05)
  ```
2. 下载Node.js
  ```
  ➜  ~ brew install node
  ```
  安装了node后会自带npm，npm是随同Node.js一起安装的包管理工具，能解决Node.js代码部署上的很多问题，可通过以下命名查看安装版本
  ```
  ➜  ~ npm -v
  5.6.0
  ➜  ~ node -v
  v9.4.0
  ```
3. 使用npm安装Gitbook
  * ```npm install gitbook -g``` 命令行安装gitbook
  * ```npm install -g gitbook-cli``` 安装gitbook的命令行工具
  * ```gitbook -V``` 查看安装版本
  * ```npm uninstall -g gitbook``` 卸载Gitbook

## 2. Gitbook与Github关联同步
我的gitbook网址 https://www.gitbook.com/@xianzhou    
我的github网址  https://github.com/XianZhou

1. 在gitbook上创建一本新书（Book & Manual），普通用户的书籍权限都是public
2. 在github上新建一个仓库（Repositories），在用户设置中   
   SETTINGS -> Applications -> Installed Github Apps -> Configure
   在Repository access中选择可被Gitbook访问到的仓库名字，然后保存
3. 进入gitbook的新书中，SETTINGS -> Github 选择一个可访问的仓库进行同步，点击Sync，点击图标，同步成功

## 3. 使用Atom写书并完成上传
1. 创建任意文件夹，如Gitbook，为方便管理，里面创建各种书的子文件夹
2. 终端进入该书籍目录，将该目录创建为一个git项目： `git init` ，通过 `ls -a` 可以看到隐藏的.git文件夹
3. 然后为本地仓库关联一个远程仓库，并将其命名为 `origin` 即远程主机名                
    ```Shell
    git remote add origin https://github.com/XianZhou/hello.git  
    ```
4. 通过 `git remote -v` 可以查看该仓库与远程仓库的关联，准备工作完成，以下是上传步骤
5. 在Atom写好内容后，需要通过终端将其上传到远程仓库
    * 将该仓库更新为一个gitbook项目： `gitbook init`       
        * 首次会生成README.md和SUMMARY.md，分别是作为书的介绍页和目录页
        * `README.md` 是gitbook上关于该书的描述，也是首页
        * `SUMMARY.md` 决定了哪些内容是可以通过gitbook展示的，需要手动写下目录
    * `git status`   查看变动
    * `git add --a`  添加需要committed的文件，--a为所有文件
    * `git commit -m "description"`
    * `git push origin master` 上传到master分支
7. `gitbook serve` (Optional) 开启本地服务，可直接在本地查看书籍效果 localhost:4000

### 2018年3月5日 —— #1
#### 感谢晓芳学姐带本小白入门并对本篇文章的大力支持～
