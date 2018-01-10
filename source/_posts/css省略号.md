---
title: 省略号
date: 
tags:
    - css
---
省略号
<!-- more -->
### 单行省略
```css
.ellipsis {
 word-wrap: normal; /* for IE */
 text-overflow: ellipsis;
 white-space: nowrap;
 overflow: hidden;
}
```

### 多行省略
```css
.text-ellipsis {
  word-break:break-all;
  display:-webkit-box;
  -webkit-line-clamp:2;
  -webkit-box-orient:vertical;
  overflow:hidden;
}

```