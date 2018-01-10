---
title: hexo环境搭建github博客
date: 2018-01-10 16:59:38
tags:
---
hexo环境搭建github博客
<!-- more -->
新建仓库
New repository
在仓库名字输入框中输入：（项目名称可以随便）

在本地新建一个分支： 
``` bash
$ git branch dev
```
切换到你的新分支:
 ``` bash
 $ git checkout dev
 ```
在dev分支下面去搭建hexo环境。搭建好会自动编译到marster分支
第一步要切换node版本
``` bash
$ nvm use 7.5.0
```
下载安装hexo
``` bash
$ npm install -g hexo-cli
```
安装好hexo以后，在终端输入：
``` bash
$ hexo
```

初始化博客
// 建立一个博客文件夹，并初始化博客，<folder>为文件夹的名称，可以随便起名字
$ hexo init <folder>
// 进入博客文件夹，<folder>为文件夹的名称
$ cd <folder>
// node.js的命令，根据博客既定的dependencies配置安装所有的依赖包
$ npm install


发表一篇文章
在终端输入：

// 新建一篇文章
``` bash
$ hexo new "文章标题"
```

启动服务预览：
``` bash
$ hexo server
```
生成
``` bash
$ hexo g
```
部署
``` bash
$ hexo d
```
hexo g == hexo generate#生成
hexo s == hexo server #启动服务预览
hexo d == hexo deploy#部署


最后在github Settings上面去配置github pages
 网站为 www.zhangshujuan.cn
 
 CNAME文件内容www.zhangshujuan.cn