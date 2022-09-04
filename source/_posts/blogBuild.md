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
{% asset_img img.png This is an test image %}
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