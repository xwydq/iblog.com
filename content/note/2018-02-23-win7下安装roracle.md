---
title: win7下安装ROracle
author: gXcloud
date: '2015-02-11'
slug: win7-roracle
categories:
  - R
tags:
  - oracle
  - ROracle
---

win7下安装R的ROracle来连接Oracle数据库速度比ODBC的方式快很多

安装配置也比较简单，但是有个问题：

同时使用PL/SQL(32bits)与R(64bits，当然完全可以使用32位的，但是32的有保存使用限制~~~~~)。

本配置的目的就是同时兼顾以上两者：

### 一、ROracle、Oracle instantclient下载

- （1）ROracle下载

[ROracle下载地址](http://download.csdn.net/detail/xwydq/8440643)

- （2）Oracle instantclient下载

将文件夹32/64（文件夹中分别包含将32位、64位的Oracle instant client（basic、jdbc、odbc、sdk、sqlplus）解压后的文件，都解压到instantclient_11_2文件夹下，且将TNS数据源配置直接复制到该文件下）COPY到D盘
如果使用版本instantclient_11_2可以直接[32bits](http://download.csdn.net/detail/xwydq/8440861)、[64bits](http://download.csdn.net/detail/xwydq/8440909)

其他版本可以官网下载
解压后见下图
![img](http://img.blog.csdn.net/20150211174614234?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHd5ZHE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

### 二、环境变量的配置

在此例中需要设置如下环境变量：

```Shell
Path
D:\32\instantclient_11_2;D:\64\instantclient_11_2;
ORACLE_HOME
D:\32\instantclient_11_2
TNS_ADMIN
D:\32\instantclient_11_2
NLS_LANG
SIMPLIFIED CHINESE_CHINA.ZHS16GBK
OCI_LIB64
D:\64\instantclient_11_2
OCI_LIB32
D:\32\instantclient_11_2
```

