---
title: linux环境下selenium操作Chrome
author: gXcloud
date: '2018-02-27'
slug: linux-selenium-chromeheadless-install-test
categories:
  - 爬虫
tags:
  - python
  - selenium
  - PhantomJS
  - chrome
  - cookie
---

---
typora-copy-images-to: ipic

---



> centos7及以上Linux环境。使用PhantomJS设置cookie报错[Can only set Cookies for the current domain](https://github.com/ariya/phantomjs/issues/14228)，看样子后期相关维护也很慢。google chrome已经开始支持headless，可以考虑换大腿了

![6af89bc8gw1f8np3c8jdtj207i07674j](https://ws1.sinaimg.cn/large/006tNc79gy1fouu37p1zvj307i076q34.jpg)

### 环境配置安装

[可以直接使用配置yum源的方法来安装](https://tecadmin.net/install-google-chrome-in-centos-rhel-and-fedora/)，但是由于伟大的墙可能访问不到Google服务，也可以先下载指定版本的安装文件再安装

- chromedriver安装

  ```shell
  $ wget https://chromedriver.storage.googleapis.com/2.35/chromedriver_linux64.zip
  $ unzip chromedriver_linux64.zip
  $ chromedriver --version 
  #ChromeDriver 2.35.528139

  # 将chromedriver执行文件所在的路径添加到PATH环境变量
  ```

- chromebrowser安装

  ```shell
  $ wget https://dl.google.com/linux/direct/google-chrome-stable_current_x86_64.rpm
  $ sudo yum install google-chrome-stable_current_x86_64.rpm
  $ google-chrome --version 
  #Google Chrome 61.0.3163.100
  ```

- Xvfb安装

  Xvfb（virtual framebuffer X server for X Version 11）， Xvfb 可以直接处理 Window 的图形化功能，且不把图像輸出到屏幕，目的就是在没有 Xwindow的情况下仍然可以执行相关图像界面操作。实现一种虚拟窗口（window）的操作。[安裝 XVFB 做 Selenium 測試](https://www.puritys.me/docs-blog/article-262-%E5%AE%89%E8%A3%9D-XVFB-%E5%81%9A-Selenium-%E6%B8%AC%E8%A9%A6.html)

  ```shell
  $ sudo yum install Xvfb
  $ sudo yum install libXfont
  $ sudo yum install xorg-x11-fonts*

  # 启动测试Xvfb
  $ Xvfb :2 -screen 0 1024x768x16
  $ ps -ef | grep Xvfb
  #uname     32701 30446  0 10:33 pts/2    00:00:00 Xvfb :2 -screen 0 1024x768x16
  #uname     32045 32016  0 10:01 pts/1    00:00:00 Xvfb -br -nolisten tcp -screen 0 800x600x24 :1001
  ```

- python模块

  pyvirtualdisplay启动管理虚拟窗口

  ```python
  # as root
  # pip install pyvirtualdisplay

  from pyvirtualdisplay import Display
  xephyr=Display(visible=1, size=(320, 240)).start()
  ```

  

  

### 效果测试

- 有窗口模式示例

  ```python
  from selenium import webdriver  
  from pyvirtualdisplay import Display  

  display = Display(visible=0, size=(800,600))  
  display.start()  
  driver = webdriver.Chrome()  
  driver.get("http://www.baidu.com")  
  driver.page_source 
  driver.quit()  
  display.stop() 
  ```


- headless无窗口模式示例

  ```python
  from selenium import webdriver  

  url1 = 'http://q.10jqka.com.cn/xsb/'
  url = 'http://q.10jqka.com.cn/xsb/index/field/zdf/order/desc/page/7/ajax/1/'

  options = webdriver.ChromeOptions()
  #options.add_argument('--headless')效果下同
  options.add_argument('headless')
  driver = webdriver.Chrome(
      chrome_options=options,
      service_args=['--ignore-ssl-errors=true']) 

  ## 先访问获取cookie
  driver.get(url1)  
  driver.page_source 
  driver.get_cookies()

  ## 带上面的cookie访问获取数据
  driver.get(url)  
  driver.page_source 

  driver.quit() 
  ```

  


------

### 参考

[使用 headless chrome进行测试](https://segmentfault.com/a/1190000009399609)

[Driving Headless Chrome with Python](https://duo.com/blog/driving-headless-chrome-with-python)

