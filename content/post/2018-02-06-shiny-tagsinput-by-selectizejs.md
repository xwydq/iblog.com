---
title: shiny tagsInput by selectizejs
author: 徐伟云
date: '2018-02-06'
slug: shiny-tagsinput-by-selectizejs
categories:
  - shiny
tags:
  - 标签功能
  - selectize.js
---


> [selectize.js](http://selectize.github.io/selectize.js/)是基于jQuery开发的混合了文本输入框与下拉选择框功能的JS库，对应的[GITHUB地址](https://github.com/selectize/selectize.js)

最新版本的shiny已经整合了selectize.js 到 selectInput

```R
> sessionInfo()
R version 3.4.3 (2017-11-30)
...
other attached packages:
[1] shiny_1.0.5
```

### 打标签

   使用 selectInput/selectizeInput 实现打标签功能

   ```r
     selectizeInput(inputId='ids1', 
                    label= tags$h5('标签效果'), 
                    choices = NULL, 
                    multiple = TRUE, 
                    options = list( 
                      placeholder = 'Please select/type something', 
                      maxItems = 3, # 最大数量限制
                      onInitialize = I('function() {
                                       this.setValue("hello world"); }'), # 未发现具体作用
                      # class="demo",
                      plugins = list('restore_on_backspace', # backspace 按钮可以起到编辑效果
                                     'remove_button', # 显示移除按钮
                                     'drag_drop'), # 可以拖动编辑顺序
                      create =  TRUE, 
                      delimiter = ',', 
                      persist = FALSE,
                      onDelete = I("function(values) {
                                   return confirm(values.length > 1 ? 'Are you sure you want to remove these ' + values.length + ' items?' : 'Are you sure you want to remove \"' + values[0] + '\"?');
                                   }") # onDelete 触发删除确认的作用
                        ))
   ```

   默认样式显示的标签效果较差，可以使用自定义css样式的方式修改

   - 默认样式

     <img src="https://ws2.sinaimg.cn/large/006tNc79gy1fo6rhjd5amj30ki07g74n.jpg" style="zoom:50%" />

   - 修改后效果，使用css显示标签的渐变色

     <img src="https://ws3.sinaimg.cn/large/006tNc79gy1fo6rhiwqagj30t8090t9l.jpg" style="zoom:50%" />

   - 修改过程

     定位原标签的的具体位置确定起作用的css样式，可以下载 [GITHUB](https://github.com/selectize/selectize.js)的示例在Chrome中查看

     ![1517884574736](https://ws1.sinaimg.cn/large/006tNc79gy1fo6rhgkf6pj31kw0l0qj3.jpg)

     自定义的CSS样式

     ```css
     .selectize-control.multi .selectize-input [data-value] {
     	background-image: linear-gradient(to bottom, #ddd7ee, #1e9);
     }
     ```

### 下拉选择添加邮箱地址的功能

   实现邮箱正则匹配，设置展示规则等。直接将原示例转移到shiny，需要注意：

   - `I()`函数及R的`AsIs`类型的使用
   - JS代码作为R字符串时需转义`'; "; \` to `\'; \"; \\`

   ```r
   selectizeInput(inputId='ids2', 
                  label= tags$h5(em('选择添加邮箱（正则限制）')), 
                  width = "80%",
                  choices = NULL, 
                  multiple = TRUE, 
                  options = list( 
                    persist = FALSE,
                    maxItems = NULL,
                    valueField = 'email',
                    labelField = 'name',
                    searchField = c('name', 'email'), # 搜索字段
                    # 可选项
                    options = I("[
                                {email: 'brian@thirdroute.com', name: 'Brian Reavis'},
                                {email: 'nikola@tesla.com', name: 'Nikola Tesla'},
                                {email: 'someone@gmail.com'}
                                ]"),
                    # 选择后的显示结果
                    render = I("{
                               item: function(item, escape) {
                               return '<div>' +
                               (item.name ? '<span class=\"name\">' + escape(item.name) + '</span>' : '') +
                               (item.email ? '<span class=\"email\">' + escape(item.email) + '</span>' : '') +
                               '</div>';
                               },
                               option: function(item, escape) {
                               var label = item.name || item.email;
                               var caption = item.name ? item.email : null;
                               return '<div>' +
                               '<span class=\"label\">' + escape(label) + '</span>' +
                               (caption ? '<span class=\"caption\">' + escape(caption) + '</span>' : '') +
                               '</div>';
                               }
                               }"),
                          # 生成邮件规则的筛选，正则出现 \ 要替换为 \\
                    createFilter = I("function(input) {
                                     var match, regex;
                                     var REGEX_EMAIL = '([a-z0-9!#$%&\\'*+/=?^_`{|}~-]+(?:\\.[a-z0-9!#$%&\\'*+/=?^_`{|}~-]+)*@(?:[a-z0-9](?:[a-z0-9-]*[a-z0-9])?\\.)+[a-z0-9](?:[a-z0-9-]*[a-z0-9])?)';
                                     
                                     
                                     // email@address.com
                                     regex = new RegExp('^' + REGEX_EMAIL + '$', 'i');
                                     match = input.match(regex);
                                     if (match) return !this.options.hasOwnProperty(match[0]);
                                     
                                     // name <email@address.com>
                                     regex = new RegExp('^([^<]*)\\<' + REGEX_EMAIL + '\\>$', 'i');
                                     match = input.match(regex);
                                     if (match) return !this.options.hasOwnProperty(match[2]);
                                     
                                     return false;
                                     }"),
                          # 创建数据结果
                    create = I("function(input) {
                               var REGEX_EMAIL = '([a-z0-9!#$%&\\'*+/=?^_`{|}~-]+(?:\\.[a-z0-9!#$%&\\'*+/=?^_`{|}~-]+)*@(?:[a-z0-9](?:[a-z0-9-]*[a-z0-9])?\\.)+[a-z0-9](?:[a-z0-9-]*[a-z0-9])?)';
                               
                               if ((new RegExp('^' + REGEX_EMAIL + '$', 'i')).test(input)) {
                               return {email: input};
                               }
                               var match = input.match(new RegExp('^([^<]*)\\<' + REGEX_EMAIL + '\\>$', 'i'));
                               if (match) {
                               return {
                               email : match[2],
                               name  : $.trim(match[1])
                               };
                               }
                               alert('Invalid email address.');
                               return false;
                               }"),
                          delimiter = ','
                        ))
   ```

   具体效果

   <img src="https://ws1.sinaimg.cn/large/006tNc79gy1fo6rhhr8nrj315y09s40p.jpg" style="zoom:50%" />

### 具体实例见[shinyapps.io](https://xwydq.shinyapps.io/selectize/)


   [shinyapps.io的部署](http://docs.rstudio.com/shinyapps.io/)



