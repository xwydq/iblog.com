---
title: centos7 中rstudio-server画图中文显示问题
author: gXcloud
date: '2017-08-18'
slug: centos7-rstudio-server-chinese
categories:
  - R
tags:
  - 乱码
  - 画图
  - 配置
  - plot
---


关于centos 7中rstudio-server画图中文不显示问题整理

- 修改操作系统默认字符编码的支持

```shell
vi /etc/locale.conf

LANG="zh_CN.UTF-8"
SUPPORTED="zh_CN.GB18030:zh_CN:zh:zh_CN.utf-8:en_US.UTF-8:en_US:en"
SYSFONT="Microsoft YaHei"

# LANG变量，设置系统语言。
# SUPPORTED变量决定系统支持的语言，即系统能够显示的语言。
# SYSFONT变量表示系统字体。

reboot #需要重启后生效
```

- 查看已经安装的中文字体

  ```shell
  fc-list :lang=zh
  ```

  如果查询为空就需安装中文字体

  安装部分中文字体后的查询结果如下

  ```shell
  fc-list :lang=zh

  # 微软雅黑：msyh.ttc 对应字体名： 微软雅黑, Microsoft YaHei，两个命名都支持
  /usr/share/fonts/win/msyh.ttc: Microsoft YaHei UI:style=Regular,Normal,obyčejné,Standard,Κανονικά,No
  rmaali,Normál,Normale,Standaard,Normalny,Обычный,Normálne,Navadno,Arrunta
  /usr/share/fonts/win/simsun.ttc: 宋体,SimSun:style=常规,Regular
  /usr/share/fonts/win/msyh.ttc: 微软雅黑,Microsoft YaHei:style=Regular,Normal,obyčejné,Standard,Κανον
  ικά,Normaali,Normál,Normale,Standaard,Normalny,Обычный,Normálne,Navadno,Arrunta
  /usr/share/fonts/win/simsun.ttc: 新宋体,NSimSun:style=常规,Regular
  ```

- 无中文字体时安装

  ```shell
  yum install -y fontconfig mkfontscale
  #1) Windows下的ttf字体与GNOME是兼容的！因而可以将C:\Windows\Fonts下的字体打包。
  #2)在/usr/share/fonts目录下建立一个子目录，例如win，命令如下：
  mkdir /usr/share/fonts/win
  #3) 将上传的字体解压至/usr/share/fonts/win目录
  #4) 建立字体索引信息，更新字体缓存：
  cd /usr/share/fonts/win
  mkfontscale
  mkfontdir
  fc-cache
  #至此，字体已经安装完毕！
  #5) 重启系统，实测只需要重启对应的应用即可
  reboot
  ```

  [参考]: https://yq.aliyun.com/articles/319340	"centos7.0 安装字体库"

  同样centos7.3微软云默认已安装仿宋中文字体，而阿里云无任何中文字体

- R中画图中文字体指定

  ```r
  par(family='Microsoft YaHei') #par(family='微软雅黑')中英文名称都支持
  plot(rnorm(10), main = '中文')
  par(family='NSimSun')
  plot(rnorm(10), main = '中文')
  ```

  <img src="https://ws4.sinaimg.cn/large/006tNc79gy1fnif2xxq9gj30hy0cujrk.jpg" style="zoom:60%" /><img src="https://ws1.sinaimg.cn/large/006tNc79gy1fnif2xhaojj30hy0cu0sv.jpg" style="zoom:60%" />

  ```r
  #ggplot2 上述在par中指定family对ggplot2不起作用，需要按以下重新指定（同上）
  library(ggplot2)
  p <- ggplot(mpg, aes(class, hwy))
  p + geom_boxplot() + ggtitle("微软雅黑") +
    theme(text=element_text(size=16,  family="微软雅黑"))
  ```

  ![Rplot02](https://ws1.sinaimg.cn/large/006tNc79gy1fnifffhmsmj30hy0cu0te.jpg)

  注：安装多个中文字体后，即是在`/etc/locale.conf`设置`SYSFONT="Microsoft YaHei"`，R画图时中文默认字体也一定是`Microsoft YaHei`

- 使用指定PDF的画图设备包CairoPDF解决中文乱码问题

  ```R
  # sudo yum install libXt-devel
  library(Cairo)
  CairoPDF("test.pdf")
  par(family = "simsun")
  plot(3, 3, type = "n", main = "五角星")
  points(3, 3, pch = "☆", cex = 2, col = "blue")
  dev.off()
  ```

  显示效果与具体AI有关

