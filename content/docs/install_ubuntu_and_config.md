---
title: "Ubuntu的安装及配置"
keywords: ["WeFox", "唯狐博客", "Ubuntu", "Install", "安装", "配置"]
date: 2018-12-11T14:26:11+08:00
draft: false
tags: ["Ubuntu"]
categories: ["System"]
---

(本文会持续更新，有需要的朋友可收藏或关注我的博客https://wefox.me)  

因为经常要安装Ubuntu的原因，每次装完都要配置，备份也不方便，所以写这篇博客记录一下。

## 前言
之前安装了Btrfs的系统，并且用snapper做自动备份，刚开始用的还行，系统玩坏了就重新Rollback一下，过了一段时间之后发现系统空间老是不足，一查才发现Snapper目录动辄一两百G，本来给Ubuntu就只装在一个256G的SSD上，这哪儿受得了，果断换回Ext4。

## 安装Ubuntu
这里安装的是Ubuntu 18.04LTS，具体安装步骤就不说了，上网随便找个Universal USB Installer安装即可，也不需要用啥UltraISO之类的，又要破解又要安装的。稍微说一下分区吧，本人两块硬盘，一块120G的SATA固态，一块M.2支持Nvme的256G固态，之前分区的时候是把```/```目录分在SATA盘下，```/home```分在256G的盘下，后来发现备份也不好备份，所以后来干脆就都丢在256G的SSD上，120G的SATA盘就纯粹拿来挂载用了，具体分区是启动分区100M，```/swap```分区16G，因为我内存是16G的，小了没用，也没必要按网上说的分内存的两倍大小给交换分区，够用就行了，然后一个```/```分区，一个```/home```分区，至于大小的话，我是```/home```分的大一点，其他的就不分了。

## 终端
安装好Ubuntu系统后，第一件事儿当然就是终端了，我用的是```Tilda + Fish```。安装Tilda很简单，直接

    sudo apt install tilda

即可。至于Fish的配置及效果，大家可以看[这里](https://wefox.me/post/install_fish_on_ubuntu_and_osx/)。至于开机自启，直接系统里搜```startup```吧。

## 中文输入法
接下来肯定是中文输入法了，每次都被输入法弄的很头疼，整了半天，终于顺手了，其实安装和配置很简单，首先：

    sudo apt install fcitx

安装输入法，接下来，打开```Settings```（我的Ubuntu是英文版，中文版就打开系统的“设置”，下同），里面不需要修改，**不要添加中文**，保持下图即可  

![Img](https://raw.githubusercontent.com/Wefox/wefox.github.io/master/post/img/install_ubuntu_1.png)

然后点击上图中的```Manage Installed Languages```，选择```fcitx```

![Img](https://raw.githubusercontent.com/Wefox/wefox.github.io/master/post/img/install_ubuntu_2.png)

接着点击屏幕右上角的键盘，进入下图界面添加```pinyin```

![Img](https://raw.githubusercontent.com/Wefox/wefox.github.io/master/post/img/install_ubuntu_3.png)

要添加```pinyin```只需点击左下角的```+```，然后取消```only show```前面的勾，直接搜索```pinyin```即可

![Img](https://raw.githubusercontent.com/Wefox/wefox.github.io/master/post/img/install_ubuntu_4.png)

## 交换Caps和Ctrl
本人的按键习惯是把Caps当Ctrl用，可以使用gnome-tweak-tool工具

    sudo apt install gnome-tweak-tool

装完之后按下图更改

![Img](https://raw.githubusercontent.com/Wefox/wefox.github.io/master/post/img/install_ubuntu_5.png)

## 梯子
没有梯子实在不会用电脑，因为有买[国外主机](https://bwh8.net/clientarea.php?action=products)自己架梯子，需要shadowsocks，之前没用UI版本的，着实不太方便，需要自己配置还要设置开机自启，有点麻烦，而这些功能UI版本都有而且会在任务栏有图标，方便切换，还有很多实用功能，果断使用UI版，具体下载地址在[这里](https://github.com/shadowsocks/shadowsocks-qt5/releases)，说明还有源码可以看[这里](https://github.com/shadowsocks/shadowsocks-qt5/wiki/Installation)，因为下载的是AppImage格式，所以可以系统直接搜```startup```然后选择AppImage路径即可开机自启。

至于浏览器端的配置简单说一下，先安装SwitchyOmega插件，Proxy设置成```SOCKS5```，代理服务器就```127.0.0.1```，代理端口是shadowsocks设置里的```Local Port```，自己去ss里看一下就知道了，默认1080。```auto switch```的```Rule List Config```选```AutoProxy```，URL填

    https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt

剩下的按自己的喜好配置吧。

## VSCode
这玩意儿也是必须的（当然，用Vim Emacs的大神们就直接跳过吧）  
[下载地址](https://code.visualstudio.com/docs?start=true)

## 安装VMWare Workstation 15 Pro
先去VMWare[官网](https://my.vmware.com/web/vmware/downloads)下载一个.bundle的安装文件，然后为其添加可执行权限并安装

    sudo chmod +x *.bundle
    sudo ./*.bundle         //*均为你下载的.bundle文件的文件名

如果出现```Before you can run VMware several modules must be compiled and loaded into the running kernel```错误，可以先尝试该命令：

    sudo vmware-modconfig --console --install-all

这个命令会安装VMware全部的模块，或者可以尝试

    sudo apt-get install build-essential linux-headers-$(uname -r) open-vm-dkms
    sudo ln -s /usr/src/linux-headers-$(uname -r)/include/generated/uapi/linux/version.h /usr/src/linux-headers-$(uname -r)/include/linux/version.h

然后再执行：

    sudo vmware-config-tools.pl

最后再执行一遍

    sudo vmware-modconfig --console --install-all

或者再安装.bundle文件应该就可以了。

## 更改Nvidia显卡风扇转速
在```/etc/X11/xorg.conf```文件中添加``` Option "Coolbits" "4"```，如下

    Section "Device" 
    Identifier "Device0" 
    Driver "nvidia" 
    VendorName "NVIDIA Corporation" 
    Option "NoLogo" "True" 
    Option "Coolbits" "4" 
    EndSection

不用每个字段都一样，只要有``` Option "Coolbits" "4"```这行就可以了，然后重启，在

    nvidia-settings

里就可以更改风扇转速了

![Img](https://raw.githubusercontent.com/Wefox/wefox.github.io/master/post/img/install_ubuntu_6.png)

或者

    nvidia-settings -a "[gpu:0]/GPUFanControlState=1" -a "[fan:0]/GPUTargetFanSpeed=100"

如果没有```/etc/X11/xorg.conf```该文件，这时需要

    sudo nvidia-xconfig
    sudo nvidia-xconfig --cool-bits=4

然后再查看是否添加了``` Option "Coolbits" "4"```这行，没有的话重复上述步骤手动添加一下。

## Reference
https://blog.csdn.net/zwliang98/article/details/80851022  
https://blog.csdn.net/jiandanjinxin/article/details/75045232