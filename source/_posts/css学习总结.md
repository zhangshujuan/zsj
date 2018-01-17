---
title: "css学习总结"
date: 2018-01-09 13:48:45
tags:
    - css
---
css学习总结
<!-- more -->

## 盒子宽度百分比减去固定宽度
``` css
    width: calc(100% - 20px);
```
减号 前后必须有空格

``` css
    .article img:nth-of-type(n+2):nth-last-of-type(n+2){
        Float:left;
    }
```
第一部分“nth-of-type(n+2)”是从第2个图片开始定位，选择了文章所有图片除了第一张。
第二部分“nth-last-of-type(n+2)”从文章中倒数第二张图片开始定位，选择了文章中所有图片，除了最后一张。
这样可以立刻派出第一张和最后一张图片达到想要的效果。



## div相对div垂直居中
```CSS
display: -webkit-box;    /*容器为旧伸缩盒*/
-webkit-box-align: center; /*设置旧伸缩盒对齐方式*/
display: flex;            /*容器为伸缩盒*/
align-items: center;  /*纵轴方向上的对齐方式设置为居中*/
```


## 有时候会要求水平垂直居中。再加一层
``` html
<div class="bar-list"><div class="vcenter"><i class="icon-mail"></i>男</div></div>
```
```css
.bar-list{
    flex: 1;
    line-height: 80/$ppr;
    font-size: 26/$ppr;
    color: #666666;
    text-align: center;
    .vcenter{
      display: inline-flex;
      align-items: center;
    }
}
```

还有另一种水平垂直居中

```css
div#wrap {    
display: table;    
border: 1px solid #FF0099;    
background-color: #FFCCFF;    
width: 760px;    
height: 400px;    
*position: relative;    
overflow: hidden;    
}    
  
div#subwrap {    
vertical-align: middle;    
display: table-cell;    
*position: absolute;    
*top: 50%;   
}    
  
div#content {    
*position: relative;    
*top: -50%;    
} 
```

## Css中控制当鼠标滑过<li>元素时，显示它里面的<ul>元素
```css
#navigation ul li:hover ul{
    Background-color:#88c366;
    Position:absolute;
    Width:100px;
    Display:block;
}
```
上面的写法IE6不支持，改用脚本完成
```js
    $(function(){
        $("#navigation ul li:has(ul)”).hover(function(){
        $(this).children(“ul”).stop(true,true).slideDown(400);
        },function(){
        $(this).children(“ul”).stop(true,true).slideUp(“fast”);
        });
    })
```
添加Stop(true,true)的好处是能把未执行完的动画队列清空，并且将正在执行的动画跳转到末状态


### Background-attachment:fixed;背景图片不随着内容一起滚动。

### 表格设置百分百但是有时候上下还是会错位只需要将table属于加上
```css
table-layout: fixed;
word-break: break-all;
word-wrap: break-word;
```