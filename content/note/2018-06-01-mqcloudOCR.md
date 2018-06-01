---
title: 腾讯云OCR文本解析获取
author: gXcloud
date: '2018-06-01'
slug: mqcloudOCR
categories:
  - 工具
tags:
  - 文本
  - R
  - python
---




### 账号配置

- [API密钥管理](https://console.cloud.tencent.com/cam/capi)申请对应的ID/Key

  ![WX20180601-170901@2x](https://ws2.sinaimg.cn/large/006tNc79gy1frvtve37paj31kw0jnn2o.jpg)

- [鉴权签名](https://cloud.tencent.com/document/product/641/12409)

  ```php
  <?php
  	$appid = "1256847800";
  	$bucket = "tencentyun";
  	$secret_id = "AKIDlQxV4XsCNRN9xgmfuxqqhNrNjutgZjy2";
  	$secret_key = "61oSxv0ueCdsmlgPyMYaWQDtQo162oOq";
  	$expired = time() + 5592000;
  	$onceExpired = 0;
  	$current = time();
  	$rdm = rand();
  	$userid = "0";
  	$fileid = "tencentyunSignTest";

  	$srcStr = 'a='.$appid.'&k='.$secret_id.'&e='.$expired.'&t='.$current.'&r='.$rdm.'&u='
  	.$userid.'&f=';

  	$signStr = base64_encode(hash_hmac('SHA1', $srcStr, $secret_key, true).$srcStr);

  	echo $signStr."\n"; 
  ?>
  ```

  以上文件保存为php，并在命令行执行，打印的结果就为生成的签名

  ```shell
  php test.php
  #2jMeRhhMXLKcmE4lT2mleNOFQ5lhPTEyNTY4NEbFF4VjRYc0NOUk45eGdtZnV4cXFoTnJOanV0Z1pqeTImZT0xNTMzNDM0ODAwJnQ9MTUyNzg0MjgwMCZyPTE5NTkwMzQ0NiZ1PTAmZj0=
  ```

  

### 请求测试、数据合并

- postman测试

  ![1527844842648](https://ws4.sinaimg.cn/large/006tNc79gy1frvtvceqp0j31dc0c4gnq.jpg)![1527844877319](https://ws3.sinaimg.cn/large/006tNc79gy1frvtvf4ynaj31740c4q4v.jpg)

- R脚本获取文本数据

  ```r
  library(rvest)
  library(jsonlite)
  library(dplyr)
  library(readr)
  library(httr)

  h = c(
  '2jMeRhhMXLKcmE4lT2mleNOFQ5lhPTEyNTY4NDc4MDAmaz1BS0lEbFF4VjRYc0NOUk45eGdtZnV4cXFoTnJOanV0Z1pqeTImZT0xNTMzNDM0ODAwJnQ9MTUyNzg0MjgwMCZyPTE5NTkwMzQ0NiZ1PTAmZj0=',
    'recognition.image.myqcloud.com',
    'application/json'
  )
  names(h) <- c("Authorization", "Host", "Content-Type")

  url = 'http://recognition.image.myqcloud.com/ocr/general'
  u1 = POST(
    url,
    add_headers(.headers = h),
    body = list(appid = "1256847800",
                url = "http://pbwci.qun.hk/FuIhbSy6UrWnMsWnvooggCO-GZkY"),
    encode = 'json'
  )
  rvt = read_html(u1)
  rvt = jsonlite::fromJSON(html_text(rvt))
  # print(rvt)
  ## 解析后文本
  paste0(rvt$data$items$itemstring, collapse = '')

  ```

  结果比较

  ![](http://pbwci.qun.hk/FuIhbSy6UrWnMsWnvooggCO-GZkY)

  ```r
  [1] "珠宝首饰证书(鉴定结果]寿山石No: M180501026饰品名称招件总质量(g)10413. 0000色褐黄色偏光性非均质集合体遵明度徽透明折光率1.57 (点刻)吸收光谱未见放大检查县微丝网结构紫外荧光长波无短波无其它测试红外光讲检查，寿山石特征吸收M180501026备注执行标准，GB/T16552-2017. GB/T16553-2017江苏省消费者协会黄金珠宝检测鉴定定点单位检验师，陈飞股文华438646红苏省质量技术监督局珠宝首饰产品打假举报工作站询网址: www.jszbz.com 本证 书仅对所示样品负责， 复制无效。"
  ```

- python post

  ```python
  import json
  import requests

  headers = {'Authorization': '2jMeRhhMXLKcmE4lT2mleNOFQ5lhPTEyNTY4NDc4MDAmaz1BS0lEbFF4VjRYc0NOUk45eGdtZnV4cXFoTnJOanV0Z1pqeTImZT0xNTMzNDM0ODAwJnQ9MTUyNzg0MjgwMCZyPTE5NTkwMzQ0NiZ1PTAmZj0=',
  'Host': 'recognition.image.myqcloud.com',
  'Content-Type': 'application/json'}

  url = 'http://recognition.image.myqcloud.com/ocr/general'

  s = json.dumps(
  	{
  	  "appid":"1256847800",
  	  "url":"http://pbwci.qun.hk/FjhMnaiO4PxZ577-S10WGq5kZ7hc"
  	}
  )

  r = requests.post(url, headers=headers, data=s)
  print(r.text)
  ```



