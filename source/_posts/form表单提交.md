---
title: form表单提交
date: 2018-01-16 14:11:29
tags:
---

form表单提交
<!-- more -->
```js
$(document).on('click',".js-savePageForm",function(e){
      var __form = $($(this).data("target"));
      var _dir = $(this).data("dirurl");
      __app.myValidate(__form,function(item,text){
          $.alert(text);
      },function(){
        $.ajax({
          url:__form.attr("action"),
          type:__form.attr("method")||"post",
          data:__form.serializeArray(),
          dataType:"json",
          success:function(res){
              if(res.errorCode != 0){
                $.alert(res.errorInfo);
              }else{
                $.router.load(_dir);
              }
          }
        })
      })
  })
```

### form表单提交可以通过隐藏域传参
```html
<input type="hidden" name="appid" id="appid" value="${appInfo.appid}" />
<input type="hidden" name="apppath" id="apppath" value="${appInfo.apppath}" />
<input type="hidden" name="originalname" id="originalname" value="${appInfo.originalname}" />
<input type="hidden" name="apppackagename" id="apppackagename" value="${appInfo.apppackagename}" />
<input type="hidden" name="appversioncode" id="appversioncode" value="${appInfo.appversioncode}" />
<input type="hidden" name="appiconpath" id="appiconpath" value="${appInfo.appiconpath}" />
<input type="hidden" name="appscreenshotpath" id="appscreenshotpath" value="${appInfo.appscreenshotpath }" />
<input type="hidden" name="apksize" id="apksize" value="${appInfo.apksize}" />
```
```js
$("#save").bind("click",function(){
	var apploadl = $("#appload .files .template-upload").length;
	if (apploadl == 0) {
		alert_show("请上传应用图标");
	} else{
		$("#form1").submit();
	}
});
```


### Ajax提交
``` js
$(".mapheader input").click(function(){
   $(".loadLocation").css("display","block");  
   var str = '${deviceid}';
   var serialnumber = '${deviceLocation.serialnumber}';
   var id = '${deviceLocation.id}';
   //document.location.href = 'devLocationStr?idstr='+str+'&serialnumber='+serialnumber+'&id='+id;
   var data = 'idstr='+str+'&serialnumber='+serialnumber+'&id='+id+'&type=9&d='+(new Date()).getTime();
   $.ajax({   
       //要用post方式   
       type: "POST",   
       //方法所在页面和方法名   
       url: "devLocationStr",   
       data:data,//没有参数的初始化 
       success: function(data) { 
           //返回的数据用data.d获取内容   
        setTimeout("devLocation()",1000);
          if(data != 'successful' ){
               cmccTips.createTips("定位失败！", "middle");
          } else{
                document.location.href = 'location?deviceid='+str+'&serialnumber='+serialnumber+'&d='+(new Date()).getTime();
          }
       },
        error: function(err) {
            setTimeout("devLocation()",1000);
            cmccTips.createTips("定位失败！", "middle");
        }  
   }); 
    //禁用按钮的提交 
    return false;
});
```