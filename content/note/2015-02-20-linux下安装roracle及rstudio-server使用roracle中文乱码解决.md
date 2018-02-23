---
title: Linux下安装ROracle及rstudio-server使用ROracle中文乱码解决
author: gXcloud
date: '2015-02-20'
slug: linux-rstudioserver-roracle
categories:
  - R
tags:
  - oracle
  - ROracle
  - linux
---

> `rstudio-server`安装在Linux服务器中，数据库一般也部署在服务器，所以在Linux服务器上使用R直连数据库操作更加方便，由于ROracle文档理解不到位，安装遇到一些问题在此补充，本文安装环境centos 6.5并测试通过

### 一、oracle-instantclient的安装

1. Oracle Instant Client下载（与数据库版本一致）

   下载地址：[官网](http://www.oracle.com/technetwork/topics/linuxsoft-082809.html)

   可以下载rpm包或者zip包进行安装,这里已rpm包（64位）为例

   下载rpm 包[csdn下载地址](http://download.csdn.net/detail/xwydq/8440629)

```
oracle-instantclient11.2-basic-11.2.0.3.0-1.x86_64.rpm
oracle-instantclient11.2-sqlplus-11.2.0.3.0-1.x86_64.rpm
oracle-instantclient11.2-devel-11.2.0.3.0-1.x86_64.rpm
```

1. 查看默认安装路径

```Shell
rpm -qpl oracle-instantclient11.2-basic-11.2.0.3.0-1.x86_64.rpm
rpm -qpl oracle-instantclient11.2-sqlplus-11.2.0.3.0-1.x86_64.rpm
rpm -qpl oracle-instantclient11.2-devel-11.2.0.3.0-1.x86_64.rpm
```

1.  安装

   使用如下命令进行安装：

```shell
rpm -ivh oracle-instantclient11.2-basic-11.2.0.3.0-1.x86_64.rpm
rpm -ivh oracle-instantclient11.2-sqlplus-11.2.0.3.0-1. x86_64.rpm
rpm -ivh oracle-instantclient11.2-devel-11.2.0.3.0-1. x86_64.rpm
```

安装完成后，ORACLE Instant Client 相关的头文件在 /usr/include/oracle/11.2/client64/下；库文件在/usr/lib/oracle/11.2/client64/下(cliient目录下有 lib 和 bin两个目录.)

1. 指定TNS文件目录

   一般情况将TNS文件（tnsnames.ora）保存到以下目录：

   目录不存在，建目录先：

```shell
mkdir -p /usr/lib/oracle/11.2/client64/network/admin
# 将已有的TNS文件（tnsnames.ora）复制到该目录下
```

1. 头文件的转移

   相关的头文件在/usr/include/oracle/11.2/client64/目录下

   ![img](http://img.blog.csdn.net/20150211162254038?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHd5ZHE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

   但是需要转移到目录（如果不复制过去，安装ROracle时就提示~/rdbms/public目录不存在，真的不存在啊，为啥去这个路径找？ROracle安装文档尽然没有提到，太坑了）：/usr/lib/oracle/11.2/client64/rdbms/public

   如下命令：

```shell
mkdir -p /usr/lib/oracle/11.2/client64/rdbms/public
cd /usr/lib/oracle/11.2/client64/rdbms/public
cp /usr/include/oracle/11.2/client64/* .
```

1. 设置环境变量

```shell
vi /etc/profile
# 加入：

export ORACLE_HOME=/usr/lib/oracle/11.2/client64
export TNS_ADMIN=$ORACLE_HOME/network/admin
export NLS_LANG="SimplifiedChinese_china".AL32UTF8
#export NLS_LANG='simplifiedchinese_china'.ZHS16GBK
export LD_LIBRARY_PATH=/usr/local/lib:/usr/lib/oracle/11.2/client64/lib
PATH=$JAVA_HOME/bin:$PATH:$HADOOP_HOME/bin:$ORACLE_HOME/bin:$R_HOME/bin
export PATH

chmod +x /etc/profile
source /etc/profile
```

**注**：设置环境变量NLS_LANG可以选择GBK，只要其他local、系统语言变量设置好，R在console下读取数据中文是没有乱码问题的，但是使用rstudio-server却始终不能正常显示中文，查来查去是rstudio-server的问题-对GBK支持有问题（不能确定），所以这里使用UTF-8作为变量可以解决中文显示的问题

1. sqlplus测试

   使用sqlplus测试是否可以成功连接数据库

![img](http://img.blog.csdn.net/20150211162359745?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHd5ZHE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

### 二、ROracle安装

以上安装正常的话就可以直接安装ROracle，建议root，其他用户也可以

```shell
R CMD INSTALLROracle_1.1-12.tar.gz
```

![img](http://img.blog.csdn.net/20150211162443267)

如此就表示安装成功

### 三、加载使用

 ![img](http://img.blog.csdn.net/20150211162510660)

### 四、rstudio-server加载出错及中文乱码解决

使用rstudio-server加载ROracle一直报错

```R
Error in dyn.load(file, DLLpath =DLLpath, ...) :
 unable to load shared object '/usr/lib64/R/library/ROracle/libs/ROracle.so':
 libclntsh.so.11.1: cannot open shared object file: No such file ordirectory
Error: package or namespace load failedfor ‘ROracle’
```

 ![img](http://img.blog.csdn.net/20150211162527090)

按官方文档说是没有环境变量LD_LIBRARY_PATH，但是即使指定了之后还是报错，想到rstudio-server每次都要一个新的R进程（理解可能不当），而启动R时可以可以设置一些默认的启动命令，这样就把一些环境变量的设定语句直接加到Rprofile.site（文件不存在可直接新建），命令：

```R
cd /usr/local/lib64/R/etc/
mkdir -p Rprofile.site
vi Rprofile.site

# 加入：
Sys.setenv(LD_LIBRARY_PATH="/usr/local/lib64/R/lib:/usr/local/lib64:/usr/java/jdk1.6.0_45/jre/lib/amd64/server:/usr/local/lib:/usr/lib/oracle/11.2/client64/lib")
Sys.setenv(ORACLE_HOME="/usr/lib/oracle/11.2/client64")
Sys.setenv(TNS_ADMIN="/usr/lib/oracle/11.2/client64/network/admin")
Sys.setenv(PATH="/usr/java/jdk1.6.0_45/bin:/usr/java/jdk1.6.0_45/bin:/usr/lib64/qt-3.3/bin:/usr/local/bin:/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/sbin:/home/hadoop/hadoop-1.2.1/bin:/usr/lib/oracle/11.2/client64/bin:/home/hadoop/bin:/home/hadoop/hadoop-1.2.1/bin:/usr/lib/oracle/11.2/client64/bin:/usr/local/lib64/R/bin")
Sys.setenv(NLS_LANG="Simplified Chinese_china.AL32UTF8")
```

具体的环境变量值可以通过R console查看

重启rstudio-server发现还是没有成功，但是其他环境变量已经有值，再设置：

```R
cd /etc/rstudio
vi rserver.conf

# 加入：
# Server Configuration File
rsession-ld-library-path=/usr/local/lib64/R/lib:/usr/local/lib64:/usr/java/jdk1.6.0_45/jre/lib/amd64/server:/usr/local/lib:/usr/lib/oracle/11.2/client64/lib
```

结果终于OK了！（中文乱码就是用UTF-8解决）

![img](http://img.blog.csdn.net/20150211161318818)

------

### 参考：

[在R用ROracle去操作Oracle資料庫](http://chingchuan-chen.github.io/posts/2016/07/25/use-ROracle-to-manipulate-oracle-database-in-R)