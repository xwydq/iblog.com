---
title: R中data.frame与matrix循环效率比较
author: gXcloud
date: '2013-09-22'
slug: r-loop-dataframe-vs-matrix
categories:
  - R
tags:
  - 性能
  - data.frame
  - matrix
  - 循环
---

一直以来，在R中使用data.frame处理数据跟方便，大多数统计函数直接支持data.frame类型的运算，虽说在R中要尽可能避免使用循环，but由于能力与临时需要，循环还是不经意间就出现了。今天对一个例子测试下，R循环中使用matrix的速度明显大于使用data.frame。有如下简单循环代码：

```r
test <- matrix(0, nrow=1000, ncol=50)
system.time(for(i in 1:nrow(test)){
  for(j in 1:ncol(test)){
    test[i,j] <- rnorm(1)
  }
})
# 用户  系统  流逝 
# 0.310 0.015 0.324 

test <- matrix(0, nrow=1000, ncol=50)
test <- as.data.frame(test)
system.time(for(i in 1:nrow(test)){
  for(j in 1:ncol(test)){
    test[i,j] <- rnorm(1)
  }
})
# 用户  系统  流逝 
# 1.868 0.002 1.868
```

结果差距明显（5倍多）！！