---
title: 命令获取Office文档内容
author: gXcloud
date: '2018-04-19'
slug: get-office-doc-content
categories:
  - 工具
tags:
  - 文本
---

>  批量获取Office文档的文本内容用于文本解析或者文本分析

### docx类型文档读取

#### 使用pandoc命令进行文件类型转换

```
pandoc test-getdocxcontent.docx -o testdocx.txt
```

![image-20180430073537021](https://ws2.sinaimg.cn/large/006tKfTcgy1fquc55f37ij30fr08mmyf.jpg)

#### 使用python的模块docx2txt

- 安装 docx2txt

```
pip install docx2txt
```

- 获取文档内容

```
docx2txt ./test/test-getdocxcontent.docx
# extract text and images
# docx2txt -i /tmp/img_dir test-getdocxcontent.docx
```

原始文档

![](https://images-cdn.shimo.im/hdxZ3UlV3dAvg1CG/image.png)

解析结果

```
让工作  更精彩
推荐编号：207339
推荐公司：浙jds投资管理有限公司
推荐职位：数据挖掘工程师
推荐顾问：
推荐时间：2017-11-06
。。。。。。。
```



### doc类型文档读取

目前尝试成功的方法：使用开源的Office：libreoffice通过命令行来进行格式转换或者获取文本内容

```shell
## libreoffice下载地址
https://downloadarchive.documentfoundation.org/libreoffice/old/4.4.2.2/rpm/x86_64/
## 安装方法
http://www.techoism.com/install-libreoffice-4-4-on-centosrhel/
## 格式转化命令:将doc转为docx
libreoffice4.4 --headless --convert-to docx --outdir /u/user/doc_folder /u/user/doc_folder/old_office/file.doc
## 格式转化命令:将doc转为txt
libreoffice4.4 --headless --convert-to txt --outdir /u/user/doc_folder /u/user/doc_folder/old_office/file.doc
```

![image-20180430074337853](https://ws2.sinaimg.cn/large/006tKfTcgy1fquccs2z5uj30ni08n0u2.jpg)