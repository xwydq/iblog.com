---
title: shiny-server 安装与配置、问题汇总
author: gXcloud
date: '2014-04-01'
slug: centos6-install-shiny-server
categories:
  - shiny
tags:
  - 配置
---


## 1 安装
环境：centos6.5

 - 1.1 安装R

```
$ sudo yum install R
```

 - 1.2 进入R，安装包shiny,rmarkdown

```
install.packages('shiny')
install.packages('rmarkdown')
```

 - 1.3 安装shiny-server

```
$ wget https://download3.rstudio.org/centos5.9/x86_64/shiny-server-1.4.2.786-rh5-x86_64.rpm
$ sudo yum install --nogpgcheck shiny-server-1.4.2.786-rh5-x86_64.rpm
```

查看是否安装成功：进入ip:3838

##2 启动

```
start shiny-server
stop shiny-server
status shiny-server
reload shiny-server
```

##3 配置

 - 3.1 默认配置

```
cat /etc/shiny-server/shiny-server.conf
```

首页：**/srv/shiny-server/index.html**
shiny app 的目录：**/srv/shiny-server/sample-apps**
详细配置参数见：[shiny-server conf settings](http://docs.rstudio.com/shiny-server/#configuration-settings)

##4 注意问题

 - 4.1 The application took too long to respond.

修改**/etc/shiny-server/shiny-server.conf**中的参数增加延时

```
$ vi /etc/shiny-server/shiny-server.conf
app init timeout 300;
app idle timeout 300;
```

 - 4.2 linux文件建立连接（软连接、硬链接）

建立文件夹软连接

```
ln -s /home/xuwy/flow_model /srv/shiny-server/app_flow
```

删除 

```
rm -rf /home/xuwy/flow_model
```

注意不可按照以下方式删除(会直接删除对应原文件的子目录下所有文件)

```
rm -rf /home/xuwy/flow_model/
```

 - 4.3 HTML的图片插入相对路径

与index.html相同路径的直接使用

```
<img src="mac_heatmap_gg.gif" alt="mac View" style="width:1104px;height:528px;">
```

子目录下的使用方法

```
<img src="/images/mac_heatmap_gg.gif" alt="mac View" style="width:1104px;height:528px;">
```

 - 4.4 shiny project中有load(./xx.RData)

xx.RData会间隔时间变化，但是**shiny-server不会实时更新**，只能采取间隔时间重启shiny-server服务

```
restart shiny-server
```


----------
备注
将shiny project 或者 rmarkdown嵌入到html

```
  <div id="shiny">
	<iframe src="./sample-apps/hello/" style="border: 1px solid #AAA; width: 290px; height: 460px"></iframe>
	<div class="caption">
	  When Shiny is properly configured on your server, you'll see a Shiny app above.
	</div>

	<iframe src="./sample-apps/rmd/" style="border: 1px solid #AAA; width: 290px; height: 420px"></iframe>
	<div class="caption">
	  With Shiny and <code>rmarkdown</code> installed, you should see a Shiny doc above.
	</div>
  </div>
```