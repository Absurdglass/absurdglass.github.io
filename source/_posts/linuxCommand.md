---
title: linux命令
date: 2022-11-08 15:51:51
tags:
---
## linux命令
Linux命令大全(手册): [友情链接](https://www.linuxcool.com/)
Shell 教程|菜鸟教程: [友情链接](https://www.runoob.com/linux/linux-shell.html)
### 常用命令
``` bash
cd 目录 #进入指定目录
cp xxx newxxx #复制文件
mv xxx newxxx #移动文件
ls -lrt 目录 #查看目录下文件，不输入目录则查看当前目录文件
du -h 目录 #查看目录占用空间
du -h --max-depth=1 |sort #查看当前目录下所有一级子目录文件夹大小 并排序
log_date=$(data -d yesterday +%Y-%m-%d) #获取昨天的时间 使用时格式 ${log_date}
find 目录 -type f -name "*.log" -ctime +7 -exec rm -f {} \; #找出超过7天的日志文件并删除
ps -ef|grep redis #查看redis服务进程情况，这里拿redis服务举例,其他服务查询更改名字即可
lsof -i :6379 #查看6379（为redis的端口号）端口号是否被占用
netstat -tnlp #显示tcp的端口和进程等相关情况
grep 查询内容 目录/文件 | wc -l #查询文件指定内容并统计行数
```
### 创建定时任务
``` bash
crontab -l #查看当前用户的定时任务
crontab -e #设置用户级别的定时任务，可以通过定时任务调用脚本
* * * * * echo `date` >> /home/xxx/time.log
* * * * * /bin/sh /webapps/logs/xxx.sh
```
配置系统级别的任务直接使用 root 权限编辑系统级别定时任务的配置文件： /etc/crontab

### 监控url脚本，通过定时任务结合使用
``` bash
#!/bin/sh
function usage() {     #<==帮助函数
    echo $"usage:$0 url"
    exit 1
}
function check_url() { #<==检测URL函数。
    wget --spider -q -o /dev/null --tries=1 -T 5 $1 
   #<==采用wget返回值方法，这里的$1就是函数传参。
    #curl -s -o /dev/null $1 #<==采用curl返回值方法也是可以的。
    if [ $? -eq 0 ]
    then
        echo "$1 is yes."
        exit 0
    else
        echo "$1 is fail."
        ##失败后的逻辑、发送短信、邮件等等
        exit 1
    fi
}
function main() {   #<==主函数。
     if [ $# -ne 1 ]   #<==如果传入的多个参数，则打印帮助函数，提示用户。
     then
         usage
     fi
     check_url $1     #<==接收函数的传参，即把结尾的$*传到这里。
}
main $*            #<==这里的$*就是
```

### 服务启动校验
``` bash
#!/bin/bash
url=http://xxx
code=`curl -I -m 30 -o /dev/null -s -w %{http_code}"\n" $url`
echo "访问时间是：`date '+%Y-%m-%d %H:%M:%S'`--$code--->$url"
#while循环访问url，直到状态码200或者达到最大重试次数结束
count=1
while ( [ $code -ne 200 -a $count -lt 9 ] )
do
 sleep 20s
 echo "访问时间是：`date '+%Y-%m-%d %H:%M:%S'`--$code--->$url"
 ((count++))
done
```