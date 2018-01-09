---
title: 动态添加的html元素怎么让它的div点击事件生效
date: 
tags:
    - js
---
## 动态添加的html元素怎么让它的div点击事件生效
### jquery 1.9以下可以用live去绑定事件。
live() 方法附加的事件处理程序适用于匹配选择器的当前及未来的元素（比如由脚本创建的新元素）。

``` bash
$("button").live("click",function(){
  $("p").slideToggle();
});
```


### jquery 1.9以上的版本删除了live方法。建议用以下方式实现

``` bash
$(document).on('click', '.list li', function() {
    alert('你点我了');
});
```