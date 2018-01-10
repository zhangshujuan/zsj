---
title: vue表单验证加验证规则
date: 2018-01-09 15:03:55
tags:
     - vue
---
vue表单验证加验证规则
<!-- more -->
### 表单加正则表达式
``` js
rules: {
    mobile: [
        {required: true, message: '请输入手机号码', trigger: 'blur'},
        {min: 11, max: 11, message: '请输入正确的手机号码', trigger: 'blur'},
        { validator: function (rule, value, callback) {
            value = value.replace(/(^\s+)|\s+$/g,"");//去除空格
            var MobileRegex = /^1[0-9]{10}$/;//添加验证规则
            if (value.length == 0) {
                callback(new Error('不能为空'))
            } else {
                if (!MobileRegex.test(value)) {
                    callback(new Error('手机号码格式不正确！'))
                } else {
                    callback();
                }
            }
        }, trigger: 'blur'}
    ]
}
```

### 完整代码如下
```html
<template>
    <div class="table-content register-ruleForm">
        <el-form :model="ruleForm" :rules="rules" ref="ruleForm">
            <el-form-item label="" prop="mobile">
                <el-input v-model="ruleForm.mobile" placeholder="请输入手机号码"></el-input>
            </el-form-item>
            <el-form-item label="" prop="code">
                <el-input v-model="ruleForm.code" placeholder="请输入验证码">
                    <el-button slot="append"  @click="getConfirmCode">{{getConfirm}}</el-button>
                </el-input>
            </el-form-item>
            <el-form-item label="" prop="pass">
                <el-input type="password" auto-complete="off" v-model="ruleForm.pass" placeholder="请输入6-16位密码"></el-input>
            </el-form-item>
            <el-form-item label="" prop="realname">
                <el-input v-model="ruleForm.realname" placeholder="请输入真实的姓名"></el-input>
            </el-form-item>
            <el-button @click="submitForm('ruleForm')" class="btn-lg btn-red">下一步</el-button>
        </el-form>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                ruleForm: {
                    realname: '',
                    mobile: '',
                    code: '',
                    pass: ''
                },
                rules: {
                    mobile: [
                        {required: true, message: '请输入手机号码', trigger: 'blur'},
                        {min: 11, max: 11, message: '请输入正确的手机号码', trigger: 'blur'},
                        { validator: function (rule, value, callback) {
                            value = value.replace(/(^\s+)|\s+$/g,"");
                            var MobileRegex = /^1[0-9]{10}$/;
                            if (value.length == 0) {
                                callback(new Error('不能为空'))
                            } else {
                                if(value.substr(0, 4) != "0000"){//0000的放过
                                    if (!MobileRegex.test(value)) {
                                        callback(new Error('手机号码格式不正确！'))
                                    } else {
                                        callback();
                                    }
                                } else {
                                    callback();
                                }
                            }
                        }, trigger: 'blur'}
                    ],
                    code: [
                        {required: true, message: '请输入验证码', trigger: 'blur'},
                        { validator: function (rule, value, callback) {
                            value = value.replace(/(^\s+)|\s+$/g,"");
                            if (value.length == 0) {
                                callback(new Error('不能为空'))
                            } else {
                                callback();
                            }
                        }, trigger: 'blur'}
                    ],
                    pass: [
                        {required: true, message: '请输入6-16位密码', trigger: 'blur'},
                        {min: 6, max: 16, message: '长度在 6 到 16 个字符', trigger: 'blur'},
                        { validator: function (rule, value, callback) {
                            value = value.replace(/(^\s+)|\s+$/g,"");
                            if (value.length == 0) {
                                callback(new Error('不能为空'))
                            } else {
                                callback();
                            }
                        }, trigger: 'blur'}
                    ],
                    realname: [
                        {required: true, message: '请输入真实的姓名', trigger: 'blur'},
                        {min: 2, max: 10, message: '不超过10个中文字符', trigger: 'blur'},
                        { validator: function (rule, value, callback) {
                            value = value.replace(/(^\s+)|\s+$/g,"");
                            if (value.length == 0) {
                                callback(new Error('不能为空'))
                            } else {
                                callback();
                            }
                        }, trigger: 'blur'}
                    ]
                }
            }
        }

    }
</script>
```