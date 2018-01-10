---
title: 动态添加input框并保存值传递给后台
date: 2018-01-09 16:21:17
tags:
     - vue
---
动态添加input框并保存值传递给后台
<!-- more -->
### 遍历一个数组对象拿值
``` html
<el-form-item label="我能提供" >
    <div v-for="(preferredTagInfo, index) in ruleForm.preferredTagInfo" v-if="ruleForm.preferredTagInfo.length != 0"
            :key="preferredTagInfo.key"
            :prop="'preferredTagInfo.' + index + '.title'">
    <el-input class="width250"  v-model="preferredTagInfo.title"></el-input>
    </div>
</el-form-item>
```


### 往数组里面动态添加值
``` js
this.ruleForm.preferredTagInfo.push({
    title: "",
    key: Date.now()
});
```

### 往后台传值的时候要以逗号分隔的形式传递给后台
```js
preferredTagInfoTitle:['1','2','3']
preferredTagInfoTitle.join(",")就转换成1，2，3给后台了
```

### 完整代码如下
```html
<template>
<div id="app">
	<HbHead isIndex="false" isLogin="true"></HbHead>
	<div class="center-container clearfix">
		<div class="pull-left container-left">
			<!--左边栏-->
			<LeftBar activeText="基本信息"></LeftBar>
			<div class="tab-content account-right pull-left">
				<div class="tab-pane active">
					<div class="clearfix">
						<div class="pull-left title">{{title}}</div>
						<div class="pull-right icon-edit" v-if="isEdit==false" @click="edit">编辑</div>
					</div>
					<el-form label-width="86px" class="common-form" v-if="isEdit==false">
						<el-form-item label="我能提供">
							<div class="label-right inline-block" v-for="aimTagInfo in ruleForm.preferredTagInfo">{{aimTagInfo.title}}</div>
						</el-form-item>
						<el-form-item label="我想得到">
							<div class="label-right inline-block" v-for="tagInfo in ruleForm.aimTagInfo">{{tagInfo.title}}</div>
						</el-form-item>
						<el-form-item label="擅长">
							<div class="label-right" v-for="sinfo in ruleForm.specialtiesInfo">{{sinfo.title}}</div>
						</el-form-item>
					</el-form>
					<el-form :model="ruleForm" ref="ruleForm" label-width="86px" class="common-form editBaseInfo" v-if="isEdit==true">
						
						<el-form-item label="我能提供" >
							<div v-for="(preferredTagInfo, index) in ruleForm.preferredTagInfo" v-if="ruleForm.preferredTagInfo.length != 0"
									:key="preferredTagInfo.key"
									:prop="'preferredTagInfo.' + index + '.title'">
							<el-input class="width250"  v-model="preferredTagInfo.title"></el-input>
							</div>
						</el-form-item>
						<el-form-item>
							<div class="remark-tip mt-40">每项字数不超过8字，例如：项目投资及融资</div>
						</el-form-item>
						<el-form-item label="我想得到">
							<div v-for="(aimTagInfo, index) in ruleForm.aimTagInfo" v-if="ruleForm.aimTagInfo.length != 0"
								 :key="aimTagInfo.key"
								 :prop="'aimTagInfo.' + index + '.title'">
								<el-input class="width250"  v-model="aimTagInfo.title"></el-input>
							</div>
						</el-form-item>
						<el-form-item>							
							<div class="remark-tip mt-40">每项字数不超过8字，例如：项目投资人</div>
						</el-form-item>
						<el-form-item label="个人简介" prop="professional">
							<el-input type="textarea" v-model="ruleForm.professional" placeholder="一句话介绍你，不超过300字" class="width380"></el-input>
						</el-form-item>
						<el-form-item label="擅长">
							<div v-for="(specialtiesInfo, index) in ruleForm.specialtiesInfo"  v-if="ruleForm.specialtiesInfo.length != 0"
								 :key="specialtiesInfo.key"
								 :prop="'specialtiesInfo.' + index + '.title'" class="inline-block">
								<el-input class="width120"  v-model="specialtiesInfo.title"></el-input>
							</div>
						</el-form-item>
						<el-form-item>
							<div class="remark-tip mt-40">每项字数不超过15字，例如：项目投资人</div>
						</el-form-item>
						<el-form-item>
							<el-button class="button-primary-red" @click="updateSubmit()">保存</el-button>
							<el-button class="button-default-grey" @click="cancel()">取消</el-button>
						</el-form-item>
					</el-form>
				</div>
			</div>
		</div>
	</div>

    <!--底部  -->
	<Foot></Foot>
</div>

</template>

<script>
import LeftBar from "components/AccountSetLeftbar";
import AccountSetPanel from "components/AccountSetPanel";
import HbHead from "components/HbHead";
import Foot from "components/Foot";
import address from "../../../assets/js/address.js";
import industry from "../../../assets/js/industry.js";
import placeholder from "../../../assets/js/placeholder";
import { getCookieData } from "../../../assets/js/util";
import {
  essentialInformationShow,
  essentialInformationEdit,
  memberCheckList,
  updateIndustry,
  domain
} from "../../../api/api";

export default {
  data() {
    return {
      ruleForm: {
        preferredTagInfo: [{ title: "" }, { title: "" }, { title: "" }], //string 我能提供
        aimTagInfo: [{ title: "" }], //string  我想得到
        specialtiesInfo: [{ title: "" }, { title: "" }], //string   个人专长
      }
    };
  },
  mounted() {
    
  },
  methods: {
    addPreferredTagInfo() {
      this.ruleForm.preferredTagInfo.push({
        title: "",
        key: Date.now()
      });
    },
    addAimTagInfo() {
      this.ruleForm.aimTagInfo.push({
        title: "",
        key: Date.now()
      });
    },
    addSpecialtiesInfo() {
      this.ruleForm.specialtiesInfo.push({
        title: "",
        key: Date.now()
      });
    },
    edit() {
      this.isEdit = true;
      this.title = "编辑基本信息";
      let $this = this;
      let plen = $this.ruleForm.preferredTagInfo.length;
      let alen = $this.ruleForm.aimTagInfo.length;
      let slen = $this.ruleForm.specialtiesInfo.length;
      if (plen < 3) {
        for (var i = 0; i < 3 - plen; i++) {
          $this.addPreferredTagInfo();
        }
      }
      if (alen < 3) {
        for (var i = 0; i < 3 - alen; i++) {
          $this.addAimTagInfo();
        }
      }
      if (slen < 6) {
        for (var i = 0; i < 6 - slen; i++) {
          $this.addSpecialtiesInfo();
        }
      }
    },
    cancel() {
      this.isEdit = false;
      this.title = "基本信息";
      //加载基本信息
      essentialInformationShow({}).then(res => {
        let { msg, code, data } = res;
        //console.log(res);
        if (code !== 0) {
            if(res.errorInfo != null){
                this.$message({
                    message: res.errorInfo,
                    type: "error",
                    duration: "1000"
                });
            }
        } else {
          this.ruleForm = res.data;
          let $this = this;
          let plen = $this.ruleForm.preferredTagInfo.length;
          let alen = $this.ruleForm.aimTagInfo.length;
          let slen = $this.ruleForm.specialtiesInfo.length;
          if (plen < 3) {
            for (var i = 0; i < 3 - plen; i++) {
              $this.addPreferredTagInfo();
            }
          }
          if (alen < 3) {
            for (var i = 0; i < 3 - alen; i++) {
              $this.addAimTagInfo();
            }
          }
          if (slen < 6) {
            for (var i = 0; i < 6 - slen; i++) {
              $this.addSpecialtiesInfo();
            }
          }
        }
      });
    },
    
    submitForm(formName) {
      var $this = this;
      let aim = [];
      let specialtiesArray = [];
      let preferred = [];
      for (var i = 0; i < this.ruleForm.aimTagInfo.length; i++) {
        //console.log(this.ruleForm.aimTagInfo[i].title);
        aim.push(this.ruleForm.aimTagInfo[i].title);
      }
      for (var i = 0; i < this.ruleForm.preferredTagInfo.length; i++) {
        //console.log(this.ruleForm.preferredTagInfo[i].title);
        preferred.push(this.ruleForm.preferredTagInfo[i].title);
      }
      for (var i = 0; i < this.ruleForm.specialtiesInfo.length; i++) {
        //console.log(this.ruleForm.specialtiesInfo[i].title);
        specialtiesArray.push(this.ruleForm.specialtiesInfo[i].title);
      }
      //console.log(this.ruleForm);

      var formParam = {
        name: this.ruleForm.name,
        gender: this.ruleForm.gender,
        country: this.ruleForm.countryId,
        prov: this.ruleForm.provId,
        city: this.ruleForm.cityId,
        industry: this.ruleForm.industryId,
        industryName: this.ruleForm.industryName,
        industryParent: this.ruleForm.superIndustryId,
        preferred: preferred.join(),
        aim: aim.join(),
        specialtiesArray: specialtiesArray.join(),
        professional: this.ruleForm.professional
      };
      
    }
  },
  components: {
    HbHead,
    Foot,
    LeftBar,
    AccountSetPanel
  }
};
</script>

```