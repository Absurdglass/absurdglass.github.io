---
title: Hexo个人博客搭建
date: 2022-09-04 22:27:42
tags:
---
## Hexo个人博客搭建

### 准备工具
Git: [友情链接](https://git-scm.com/downloads)  
Hexo: [友情链接](https://hexo.io/zh-cn/index.html)  
Node.js: [友情链接](http://nodejs.cn/)  
GitHub: [友情链接](https://github.com/)

### 安装
git与node.js需要安装

### 1.新建文件夹
在自己指定目录下新建一个文件夹，例如当前为
``` bash
G:\hexo
```
{% asset_img img_1.png This is an test image %}  
然后在该文件夹空白处右键  
{% asset_img img_2.png This is an test image %}  
点击
``` bash
Git Bash Here
```
### 2.在git中使用命令
npm命令需要在安装node.js后才可使用
``` bash
npm install hexo-cli -g
```
### 3.验证安装完成命令
``` bash
hexo -v
```
### 4.hexo初始化
``` bash
hexo init
```
### 5.hexo生成
``` bash
hexo generate
```
也可以使用简写
``` bash
hexo g
```
### 6.hexo启动服务
``` bash
hexo s
```
{% asset_img img_3.png This is an test image %}

### 7.hexo本地页面访问

### 8.生成ssh
``` bash
git config --global user.name "你的用户名"
git config --global user.email "你的gitHub邮箱"
ssh-keygen -t rsa -C "你的gitHub邮箱"
```
在git bash给出的路径中找到 id_rsa.pub 复制内容
### 9.gitHub创建仓库
{% asset_img img_4.png This is an test image %}

需要注意仓库名称必须是 你的用户名.github.io  
### 10.gitHub添加deploy keys，并允许覆盖
{% asset_img img_5.png This is an test image %}  
{% asset_img img_6.png This is an test image %}

### 11.修改Hexo项目文件夹下单_config.yml文件，拉到最底部找到deploy:填写如下内容
``` bash
type: 'git'
    repo: https://github.com/你的用户名/你的用户名.github.io.git
    branch: master
```

### 12.git中执行以下命令
``` bash
npm install hexo-deployer-git --save  #安装部署工具
hexo clean                            #清除缓存
hexo generate                         #生成静态文件    可缩写hexo g
hexo deploy                           #部署到Github   可缩写hexo d
```

### 13.在浏览器中输入 http://absurdglass.github.io/archives/

### issues
关于分支pull hexo并初始化配置后执行hexo g无法生成index.html的问题

一种可能是npm插件不完整,请使用如下命令检查
``` bash
npm ls --depth 0
```
如果插件完整,可能是hexo版本与package.json中的hexo信息版本不一致,请检查两个版本是否一致
``` bash
hexo -v
```
执行hexo d未能更新
可以尝试如下方法

1.删.deploy_git文件夹，然后hexo d

2.删除页面缓存

3.hexo clean之后 hexo deploy -g