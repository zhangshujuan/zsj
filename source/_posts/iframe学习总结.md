---
title: iframe学习总结
date: 2018-01-16 15:26:51
tags:
---
iframe学习总结
<!-- more -->
### 在iframe里面找iframe外面的div
``` js
$(window.parent.document).find('body')
```

### 在iframe外面找iframeli面的div
``` js
$(window.frames["index_iframe"].document).find(".hright")
```

### Iframe在子页面怎么获取父页面的div
在子页面查找父页面中的window div
``` js
$("#window",parent.document);
```
$("#window",parent.document);



### Div里面嵌套iframe框架链接到另一个子页面。
在deviceList.jsp页面中改变窗口大小里面的表格div的高度随着改变功能。
第一就是窗口改变大小的事件
1.下面窗口改变的方法写在frame 子页面中不起作用
``` js
$(window).resize(function() {
    /************************图标加载***************************/
    var toph = $(".tablescroll").position().top;
    $(".tablescroll").css("height",fullh -toph -40-45-20 -60);	
});

```
解决方法：就是在devicelist.jsp页面body那里加上onresize方法
2.在子页面中获取窗口的高度不正确
``` js
$(window).height();//屏幕的高度	
```
只有把获取高度的方法写在父页面中，在子页面中通过parent.window.fullh;方法获取fullh的值
