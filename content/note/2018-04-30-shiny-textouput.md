---
title: shiny-textOuput
author: gXcloud
date: '2018-04-30'
slug: shiny-textouput
categories:
  - shiny
tags:
  - error
  - html
  - R
  - test
---


> 在`shiny`中动态显示一个字符串变量最好使用`renderText/textOutput`的方法，使用`renderText/reactiveValues`会出现异常或不成功的情况。

比较示例代码：

```r
library(shiny)

# Define UI for application that draws a histogram
ui <- fluidPage(
  
  # Application title
  titlePanel("Old Faithful Geyser Data"),
  
  # Sidebar with a slider input for number of bins 
  sidebarLayout(
    sidebarPanel(
      sliderInput("bins",
                  "Number of bins:",
                  min = 1,
                  max = 50,
                  value = 30)
    ),
    
    # test textOutput
    mainPanel(
      
      div(h3("固定值")),
      div(h4("text1: hello world!!")),
      
      hr(),
      div(h3("reactiveValues引入的变量值(不能显示)")),
      div(h4(renderText(vt$numName))),
      
      hr(),
      div(h3("textOutput动态输出")),
      div(h4(textOutput("text1"))),
      
      hr(),
      div(h3("input$bins 报错")),
      div(h4(renderText(input$bins)))
    )
  )
)

# Define server logic required to draw a histogram
server <- function(session, input, output) {
  vt = reactiveValues(numName = 'heloo')
  
  observe({
    vt$numName = as.character(input$bins)
    print(vt$numName)
  })
  
  output$text1 = renderText({
    vt$numName
  })
}

# Run the application 
shinyApp(ui = ui, server = server)
```



执行结果：

![image-20180501080353530](https://ws4.sinaimg.cn/large/006tKfTcgy1fqvikbxy5oj30q80c6gnw.jpg)
