---
title: axios请求拦截
date: 2018-01-09 14:52:06
tags:
---
axios请求拦截
<!-- more -->

axios请求拦截api文件
```js
import axios from 'axios/dist/axios.min';
var qs = require('qs');
let header = { "content-type": "application/x-www-form-urlencoded" };
let base = "http://web-dev.renhe.cn/privateapi";
export const BASE = "http://web-dev.renhe.cn/privateapi";
//发送加好友的接口
export const addFriend = params => { return axios.post(`${BASE}/contact/addFriend`, qs.stringify(params), { headers: header }).then(res => res.data); };


//token拦截
// 超时时间
axios.defaults.timeout = 5000;
axios.interceptors.request.use(config => {
    return config
}, error => {
    return Promise.reject(error)
})
// http响应拦截器
axios.interceptors.response.use(data => {// 响应成功关闭loading
    if(data.data.code == -4 && data.data.data.reminderCode == -3){
        localStorage.clear();
        localStorage.setItem("resultContent",data.data.data.resultContent);
        if(data.data.data.reasonContent){
            localStorage.setItem("reasonContent",data.data.data.reasonContent);
        }
        if(data.data.data.showButton){
            localStorage.setItem("showButton",data.data.data.showButton);
        }
        if(window.location.href != domain+"/home/span.html"){
            window.location.href = domain+"/home/span.html";
        }
    }
    return data
}, error => {
    return Promise.reject(error)
})
```