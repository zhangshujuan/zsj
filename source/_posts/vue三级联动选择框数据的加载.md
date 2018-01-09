---
title: vue三级联动选择框数据的加载
date: 2018-01-09 16:12:56
tags:
    - vue
---
### 获取json数据遍历
```apple js
<el-form-item prop="city" class="clearfix">
    <el-select v-model="ruleForm.address" placeholder="国家" class="width-200" @change="changeAddress">
        <el-option v-for="(item,index) in address" :key="item.id" :label="item.name" :value="item.name">
        </el-option>
    </el-select>
    <el-select v-model="ruleForm.prov" placeholder="省份" class="width-140" @change="changeProv">
        <el-option v-for="item in prov" :key="item.id" :label="item.name" :value="item.name">
        </el-option>
    </el-select>
    <el-select v-model="ruleForm.city" placeholder="城市" class="width-140" @change="changeCity">
        <el-option v-for="item in city" :key="item.id" :label="item.name" :value="item.name">
        </el-option>
    </el-select>
</el-form-item>
```
上面的address是json里面遍历出来的。二级三级prov,city数据获取是直接在computed里面去获取的
```apple js
computed: {
        prov: {
            get: function() {
                for (var i in this.address) {
                    if (this.address[i].name == this.ruleForm.address) {
                        this.provData = this.address[i].subList;
                        return this.address[i].subList
                    }
                }
            },
            set: function(newValue) {
            }
        },
        city: {
            get: function() {
                for (var i in this.provData) {
                    // //console.log(this.options[i].text)
                    if (this.provData[i].name == this.ruleForm.prov) {
                        this.cityData = this.provData[i].subList;
                        return this.provData[i].subList
                    }
                }
            },
            set: function(newValue) {
            }
        }
}
```
联动change的时候需要清空后面的选择框的值
```apple js
changeAddress: function(val) {
    this.address.map((s, index) => {
        if (s.name === val) {
            this.addressId = this.address[index].id;
            //手动清空后面下拉框的数据
            this.ruleForm.prov = "";
            this.ruleForm.city = "";
        }
    })
},
```
### json字符串需要用export default[]括起来
```json字符串需要用export default[]括起来
export default[]
```
## 完成代码如下

```apple js
<template>
    <div id="app" class="enter-lg">
        <div class="center-container">
            <EnterHead></EnterHead>
            <div class="table-info bg-white">
                <div class="table-bar">
                    <ul class="clearfix">
                        <li class="pull-left">1.手机注册</li>
                        <li class="pull-left active">2.完善职位信息</li>
                        <li class="pull-left">3.寻找认识的人</li>
                    </ul>
                </div>
                <div class="table-content perfect-ruleForm">
                    <el-form :model="ruleForm" :rules="rules" ref="ruleForm">
                        <el-form-item prop="city" class="clearfix">
                            <el-select v-model="ruleForm.address" placeholder="国家" class="width-200" @change="changeAddress">
                                <el-option v-for="(item,index) in address" :key="item.id" :label="item.name" :value="item.name">
                                </el-option>
                            </el-select>
                            <el-select v-model="ruleForm.prov" placeholder="省份" class="width-140" @change="changeProv">
                                <el-option v-for="item in prov" :key="item.id" :label="item.name" :value="item.name">
                                </el-option>
                            </el-select>
                            <el-select v-model="ruleForm.city" placeholder="城市" class="width-140" @change="changeCity">
                                <el-option v-for="item in city" :key="item.id" :label="item.name" :value="item.name">
                                </el-option>
                            </el-select>
                        </el-form-item>

                        <el-form-item prop="industry" class="clearfix">
                            <el-select v-model="ruleForm.preIndustry" placeholder="请选择行业" class="width-300" @change="changePreIndustry">
                                <el-option v-for="item in categoryData" :key="item.id" :label="item.name" :value="item.name">
                                </el-option>
                            </el-select>
                            <el-select v-model="ruleForm.industry" placeholder="具体行业" class="width-190" @change="changeIndustry">
                                <el-option v-for="item in industryData" :key="item.id" :label="item.name" :value="item.name">
                                </el-option>
                            </el-select>
                        </el-form-item>
                        <el-form-item label="" prop="company">
                            <el-input v-model="ruleForm.company" placeholder="请输入公司/单位名称"></el-input>
                        </el-form-item>
                        <el-form-item label="" prop="title">
                            <el-input v-model="ruleForm.title" placeholder="请输入您的职务"></el-input>
                        </el-form-item>
                        <el-button @click="submitForm('ruleForm')" class="btn-lg btn-red">下一步</el-button>
                    </el-form>
                </div>
            </div>
            <EnterFooter></EnterFooter>
        </div>
    </div>
</template>

<script>
import address from '../../../assets/js/address.js'
import industry from '../../../assets/js/industry.js'
import { fillinfo, domain } from '../../../api/api';
import placeholder from '../../../assets/js/placeholder';
import { getCookieData, GetQueryString } from "../../../assets/js/util";
import EnterHead from "components/EnterHead";
import EnterFooter from "components/EnterFooter";

export default {
    data() {
        return {
            registerThreeUrl: domain + "/enter/registerThree.html",
            domain,
            address: address,
            provData: [],
            cityData: [],
            addressId: 0,
            provId: 0,
            cityId: 0,
            categoryData: industry,
            industryItemData: [],
            preIndustryId: 0,
            industryId: 0,
            country: "",
            industryItem: "",
            value: '',
            ruleForm: {
                company: '',
                title: '',
                address: "",
                prov: "",
                city: "",
                preIndustry: "",
                industry: ""
            },
            rules: {
                title: [
                    { required: true, message: '请输入您的职务', trigger: 'blur' },
                    { validator: function (rule, value, callback) {
                        value = value.replace(/(^\s+)|\s+$/g,"");
                        if (value.length == 0) {
                            callback(new Error('不能为空'))
                        } else {
                            callback();
                        }
                    }, trigger: 'blur'}
                ],
                company: [
                    { required: true, message: '请输入公司/单位名称', trigger: 'blur' },
                    { validator: function (rule, value, callback) {
                        value = value.replace(/(^\s+)|\s+$/g,"");
                        if (value.length == 0) {
                            callback(new Error('不能为空'))
                        } else {
                            callback();
                        }
                    }, trigger: 'blur'}
                ],
                city: [
                    { required: true, message: '请选择地区', trigger: 'change' }
                ],
                industry: [
                    { required: true, message: '请选择行业', trigger: 'change' }
                ]
            }
        }
    },
    mounted() {
        let data = decodeURIComponent(getCookieData("loginmember"));
        if (!!JSON.parse(data)) {
            this.cookieData = JSON.parse(data);
        } else {
            window.location.href = this.domain + "/login.html?goto=" + window.location.href;
        }
    },
    computed: {
        prov: {
            get: function() {
                for (var i in this.address) {
                    if (this.address[i].name == this.ruleForm.address) {
                        this.provData = this.address[i].subList;
                        return this.address[i].subList
                    }
                }
            },
            set: function(newValue) {
            }
        },
        city: {
            get: function() {
                for (var i in this.provData) {
                    // //console.log(this.options[i].text)
                    if (this.provData[i].name == this.ruleForm.prov) {
                        this.cityData = this.provData[i].subList;
                        return this.provData[i].subList
                    }
                }
            },
            set: function(newValue) {
            }
        },
        industryData: {
            get: function() {
                for (var i in this.categoryData) {
                    if (this.categoryData[i].name == this.ruleForm.preIndustry) {
                        this.industryItemData = this.categoryData[i].subCategory;
                        return this.categoryData[i].subCategory;
                    }
                }
            },
            set: function(newValue) {
            }
        }
    },
    components: {EnterHead,EnterFooter},
    methods: {
        changeAddress: function(val) {
            this.address.map((s, index) => {
                if (s.name === val) {
                    this.addressId = this.address[index].id;
                    //手动清空后面下拉框的数据
                    this.ruleForm.prov = "";
                    this.ruleForm.city = "";
                }
            })
        },
        changeProv: function(val) {
            this.provData.map((s, index) => {
                if (s.name === val) {
                    this.provId = this.provData[index].id;
                    //手动清空后面下拉框的数据
                    this.ruleForm.city = "";
                }
            })
        },
        changeCity: function(val) {
            this.cityData.map((s, index) => {
                if (s.name === val) {
                    this.cityId = this.cityData[index].id;
                }
            })
        },
        changePreIndustry: function(val) {
            this.categoryData.map((s, index) => {
                if (s.name === val) {
                    this.preIndustryId = this.categoryData[index].id;
                    //手动清空后面下拉框的数据
                    this.ruleForm.industry = "";
                }
            })
        },
        changeIndustry: function(val) {
            this.industryData.map((s, index) => {
                if (s.name === val) {
                    this.industryId = this.industryData[index].id;
                }
            })
        },
        submitForm(formName) {
            let $this = this;
            this.$refs[formName].validate((valid) => {
                if (valid) {
                    var registerParams = {
                        company: this.ruleForm.company,
                        title: this.ruleForm.title,
                        country: this.addressId,
                        prov: this.provId,
                        city: this.cityId,
                        pre_industry: this.preIndustryId,
                        industry: this.industryId
                    };
                    fillinfo(registerParams).then(res => {
                        let { msg, code, data } = res;
                        if (code !== 0) {
                            if(res.errorInfo != null){
                                this.$message({
                                    message: res.errorInfo,
                                    type: "error",
                                    duration: "1000"
                                });
                            }
                        } else {
                            window.location.href = $this.registerThreeUrl;
                        }
                    });
                } else {
                    //console.log('error submit!!');
                    return false;
                }
            });
        }
    }

}
</script>

<style lang="less">
@import "../../../assets/less/register.less";
</style>

```
json数据
```apple js
export default[
  {
    "id": 10001,
    "name": "中国",
    "subList": [
      {
        "id": 1,
        "name": "北京",
        "subList": [
          {
            "id": 1099,
            "name": "北京"
          }
        ]
      }
  }
]

export default[
  {
    "id": 41,
    "name": "计算机/互联网/通信/电子",
    "subCategory": [
      {
        "id": 42,
        "name": "计算机软件"
      }
  }
]
```