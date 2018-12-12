---
title: "Ssh Login Github Problems"
date: 2018-11-29T17:33:19+08:00
draft: false
tags: ["Ubuntu"]
categories: ["System"]
---

用Hexo+Github搭建网站的时候，每次上传都需要输入github的用户名和密码，非常不方便。所以通过设置Github上的ssh设定来解决。但是配置过程中会有非常多的坑，这里总结几点我碰到过的问题。
### Host key verification failed错误

在Github上配置好之后需要通过命令

    ssh -T git@github

验证ssh是否设置成功，此时如有提示```Host key verification failed```错误，是因为```.ssh```目录下没有```known_host```文件，在```.ssh```目录下新建一个```config```文件，输入

    StrictHostKeyChecking no
    UserKnownHostsFile /dev/null

即可忽略```known_host```文件以此解决问题

### Could not open a connection to your authentication agent错误

在使用```ssh-add```命令时，如碰到此类错误，可执行如下命令解决：

    ssh-agent bash

### Permission denied错误

Permission denied错误说明当前账户没有权限去使用```/home/username/.ssh/```下的pub公钥，如果此时加sudo再执行

    sudo ssh -T git@github

则会调用```/root/.ssh/```下的pub公钥。所以解决方法就是直接使用/root/.ssh/下的公钥

    sudo ssh-keygen -t rsa -b 4096 -C “xxx@yyy.com”

但是我们是无法进入/root/.ssh/目录的，可以将其下的id_rsa.pub拷出来打开，例如

    cp /root/.ssh/id_rsa.pub ~/.ssh/

打开后再拷出其中的内容，上述```Host key verification failed```错误中的```config```文件也可通过这种方式拷进去。以后push代码的时候，都需要加上sudo即可。

