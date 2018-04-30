---
title: Shiny display pdf file
author: gXcloud
date: '2018-04-30'
slug: shiny-display-pdf-file
categories:
  - shiny
tags:
  - html
  - github
  - JS
---


> 使用pdf.js来使用在web页面中阅读PDF文件，同时禁止下载、打水印的功能

### PDF.JS

参考 [PDF.js 在线pdf阅读插件（禁止打印，下载，每页水印）](https://blog.csdn.net/For_GG/article/details/78616063)修改对应代码（JS/HTML）

### shiny web 

- 将修改测试无误的pdf.js保存在shiny项目的www路径下

```shell
└── www
    └── pdfView
        └── generic
            ├── build
            └── web
                ├── cmaps
                ├── images
                └── locale
```



- Shiny app代码

```r
library(shiny)

# Define UI for application that draws a histogram
ui <- fluidPage(
   
   # Application title
   titlePanel("pdf view"),
   
   # Sidebar with a slider input for number of bins 
   uiOutput("iframe_source")
)

server <- function(input, output) {
  output$iframe_source <- renderUI({
  iframe_source = tags$iframe(
    src=sprintf("pdfView/generic/web/viewer.html?file=../../0b781915c086876a5eac6b1b30ba0d18.pdf"),
                              height=800, width='90%')
  iframe_source
  })
}

# Run the application 
shinyApp(ui = ui, server = server)
```

- 具体效果见

![image-20180430085439710](https://ws4.sinaimg.cn/large/006tKfTcgy1fqueep3orfj30oe0ic456.jpg)



------

[项目git仓库地址](https://github.com/xwydq/shiny-pdfview.git)


