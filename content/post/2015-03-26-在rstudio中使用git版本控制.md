---
title: 在rstudio中使用git版本控制
author: gXcloud
date: '2015-03-26'
slug: rstudio-git-version-control
categories:
  - R
tags:
  - git
  - github
  - RStudio
---

### git安装

- 方法一：源码安装

```shell
# //先安装git依赖的包 
yum install zlib-devel 
yum install openssl-devel 
yum install perl 
yum install cpio 
yum install expat-devel 
yum install gettext-devel 
//安装autoconf 
yum install autoconf 
# //安装git 
wget http://www.codemonkey.org.uk/projects/git-snapshots/git/git-latest.tar.gz 
tar xzvf git-latest.tar.gz 
cd git-{date} 
autoconf 
./configure --with-curl=/usr/local 
make 
make install
```

- 方法二：yum安装（epel源）

```shell
yum install git  
测试安装成功：
git --version
```

- 生成github的测试项目

```shell
mkdir ~/githubtest
touch README.md
echo intime-data-analyse >> README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin https://github.com/username/rstudio-intime.git
git push -u origin master
```



### 生成密钥

```
#生成rsa密钥对
ssh-keygen -t rsa
#查看公钥
cat /home/gXcloud/.ssh/id_rsa.pub
```



### 将公钥上传到github

![img](http://img.blog.csdn.net/20150326172454731)

### 使用rstudio-server新建项目

![img](http://img.blog.csdn.net/20150326172853562?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHd5ZHE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

### 添加comment

![img](http://img.blog.csdn.net/20150326173302637)

### 同步（push-上传; pull-下载）文件

![img](http://img.blog.csdn.net/20150326173212478)



------

**参考：**

[多人在线开发R](http://blog.fens.me/r-rstudio-server/)

[知乎git学习建议](http://www.zhihu.com/question/20070065)

[git pro book 教程](http://git-scm.com/book/zh)

[rstudio官网说明](https://support.rstudio.com/hc/en-us/articles/200532077-Version-Control-with-Git-and-SVN)
