---
title: "在Ubuntu和Mac OS X上安装Fish"
keywords: ["WeFox", "唯狐博客", "Ubuntu", "Mac", "Fish", "终端"]
date: 2018-11-28T17:44:12+08:00
draft: false
tags: ["Ubuntu", "Shell"]
categories: ["System"]
---

最近接触了Fish这个shell，总体感觉还不错，所以花了点时间在Ubuntu和OS X上都配置了一下。

### 环境：

Ubuntu 18.04 LTS  
OS X 10.11

#### Ubuntu
在ubuntu上，最简单的安装方法是通过最新的官方的PPA安装，具体如下：

通过PPA安装Fish
通过下面几步来安装Fish 2.x：

    sudo apt-add-repository ppa:fish-shell/release-2
    sudo apt-get update
    sudo apt-get install fish

安装好之后将Fish设置为默认的shell，这样每次打开终端的时候就会默认使用Fish：

    chsh -s /usr/bin/fish

现在Fish就装好了，通过在终端里输入fish就可以看到效果啦！

##### 给Fish安装主题
Fish有一个叫做“Oh My Fish”的框架，可以为Fish安装主题和Packages，要安装”Oh My Fish”，首先，请确保Ubuntu中已经安装了Ruby，如果还没安装，可以通过这条命令安装：

    sudo apt-get install ruby git curl

接着通过下面这条命令就可以轻松安装了：

    curl -L http://get.oh-my.fish | fish
    omf help

关于”Oh My Fish”的使用，后面会细说。当然还有其他的方法可以安装Fish，例如有.deb的package来安装等等，这里就不一一列举了。

#### Mac OS X
##### 用Homebrew安装Fish
如果你已经在使用Homebrew了，可以直接安装：

    brew install fish

##### 安装”Oh My Fish”
可以直接在终端里输入：

    curl -L http://get.oh-my.fish | fish

记得把Fish设置为默认

    chsh -s /usr/local/bin/fish

### Oh My Fish使用
具体的使用方法可以看“Oh My Fish”，先用omf安装一个budspencer的主题：

    omf install budspencer

关于这个主题的使用，可以看[这里](https://github.com/oh-my-fish/oh-my-fish/blob/master/docs/Themes.md#budspencer),装完这个主题后的效果

![Img](https://raw.githubusercontent.com/Wefox/wefox.github.io/master/post/docs/install_fish_1.png)

### 问题解决
- 乱码或某些符号不显示  
装完主题之后，可能会发现很多地方显示乱码或者不显示，例如左边的箭头，这需要安装Powerline的字体，可以在这里[下载](https://github.com/powerline/fonts)，下载之后解压，并运行./install.sh就会安装字体，安装好之后只要把终端的字体设置为任意一款Powerline字体即可。

- 右侧不显示命令运行的时间和Git的状态  
budspencer主题支持Vi模式，按ESC进入Normal模式后，按Space来切换”short long none”三种模式，按#来切换前面是显示状态还是显示往Git上更新了多少行的代码。如果还是不显示，可以卸载主题:  
    
    omf remove budspencer
    
再重新安装，如果还是不行，可以删除fish_prompt文件后重新安装主题：
    
    rm ~/.config/fish/functions/fish_prompt.fish

另外在Ubuntu下推荐使用tilda，在Mac下推荐使用Iterm2，祝大家使用愉快！