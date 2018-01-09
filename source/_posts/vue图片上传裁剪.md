---
title: vue图片上传裁剪
date: 2018-01-09 15:18:33
tags:
     - vue
---
### 上传
```apple js
<el-upload id="change" ref="upload" :multiple="false"
           :action= BaseURL
           :data={imgStr:headerImage,imgName:imgName}
           :on-change="handleChange"
           :on-success="handleSuccess"
           :auto-upload="false"
           :before-upload = "beforeLoad"
>
    <el-button class="btn-red button-primary-red" style="width: auto;">选择图片</el-button>
</el-upload>
```
multiple默认是true。直接执行图片上传事件。如果不想直接上传则设为false.然后在相对应的事件那里用
### this.$refs.upload.submit();###
去执行图片上传。

### 裁剪
```apple js
 this.cropper = new Cropper(image, {
    aspectRatio: 1,//设置剪裁框的长宽比。默认的长宽比是自由比。
    viewMode: 1,
    background:false,
    zoomable:false,
    /* minCropBoxWidth:150,//剪裁框最小宽度
     minCropBoxHeight:150,//剪裁框最小高度。*/
    ready: function () {
        self.croppable = true;
    }
});
```
### 完整代码如下
``` bash
<template>
	<div id="app">
		<HbHead isIndex="false" isLogin="true"></HbHead>
		<div class="center-container clearfix">
			<div class="pull-left container-left">
				<!--左边栏-->
				<LeftBar activeText="修改头像"></LeftBar>
				<div class="tab-content account-right pull-left">
					<div class="tab-pane active editPic-panel">
						<div class="clearfix">
							<div class="pull-left title">修改头像</div>
						</div>
						<div id="picInfo">
							<!-- 遮罩层 -->
							<div class="container" v-show="panel">
								<div>
									<img id="image" :src="url" alt="Picture">
								</div>
								<button type="button" class="button cancel"  @click="cancel">取消</button>
								<button type="button" class="button" @click="crop">上传</button>
							</div>
							<div class="clearfix pic-info">
								<div class="show pull-left">
									<div class="picture" :style="'backgroundImage:url('+headerImage+')'" v-loading="loading"
										 element-loading-text="正在上传...">
									</div>
								</div>
								<div class="pull-left text-info">
									<div class="top-text">
										支持jpg、gif、png格式，存储大小不超过3M
										头像要求正面形象，清晰可见。
									</div>
									<el-upload id="change" ref="upload" :multiple="false"
											   :action= BaseURL
											   :data={imgStr:headerImage,imgName:imgName}
											   :on-change="handleChange"
											   :on-success="handleSuccess"
											   :auto-upload="false"
											   :before-upload = "beforeLoad"
									>
										<el-button class="btn-red button-primary-red" style="width: auto;">选择图片</el-button>
									</el-upload>
								</div>

							</div>
						</div>
					</div>
				</div>
			</div>
			<!--右边栏-->
			<div class="pull-right container-right">
				<AccountSetPanel isSafePanel="false" :integrity="integrity" :list="toDoList"></AccountSetPanel>
				<div class="bg-white panel common-panel">
					<div class="panel-heading">真实的头像</div>
					<div class="panel-body">
						<div>请上传真实的头像，给您的朋友留下真实、美好的印象。</div><br>
						<div>真实的头像，能使您的朋友更快速找到您并建立联系。</div>
					</div>
				</div>
			</div>
		</div>

		<!--底部  -->
		<Foot></Foot>
	</div>

</template>

<script>
    import LeftBar from 'components/AccountSetLeftbar';
    import AccountSetPanel from 'components/AccountSetPanel';
    import HbHead from 'components/HbHead';
    import Foot from 'components/Foot';
    import { getCookieData } from '../../../assets/js/util'
    import placeholder from '../../../assets/js/placeholder';
    import { BASE, userfaceShow, userface,memberCheckList,domain, base64upload } from '../../../api/api';
    import Cropper from 'cropperjs';

    export default {
        data() {
            return {
                domain,
                headerImage:'',
                cancelImage:"",
                panel:false,
                imgName:"",
                picValue:'',
                cropper:'',
                croppable:false,
                panel:false,
                url:'',
                uploadedImageURL:"",
                file:"",
                BaseURL: BASE + '/member/userface/base64upload',
                change:0,
                integrity:0,//简历完整度
                toDoList:[],
                loading: false
            }
        },
        mounted(){
            let data = decodeURIComponent(getCookieData('loginmember'));
            var self = this;
            if (!!JSON.parse(data)) {
                //更新简历完整度
                memberCheckList().then(res => {
                    let {msg, code, data} = res;
                    if (code !== 0) {
                        if(res.errorInfo != null){
                            this.$message({
                                message: res.errorInfo,
                                type: "error",
                                duration: "1000"
                            });
                        }
                    } else {
                        this.integrity = res.data.integrity;
                        this.toDoList = res.data.toDoList;
                    }
                });
                userfaceShow().then(res => {
                    let {msg, code, data} = res;
                    if (code !== 0) {
                        if(res.errorInfo != null){
                            this.$message({
                                message: res.errorInfo,
                                type: "error",
                                duration: "1000"
                            });
                        }
                    } else {
                        self.headerImage = res.data.userfaceUrl;
                        self.cancelImage = res.data.userfaceUrl;
                    }
                });
                //初始化这个裁剪框
                var image = document.getElementById('image');
                self.cropper = new Cropper(image, {
                    aspectRatio: 1,//设置剪裁框的长宽比。默认的长宽比是自由比。
                    viewMode: 1,
                    background:false,
                    zoomable:false,
					/* minCropBoxWidth:150,//剪裁框最小宽度
					 minCropBoxHeight:150,//剪裁框最小高度。*/
                    ready: function () {
                        self.croppable = true;
                    }
                });
            } else {
                window.location.href = this.domain + '/login.html?goto=' + window.location.href;
            }
        },
        components: {
            HbHead, Foot, LeftBar, AccountSetPanel
        },
        methods: {
            getObjectURL (file) {
                var url = null ;
                if (window.createObjectURL!=undefined) { // basic
                    url = window.createObjectURL(file) ;
                } else if (window.URL!=undefined) { // mozilla(firefox)
                    url = window.URL.createObjectURL(file) ;
                } else if (window.webkitURL!=undefined) { // webkit or chrome
                    url = window.webkitURL.createObjectURL(file) ;
                }
                return url ;
            },
            handleChange(file){
				this.imgName = file.imgName;
                if(this.change == 0){
                    this.panel = true;
                    this.uploadedImageURL = URL.createObjectURL(file.raw);
                    this.headerImage = "";
                    if(this.cropper){
                        this.cropper.replace(this.uploadedImageURL);
                    }
                    $('#change input').val('');
                }
                this.change = 0;
            },
            beforeLoad(){
                this.change = 1;
            },
            handleSuccess(response) {
                this.change = 1;
                this.headerImage = response.data.userfaceUrl;
                this.cancelImage = response.data.userfaceUrl;
                this.$message({ message: '上传图片成功', type: 'success', duration:'1000' });
                this.loading = false;
            },
            cancel(){
                this.change = 0;
                this.panel = false;
                $("#change").val("");//解决input框不触发change事件
                this.headerImage = this.cancelImage;
                this.cropper.replace("");
            },
            crop () {
                var $this = this;
                this.panel = false;
                var croppedCanvas;
                if (!this.croppable) {
                    return;
                }
                // Crop
                croppedCanvas = this.cropper.getCroppedCanvas();
                this.headerImage = croppedCanvas.toDataURL();
				$("#change").val("");//解决input框不触发change事件
                setTimeout(function() {
                    $this.$refs.upload.submit();
                    $this.loading = true;
                }, 0);
            }
        }

    }
</script>

<style lang="less">
	@import "../../../assets/less/member.less";
	@import "../../../assets/less/cropper.less";
</style>


```