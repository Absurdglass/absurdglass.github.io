---
title: nginx配置及相关命令
date: 2022-11-09 16:52:52
tags:
---
nginx 教程|菜鸟教程: [友情链接](https://www.runoob.com/w3cnote/nginx-setup-intro.html)
### 重新打开日志文件
``` bash
nginx目录/sbin/nginx -s reoprn
```
### 重新加载nginx配置信息
``` bash
nginx目录/sbin/nginx -s reload
```
### nginx启动
``` bash
./nginx目录/sbin/nginx
```
### session保持
``` bash
http{
    upstream xxx {
        #ip_hash 用于session粘连
        ip_hash;
        server xxx.xxx.xxx.xxx:xxxx weight=1;
        server xxx.xxx.xxx.xxx:xxxx weight=1;
        keepalive 65;
    }
}
```
### 端口转发配置
``` bash
http{
    location /xxx {
        proxy_pass http://xxx;
        proxy_set_header Host $http_host;
        proxy_redirect / http://$host:$server_port/;
        ...
        proxy_connect_timeout 5; #5秒超时
    }
}
```
配置开机自启动：

方式一：在/etc/rc.local中配置

可以将此命令加入到rc.local文件中，这样开机的时候nginx就默认启动了

vi /etc/rc.local

加入一行  /etc/init.d/nginx start    保存并退出，下次重启会生效。



方式二：将nginx配置成自启动的服务

1.添加至服务管理列表，并让其开机自动启动
``` bash
chkconfig --add nginx
chkconfig nginx on
chkconfig nginx --list
```
日志切片待学习