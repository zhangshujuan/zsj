---
title:background属性
date: 2017-04-07 11:00:00
tags:
    - css
---
background属性
<!-- more -->
## 实现背景透明
``` bash
background-color:#000000;
background-color:rgba(0,0,0,0.2); 
```

## 实现背景图片全屏等比显示

``` bash
<div class="merber-banner-img" style="background-image: url(&quot;http://u1.renhe.cn/cover/userdefault/du2.jpg&quot;);"></div>
.merber-banner-img {
    display: block;
    background-repeat: no-repeat;
    background-size: cover;
    background-position: center center;
    -webkit-transform: translate3d(0, 0, 0);
    transform: translate3d(0, 0, 0);
    min-height: 250px;
}
```
