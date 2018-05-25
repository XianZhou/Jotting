# ZSH的安装和使用

## 1. 介绍
Shell俗称壳（用来区别于核），是指“提供使用者使用界面”的软件（命令解析器），它接收用户命令，然后调用相应的应用程序 <br/>
  * Shell分为
    * 图形界面shell（Graphical User Interface shell 即 GUI shell）<br/>
    * 命令行式shell（Command Line Interface shell ，即CLI shell）<br/>
  * 查看系统中有多少种Shell，可通过 ```cat /etc/shells``` 进行查看，如：
  ```
  ➜  ~ cat /etc/shells
  # List of acceptable shells for chpass(1).
  # Ftpd will not allow users to connect who are not using
  # one of these shells.

  /bin/bash
  /bin/csh
  /bin/ksh
  /bin/sh
  /bin/tcsh
  /bin/zsh
  ```
  * ZSH (Z Shell)，外号：终极Shell，Linux用户很少使用的power-shell，这是因为大多数Linux产品安装默认使用bash shell <br/>
  ZSH的配置很复杂，一般基于一个国外大神的开源项目 [Oh My ZSH](http://ohmyz.sh) 进行配置</br>

## 2. 安装
#### 1. 下载zsh
  * Mac自带不用下
  * Redhat Linux，执行：sudo yum install zsh
  * Ubuntu Linux，执行：sudo apt-get install zsh

#### 2. Oh My ZSH 安装
  * 通过git下载项目
  ```
  git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
  ```
  * 新建zsh配置文件
  ```
  cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
  ```
  * 将默认Shell切换到zsh
  ```
  chsh -s /bin/zsh
  ```

#### 3. zsh配置文件 <br/>
配置文件在 ```~/.zshrc``` 里，可以在这里进行主题等其他配置的修改，如
```
# Set name of the theme to load. Optionally, if you set this to "random"
# it'll load a random theme each time that oh-my-zsh is loaded.
# See https://github.com/robbyrussell/oh-my-zsh/wiki/Themes
ZSH_THEME="robbyrussell"
```

## 3. ZSH的使用
#### 1. zsh与bash之间的切换
  * 切换成bash
  ```
  chsh -s /bin/bash
  ```
  * 切换成zsh
  ```
  chsh -s /bin/zsh
  ```

## 4. 遇见的问题
#### 4.1 zsh 和 bash 在python安装xgboost中出现问题
zsh无法通过anaconda打开terminals，且无法执行conda命令 <br/>
解决方案： 将zsh改成bash，重新在python安装xgboost；conda命令解决方案如下
#### 4.2 maven：mvn command not found
下载maven时，会默认在 **~/.bash_profile** 添加
```shell
# added maven
export M2_HOME=/path/apache-maven-3.5.2
export PATH=$PATH:$M2_HOME/bin
```
需要在 **~/.zshrc** 添加同样内容，不用重启终端生效
```
source ~/.zshrc
```


### 2018年3月6日 —— #2
