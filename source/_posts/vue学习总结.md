---
title: vue学习总结.md
date: 2018-01-09 11:12:03
tags:
     - vue
---

## vue路径跳转带参数
``` bash
<el-table-column prop="operation" label="操作">
	<template scope=“scope">
		<router-link :to="{path:'announceDetail',query: {id:scope.row.id}}" ><el-button type="text">查看</el-button></router-link>
	</template>
</el-table-column>
```
获取id:this.$route.query.id

## 去掉浏览器的#配置
``` mode类型设置为history
const router = new VueRouter({
    mode:"history",
    routes
})
```


## 动态的class判断

```动态的class判断
<s :class="{'level-icon level-icon-plat':item.level == '铂金会员','level-icon  level-icon-gold':item.level == '黄金会员','level-icon level-icon-vip':item.level == 'vip','level-icon  level-icon-auth':item.level == '实名认证'}" :title=“item.level"></s>
```


## 多页面入口配置页面内容绝对跳转路径

``` api文件中配置domain
export const domain = “http://wwwdev.renhe.cn:805/views”; 
```
``` 在页面中获取domain
import { domain } from '../api/api';
<a :href="baseInfoUrl">基本信息</a>
export default {
    data() {
        return {
            baseInfoUrl: domain + "/member/baseInfo.html"
        }
    }
}

```

## 表单验证通过vm不支持的箭头函数
```vue环境
submitForm(formName) {
    var $this = this;
    this.$refs[formName].validate((valid) => {
        if (valid) {
            
        } else {
            //console.log('error submit!!');
            return false;
        }
    });
},
```

```vm环境
var promise=this.$refs[formName].validate();
promise.then(function(value){
    console.log(value);
}).catch(function(error){
    console.log(error)
});

```

## 通过class判断某个事件是否存在
``` 只有存在letter.showChooseLetter的时候才会触发search事件
<a href="javascript:void(0)" v-for="(letter,index) in letters" 
:class="{'letter':letter.showChooseLetter==true,'letter disabled':letter.showChooseLetter==false}"  :key="index"  
@click="letter.showChooseLetter && search($event)">{{letter.letter}}</a>
```

## 图片解析

直接在html中引入图片,在当前文件夹加一个assets文件夹放入图片。图片如果是动态的需要用require转base64解析图片
```apple js
 <img :src="require('./assets/'+EmojiValues[indexOne][index])">
```
如果是css中写图片直接写即可
```apple js
.icon-phone {
    background: url("../images/icon_contact.png");
}

```
## 验证码倒计时

``` bash
 data() {
    return {
        getConfirm:"获取验证码",
        isNotAllowed : false
    }
}
getConfirmCode(){
    let _this = this;
    let mobile = _this.ruleForm.mobile;
    let params = { mobile : mobile };
    if(!_this.isNotAllowed) {
        let i = 60;
        var timer = setInterval(function() {
            --i;
            _this.getConfirm = i + "秒后重试";
            _this.isNotAllowed = true;
            if(i == 0) {
                clearInterval(timer);
                _this.getConfirm = "获取验证码";
                _this.isNotAllowed = false;
            }
        },1000);
        smscode(params).then(res => {
            if (res.code !== 0) {
                if(res.errorInfo != null){
                    _this.$message({
                        message: res.errorInfo,
                        type: 'error', duration:'1000'
                    });
                }
                clearInterval(timer);
                _this.isNotAllowed = false;
            }else if(res.code == 0){
                _this.$message({
                    message: "验证发送成功，请及时查看手机",
                    type: 'success', duration:'1000'
                });
            }
        })
    }else{
        return false;
    }
}
```