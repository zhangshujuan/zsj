---
title: fileupload插件的研究
date: 2018-01-16 15:15:10
tags:
---
fileupload插件的研究
<!-- more -->
获取当前上传请求的地址是
``` js
var jqXHR = data.submit()
.success(function (result, textStatus, jqXHR) {/* ... */})
.error(function (jqXHR, textStatus, errorThrown) {/* ... */})
.complete(function (result, textStatus, jqXHR) {/* ... */});
```
终止上传的请求是
``` js
jqXHR.abort();//终止上一次请求
```
删除销毁当前文件
```html
<i class="fileRemove delete" data-ng-click="file.$destroy()"></i>删除销毁当前文件
```
清空进度条
``` js
$('#progress .progress-bar').remove();//清空进度条
$('#progress').append('<div class="progress-bar progress-bar-success"></div>');
```


``` js
var jqXHR;
//应用上传
$("#fileupload").click(function(){
	appid = $("#appid").attr("value");
	$('#fileupload').fileupload({
    		add: function (e, data) {
    			if (e.isDefaultPrevented()) {
                    return false;
                }
                var $this = $(this),
                    that = $this.data('blueimp-fileupload') ||
                        $this.data('fileupload'),
                    options = that.options;
                data.context = that._renderUpload(data.files)
                    .data('data', data)
                    .addClass('processing');
                options.filesContainer[
                    options.prependFiles ? 'prepend' : 'append'
                ](data.context);
                that._forceReflow(data.context);
                that._transition(data.context);
                if(jqXHR){                	
                	jqXHR.abort();//终止上一次请求
                }
                data.process(function () {
                    return $this.fileupload('process', data);
                }).always(function () {
                	$("#appload").find("img").attr("src","css/change/images/appi.png");	
			        $(".formError").css("display","none");//清空提示错误
			        $("#filename").removeClass("borderShadow");
			        $("#filename").css("color","#999");
			        $("#filename").css("border","1px solid #8bbdea");
                    data.context.each(function (index) {
                        $(this).find('.size').text(
                            that._formatFileSize(data.files[index].size)
                        );
                    }).removeClass('processing');
                    that._renderPreviews(data);
                  //第一步先情况文件名和大小
		        	$("#filename").attr("value"," ");
			        $("#apksize").attr("value"," ");
			        $("#appversionname").val("");
    		        $("#changesize").val("");
    		        $("#appname").val("");
			        $('#progress .progress-bar').remove();//清空进度条
                    $('#progress').append('<div class="progress-bar progress-bar-success"></div>');
                }).done(function () {
                	$(".error").text("");
                    data.context.find('.start').prop('disabled', false);
                    if ((that._trigger('added', e, data) !== false) &&
                            (options.autoUpload || data.autoUpload) &&
                            data.autoUpload !== false) {
                    	data.submit();
                    }
                }).fail(function () {
                    if (data.files.error) {
                        data.context.each(function (index) {
                            var error = data.files[index].error;
                            if (error) {
                            	$(".error").text(error);
                            	alert_show(error);
                                $(this).find('.error').text(error);
                            }
                        });
                    }
                });
               jqXHR = data.submit()
                .success(function (result, textStatus, jqXHR) {/* ... */})
                .error(function (jqXHR, textStatus, errorThrown) {/* ... */})
                .complete(function (result, textStatus, jqXHR) {/* ... */});
             },
	        url:  "appUpload?appid="+appid,//?appid=1
	        dataType: 'json',
        	acceptFileTypes: /(\.|\/)(apk)$/i,//只支持apk文件
        	maxFileSize:1024*1024*100,//单个文件最大100M
  	        maxFileNameSize:140,//限制文件名的字符长度
  	        maxNumberOfFiles:1,
     		singleFileUploads:false,
  	        messages: {
              maxNumberOfFiles: '文件超过了最大个数，请删除后再重新上传',
              acceptFileTypes: '上传的文件类型不正确，请重新上传',
              maxFileSize: '文件太大了(最大100M)，请重新上传',
              maxFileNameSize: '文件名太长了(最大140)，请重新上传'
            },
	        autoUpload: true,
	        success: function(file,done, data){//成功的情况下执行
				apksize = file.apksize;
	        	apppath = file.apppath;
	        	appid = file.appid;
	        	originalname=file.originalname;
				apppackagename=file.apppackagename;
				appversioncode=file.appversioncode;
				appversionname=file.appversionname;
				imgName = file.appiconpath;
			},	       
			progressall: function (e, data) {
	            var progress = parseInt(data.loaded / data.total * 100, 10);
       			$('#progress .progress-bar').css(
    	                'width',
    	                progress + '%'
    	        );
	        },
	        fail: function(e, data){//断网上传失败的情况下
	        	if(data.context.length != 0) {
	        		$("#filename").validationEngine('validate');
				    $(".filenameformError .formErrorContent").text(data.files[0].name+'文件上传失败');
					$('#progress .progress-bar').remove();//清空进度条
	                $('#progress').append('<div class="progress-bar progress-bar-success"></div>');
	        	}
	   		},
	        done:function(e,data){
				//第一步先清空文件名和大小
	        	$("#filename").attr("value"," ");
		        $("#apksize").attr("value"," ");
	        	var message = data.result;
	        	if (message == '0' || message == '1') {//文件格式不正确的时候
			        $("#filename").validationEngine('validate');
			        $(".filenameformError .formErrorContent").text("所选文件不是一个正确的apk文件");
			        $('#progress .progress-bar').remove();//清空进度条
                    $('#progress').append('<div class="progress-bar progress-bar-success"></div>');
 	        	}else if(message == '2' ){
 	        		$(".filenameformError .formErrorContent").text("* 此处不可空白");
 	        		alert_show("已经存在相同的应用");
 	        		$('#progress .progress-bar').remove();//清空进度条
                    $('#progress').append('<div class="progress-bar progress-bar-success"></div>');
 	        	} else {//正确的情况  	        		
 	        		$(".progress-bar").removeClass("progress-error");
 	        		$(".progress-bar").text("");
            		//给文件名和大小赋值
            		$("#filename").attr("value",data.files[0].name);
    		        $("#apksize").attr("value", apksize);
    		        $("#changesize").attr("value", fileSize(apksize));
    		        
    		        //上传成功就赋值appid    		       
    		        $("#appid").attr("value",appid);
    		        $("#apppath").val(apppath);
    		        $("#originalname").val(originalname);
    		        $("#apppackagename").val(apppackagename);
    		        $("#appversioncode").val(appversioncode);
    		        $("#appname").val(originalname);
    		        $("#appversionname").val(appversionname);
    		    	$("#filename").validationEngine('validate');
    		        $("#appname").validationEngine('validate');
    		        $("#appname").next(".errorIcon").remove();
    		        $("#appversionname").validationEngine('validate');
    		        $("#appversionname").next(".errorIcon").remove();
    		        $("#changesize").validationEngine('validate');    		        
    		        $("#changesize").next(".errorIcon").remove();
    		        $(".formError").css("display","block");//先点击确定情况下的错误消息提示显示
    		        $(".filenamewidth  .errorIcon").remove();
                    if(imgName){
        		        $('#appload').addClass("addImg");
        	   			$("#appload .btn-addicon img").attr("src","getImg?name=" + imgName);
        	   			$("#appiconpath").val(imgName);
        	   		 	hover();
                    }
 	        	}
	        }
	    }).prop('disabled', !$.support.fileInput)
	        .parent().addClass($.support.fileInput ? undefined : 'disabled');
});

//应用上传
	$("#clounFilefileupload").click(function(){
    	$('#clounFilefileupload').fileupload({
    		add: function (e, data) {
    			if (e.isDefaultPrevented()) {
                    return false;
                }
                var $this = $(this),
                    that = $this.data('blueimp-fileupload') ||
                        $this.data('fileupload'),
                    options = that.options;
                data.context = that._renderUpload(data.files)
                    .data('data', data)
                    .addClass('processing');
                options.filesContainer[
                    options.prependFiles ? 'prepend' : 'append'
                ](data.context);
                that._forceReflow(data.context);
                that._transition(data.context);
                data.process(function () {
                    return $this.fileupload('process', data);
                }).done(function () {
                	$(".unclick").css("display","block");
                	$(".save_btn").css("display","none");
                	data.context.find(".delete").click(function(){//点击删除事件
	             		var index = $(this).parents(".filenamewidth").attr("index");
	             		var rPath = $(this).attr("path");
        		    	$(this).parents(".filenamewidth").remove();        		    	
        		    	var fTitle = $(this).parent(".filename").children("span").attr("title");
        		    	stiltes.remove(fTitle);
        		    	if($(this).parents(".filenamewidth").find(".progress-bar").attr("style") == 'width: 100%;'){           		    		
        		    		completeNum.remove(0);//判断已经上传成功的个数           		       
        		    	}
        		    	var filePaths = "";
        		    	var fileNum = [];//判断有没有文件正在上传
        		    	$(".fileRemove").each(function(){//遍历name数组  
        		    		filePaths += $(this).attr("path") +":";
        		    		fileNum.push(1);//判断有没有文件正在上传
        		    	});
        		    	$("#filePath").val(filePaths);
        		    	if(fileNum.length == completeNum.length){
	       		   	    	$(".unclick").css("display","none");
	       	            	$(".save_btn").css("display","block");
	       		   	    }
	           		});
                    data.context.find('.start').prop('disabled', false);
                    if ((that._trigger('added', e, data) !== false) &&
                            (options.autoUpload || data.autoUpload) &&
                            data.autoUpload !== false) {
                        data.submit();
                    }
                    
                    data.context.each(function (index) {
                        var name = data.files[index].name;
                        if(stiltes.in_array(name) == true){//有同名的情况 
                        	alert_show("有同名文件，请重新上传");
                        	$(this).remove();
                        }
                        if(name.length > 20){	                  		
                        	 $(this).find("span").html(name.substring(0, 20)+'....');
                    	}                       
                    });
                    stiltes=[];
                    var index=0;
                    $(".filenamewidth").each(function(){//遍历name数组                    	
                    	index++;
                    	$(this).attr("index",index);
                    	var stilte = $(this).find("span").attr("title");
                    	stiltes.push(stilte);
                    });
                }).fail(function () {
            		//第一步先情况文件名和大小
    	        	if (data.files.error) {
                        data.context.each(function (index) {
                            var error = data.files[index].error;
                            if (error) {
                            	alert_show(error);
                                $(this).remove();
                            }
                        });
                    }
                });
             },
           	url:  "cloudFileUpload",
       		dataType: 'json',   		
       		acceptFileTypes: /(\.|\/)(xls|xlsx|ppt|pptx|doc|docx|txt|pdf|jpg|jpeg|png)$/i,//只支持apk文件   
    	   	maxFileSize:1024*1024*200,//单个文件最大200M
    	    minFileSize:1,//单个文件最大200M
    	    maxFileNameSize:140,//限制文件名的字符长度
    	    maxNumberOfFiles:1,
    	    singleFileUploads:false,
    	    messages: {
    	    	 acceptFileTypes: '上传的文件类型不正确，请重新上传',
    	         maxFileSize: '文件太大了(最大200M)，请重新上传',
    	         minFileSize: '文件不能为空，请重新上传',
    	         maxFileNameSize: '文件名太长了(最大140)，请重新上传',
    	         maxNumberOfFiles: '文件一次只能选择一个文件'
    	    },
    	    autoUpload: true,     
       		type:'post',
	        fail: function(e, data){//断网上传失败的情况下
	        	var title = data.context.find("span").attr("title");
	        	if($(".files").find(data.context).length != 0){
	        		parent.window.Tips.alert('提示',title+'文件上传失败','deletetips',function(){});
     				data.context.remove();
	        	}
	   		},
       		done:function(e, data){
       			var title = data.context.find("span").attr("title");
	     		//断网上传失败的情况下
		     	if(data.result == null){
     				parent.window.Tips.alert('提示',title+'文件上传失败','deletetips',function(){});
     				data.context.remove();
     				return false;
		     	}		     	
       			var len = $(this).find(".fileRemove").length;
       			$(this).find(".fileRemove:eq("+(len-1)+")").attr("path",data.result.path);
       			var result = data.result.resultCode;
	   	         if(result == 0){
	   	            //上传成功
	   	            var filePath = data.result.path;	
	   	            completeNum.push(1);//判断已经上传成功的个数
	   	         } else {
	   	            var resultMessage = data.result.resultMessage;
	   	            alert_show(resultMessage);
	   	            data.context.remove();
	   	         }	   	      
		   	     var filePaths = "";
		   	     var fileNum = [];//判断有没有文件正在上传
		   	     $(".fileRemove").each(function(){//遍历name数组  
	               	filePaths += $(this).attr("path") +":";
	               	fileNum.push(1);//判断有没有文件正在上传
	             });
		   	     $("#filePath").val(filePaths);
		   	     if(fileNum.length == completeNum.length){
		   	    	$(".unclick").css("display","none");
	            	$(".save_btn").css("display","block");
		   	     }
		   	    
       		}
       	}).prop('disabled', !$.support.fileInput)
        .parent().addClass($.support.fileInput ? undefined : 'disabled');    	
});	   
});

```
```html
<script id="template-upload" type="text/x-tmpl" charset="utf-8">
{% for (var i=0, file; file=o.files[i]; i++) { %}
    	<div class="filenamewidth">
				<div class="filename"><span title="{%=file.name%}">{%=file.name%}</span>
					<i class="fileRemove delete " data-ng-click="file.$destroy()"></i>
				</div>
           		 <div class="progress" id="progress" role="progressbar" aria-valuemin="0" aria-valuemax="100" aria-valuenow="0">
					<div class="progress-bar progress-bar-success" style="width:0%;"></div>
				</div>
				<div class="error"></div>				
			</div>
{% } %}
</script>
<script id="template-download" type="text/x-tmpl" charset="utf-8">
{% for (var i=0, file; file=o.files[i]; i++) { %}
    	<div class="filenamewidth">
				<div class="filename"><span title="{%=file.name%}">{%=file.name%}</span>
					<i class="fileRemove delete" data-ng-click="file.$destroy()"></i>
				</div>
				<div class="progress" id="progress">
					<div class="progress-bar progress-bar-success" style="width: 0%;"></div>
				</div>
				<div class="error"></div>				
			</div>

{% } %}
</script>
```