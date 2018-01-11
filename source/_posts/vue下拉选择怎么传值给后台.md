---
title: vue下拉选择怎么传值给后台
date: 2018-01-11 14:10:22
tags:
---
vue下拉选择怎么传值给后台
<!-- more -->
``` html
<el-form-item label="性质">
    <el-select v-model="ruleForm.orgtype" placeholder="请选择"  @change="type">
        <el-option v-for="(item, index) in ruleForm.orgTypeList" :key="item.key" :label="item.value" :value="item.value"></el-option>
    </el-select>
</el-form-item>
<el-form-item label="规模">
    <el-select v-model="ruleForm.orgsize" placeholder="请选择" @change="size">
        <el-option v-for="(item, index) in ruleForm.orgSizeList" :key="item.key" :label="item.value" :value="item.value" ></el-option>
    </el-select>
</el-form-item>
```
```js
//orgTypeList:[{key:1,value:""}],//string 类型名
//orgSizeList:[{key:1,value:""}],
type(val){
    this.ruleForm.orgTypeList.map((s,index)=>{
        if(s.value===val){
            this.ruleForm.typeId = this.ruleForm.orgTypeList[index].key;
            //console.log(this.ruleForm.typeId);
        }
    })
},
size(val){
    this.ruleForm.orgSizeList.map((s,index)=>{
        if(s.value===val){
            this.ruleForm.sizeId = this.ruleForm.orgSizeList[index].key;
            //console.log(this.ruleForm.sizeId);
        }
    });
}
```