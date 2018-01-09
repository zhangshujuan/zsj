---
title: vue开始时间和结束时间
date: 2018-01-09 16:07:19
tags:
     - vue
---

```apple js
<el-form-item label="时间">
    <el-col :span="11" prop="startTime">
        <el-date-picker
                v-model="ruleForm.eduExperienceItem.startTime"
                type="month"
                placeholder="选择日期"
                :picker-options="pickerOptions0" @change="dateChange1">
        </el-date-picker>							</el-col>
    <el-col class="line text-center" :span="2">至</el-col>
    <el-col :span="11" prop="endTime">
        <el-date-picker
                v-model="ruleForm.eduExperienceItem.endTime"
                type="month"
                placeholder="选择日期" :disabled="ruleForm.status"
                :picker-options="pickerOptions1" @change="dateChange2" class="endTime">
        </el-date-picker>
    </el-col>
</el-form-item>
<script>
    export default {
        data() {
            return {
                ruleForm:{
                    schoolAddressInfo:{
                        countryId:0,//int 学校的国家id
                        provinceId:0,//int 学校的省份id
                        schoolTableHave:true
                    },//boolean 该学校是否在学校库中
                    countryList:[
                        {id:0,//int 国家id 新增或者编辑需要传的参数
                        superId:0,
                        name:""}
					],//string 国家名
                    provinceList:[
                        {id:0,//int 省份id 新增或者编辑需要传的参数
                        superId:0,//int 国家id
                        name:""}
					],//string 省份名
                    schoolList:[
                        {countryId:0,//
                        createdDate:0,
                        id:0,//学校的id  编辑或者新增时传的参数
                        lastUpdatedDate:0,
                        name:"",//学校的名字
                        provId:0}
					],
                    eduExperienceItem: {
                        schoolId: 0,//int 学校id,
                        schoolTempId: 0,//int 临时学校id
                        schoolName: "",//学校的名字
                        countryName:"",
                        provinceName:"",
                        studyField: "",//专业
                        degree: "",//学位
                        startTime: "",//开始时间
                        endTime: "",//结束时间
                        content: "",//说明
                        timeInfo: "",//时间区间
                        duringDate: ""
                    },
                    provId:1,
					countryId:1,
					schoolId:1,
					schoolTempId:1
                },
                pickerOptions0: {
                    disabledDate(time) {
                        let beginDateVal = that.ruleForm.eduExperienceItem.endTime;
                        if (beginDateVal) {
                            return time.getTime() > new Date(beginDateVal).getTime() || time.getTime() > Date.now() - 8.64e7;
                        }
                        //return time.getTime() > Date.now() - 8.64e7;
                    }
                },
                pickerOptions1: {
                    disabledDate(time) {
                        let beginDateVal = that.ruleForm.eduExperienceItem.startTime;
                        if (beginDateVal) {
                            return time.getTime() > Date.now() - 8.64e7 || time.getTime() < new Date(beginDateVal).getTime();
                        }
                        //return time.getTime() > Date.now() - 8.64e7 || time.getTime() < new Date(that.value1).getTime();
                    }
                }
            }
        },
        computed: {
        },
        mounted(){
            

        },
        methods:{
            dateChange1(val){
                this.ruleForm.eduExperienceItem.startTime = val;
            },
            dateChange2(val){
                this.ruleForm.eduExperienceItem.endTime = val;
            }
            
        }

    }
</script>
```