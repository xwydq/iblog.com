---
title: R使用代理并行下载
author: gXcloud
date: '2018-05-10'
slug: r-proxy-parallel-download
categories:
  - 爬虫
tags:
  - 代理
  - http
  - httr
  - R
---

> 在R中结合`parallel`包来实现并行下载或者请求多个URL地址以提高速度

```R
urls = c(
"http://www.cninfo.com.cn/finalpage/2017-12-14/1204220648.PDF",
"http://www.cninfo.com.cn/finalpage/2017-12-14/1204220649.PDF",
"http://www.cninfo.com.cn/finalpage/2017-12-14/1204220650.PDF",
"http://www.cninfo.com.cn/finalpage/2017-12-14/1204220651.PDF",
"http://www.cninfo.com.cn/finalpage/2017-12-14/1204220653.PDF",
"http://www.cninfo.com.cn/finalpage/2017-12-14/1204220654.PDF",
"http://www.cninfo.com.cn/finalpage/2017-12-14/1204220726.PDF",
"http://www.cninfo.com.cn/finalpage/2017-12-14/1204220878.PDF",
"http://www.cninfo.com.cn/finalpage/2017-12-14/1204220882.PDF",
"http://www.cninfo.com.cn/finalpage/2017-12-14/1204220939.PDF",
"http://www.cninfo.com.cn/finalpage/2017-12-14/1204220942.PDF",
"http://www.cninfo.com.cn/finalpage/2017-12-14/1204220945.PDF",
"http://www.cninfo.com.cn/finalpage/2017-12-14/1204220947.PDF",
"http://www.cninfo.com.cn/finalpage/2017-12-14/1204221018.PDF"
)

library(parallel)
library(httr)
library(readr)

# wget proxy
str1 = 'wget -e "http_proxy=%s"  %s -O ~/test/annouce/%s.pdf --timeout=10 --tries=2' 

str1 = 'wget %s -O ~/test/annouce/%s.pdf --timeout=10 --tries=2 --quiet'

lturls = as.list(urls)

t1 = Sys.time()
mclapply(lturls, FUN = function(x){
  print(x)
  ## update ips
  # if(runif(1) > 0.6){
  #   ips <<- updateIps(ips)
  # }
  fnm = digest(x)
  ## wget proxy
  # cmd_str = sprintf(str1, sample(ips, 1), x, fnm)
  cmd_str = sprintf(str1, x, fnm)
  system(cmd_str)
  
  ## rm file
  # system(sprintf('rm ~/test/annouce/%s.pdf', fnm))
  
}, mc.cores = 10
)

t2 = Sys.time()
print(difftime(t2, t1))
```

