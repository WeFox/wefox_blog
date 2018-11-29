---
title: "Create_blog_on_github_by_hugo"
date: 2018-11-29T16:20:02+08:00
draft: false
---

## 前言
Hugo是使用 Go 语言开发的开源静态网站生成工具，相对于Hexo，速度快是 Hugo 的一大特点，在本地修改后，它会自动渲染。

## 安装Hugo
虽然在Ubunut上可以通过
    sudo apt install hugo

来安装，但是，强烈不推荐这样做，因为安装的版本太低，在build的时候（例如安装主题的时候）会提示很多错误。  

推荐到[hugo官网下载](https://github.com/gohugoio/hugo/releases)下载符合系统的最新版本，这里我们下载hugo_xxx_Linux-64bit.deb  

下载好之后进入下载的目录(xxx代表版本号，请自行替换)
    sudo dpkg -i hugo_xxx_Linux-64bit.deb
    sudo apt install -f

```<!--more-->```

## 创建博客
    hugo new site blog  //blog就是生成blog之后存放的文件夹

hugo会自动创建blog文件夹并生成需要的文件

## 安装主题
到[hugo官网](https://themes.gohugo.io)上可以找到很多主题，通过Demo可以预览主题，每种主题的安装方式都会有说明，自行按需安装即可。

安装完主题之后需在blog根目录下配置  config.toml，每个主题配置不同，这里就不一一说明了，具体可以查相关主题的配置文档。

## 生成博客
直接运行
    hugo server -D
就会生成博客，可以在http://localhost:1313 中查看效果

## 在Github上部署
###配置信息并关联ssh
首先，先配置Github账号信息
    git config –global user.name “your_name” 
    git config –global user.email “your_email@youremail.com”

然后生成 SSH key 与 github 联系
    ssh-keygen -t rsa -C “your_email@youremail.com”

your_email@youremail.com 改为你的邮箱，之后会要求确认路径和输入密码，我们这使用默认的一路回车就行。成功的话会在根目录下生成```.ssh```文件夹，进去这个文件夹，打开```id_rsa.pub```，复制里面的内容。
然后登录[github](www.github.com)，如果没有就注册一个账号，登录后右上角 头像 -> Settings —> SSH nd GPG keys —> New SSH key。把公钥(id_rsa.pub)粘贴到 key 中，填好 title 并点击 Add SSH key

关联完之后可以在终端输入
    ssh git@github.com

显示如下界面![](https://github.com/Wefox/wefox.github.io/master/post/img/create_blog_1.png)代表关联成功。

### 推送到 GitHub 上
Github pages 分为两种：一种是项目主页，每个项目都可以有一个，一个用户主页可以由多个项目主页；另一种是用户主页，一个用户只能有一个。
我这里采用用户主页，使用个人主页作为 Host 也相对更简单一点。

首先，需要在Github上创建两个单独的 repo：website-hugo和xxx.github.io，新建一个 repository，创建时，只需要填写 Repository name 即可，可以顺便创建```README```文件，第二个repo的名字格式必须为user_name.github.io，其中 user_name 必须与你的```github```用户名一样，这是 github pages 的特殊命名规范。

在blog根目录运行
    hugo
后会在blog根目录生成一个public文件夹，这个文件夹就是博客的内容了，把public文件夹托管到xxx.github.io上，将blog根目录下除了public的文件托管到website-hugo上。

### 托管
如果到这一步都顺利的话，我们可以先删除```public```文件夹(如果有这个文件夹的话)
    rm -fr public/

然后把```public/```目录添加为```submodule```
    git init git submodule add git@github.com:XXX/xxx.github.io public

添加```.gitignore```文件，文件中写```public/```，在同步```my-blog```时会忽略```public```文件夹
接下来的工作可以借助一个```deploy.sh```脚本来完成```public/```文件夹(也就是xxx.github.io仓库)的托管
```sh
#!/bin/bash

echo -e "\033[0;32mDeploying updates to GitHub...\033[0m"

# Build the project.
hugo # if using a theme, replace with `hugo -t <YOURTHEME>`

# Go To Public folder
cd public

# Add changes to git.
git add .

# Commit changes.
msg="rebuilding site `date`"
if [ $# -eq 1 ]
  then msg="$1"
fi
git commit -m "$msg"

# Push source and build repos.
git push origin master

# Come Back up to the Project Root
cd ..
```

把以上命令保存为.sh文件，然后再运行以下命令
    # 运行以下命令更改 deploy.sh 的权限
    chmod +x deploy.sh
    # 运行以下命令完成 push 整个博客的操作
    ./deploy.sh "Your optional commit message"

然后把 ```blog``` 里的内容同步到 GitHub 上。同时要在data、layouts、static等空文件夹里面新建一个空的.gitkeep文件，以保持目录结构(因为空文件夹不会被托管)。记住一定要删掉你的主题目录里面的.git文件夹
    git remote add origin git@github.com:github_user_name/your_repository   // your_repository 即你存放你的博客源文件的仓库(也就是xxx.github.io)
    git pull origin master
    git add .
    git commit -m "the message you want to write"
    git push -u origin master    // 第二次 push 可以不用加 -u

到这里就大功告成了，如果你要在另一台电脑上继续写博客，只要把```blog```克隆下来，然后把里面“看得见”的东西拷贝到一个新的文件夹(即除了.git、.gitmodule等)，然后再重复刚才创建 git 子模块的那些步骤，当然你要准备Hugo环境。如果你想获取你的主题的最新更新，则在你的主题根目录下运行如下命令：
    git init
    git remote add origin https://github.com/author_name/your_theme_repo_name.git
    git pull    // or git pull origin master

### Reference
https://fengberlin.github.io/post/use-hugo-to-build-blog/