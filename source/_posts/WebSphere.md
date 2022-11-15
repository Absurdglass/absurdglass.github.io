---
title: WebSphere相关问题记录
date: 2022-11-14 14:00:16
tags:
---
## 官方文档地址
以下命令均通过实际使用和参考官方文档得出，如遇到疑问请使用命令关键词查询官方文档

IBM官方参考文档|设置父类最后: [友情链接](https://www.ibm.com/docs/en/was/9.0.5?topic=caus-modifying-war-class-loader-mode-using-wsadmin-scripting)
## 自动化部署
### wsadmin scripting
官方提供了两种方式来写脚本
IBM官方参考文档|Using wsadmin scripting with Jacl (deprecated): [友情链接](https://www.ibm.com/docs/en/was/9.0.5?topic=scripting-using-wsadmin-jacl-deprecated)

IBM官方参考文档|Using wsadmin scripting with Jython: [友情链接](https://www.ibm.com/docs/en/was/9.0.5?topic=scripting-using-wsadmin-jython)

下文我们主要使用Jacl来实现，新项目建议使用Jython，Jython语法不同，如有需要可以查阅官方文档替换成Jython语法

注意：Jacl是TCL的替代实现，完全用Java™代码编写。Jacl 已被弃用，Jython 是默认的脚本语言。
### 使用 wsadmin 脚本编制自动执行服务器管理
#### 1.创建 cmd.jacl
``` bash
#设置传递的一个参数为op值，代表需要执行的操作
set op [lindex $argv 0]
set file_name [lindex $argv 1]
set script_path [lindex $argv 2]
source $script_path/$file_name.jacl
source $script_path/tools.jacl
switch -exact -- $op {
    #卸载war包
    uninstall {
        uninstall $AdminApp $AdminConfig $war_id
    }
    #安装war包
    install {
        install $AdminApp $AdminConfig $install_path $war_name $war_id $cell_name $install_cluster_name $contextroot
    }
    #安装war包后设置父类最后
    parentLast {
        parentLast $AdminConfig $war_id
    }
    #停止集群
    clusterStop {
        clusterStop $AdminControl $cell_name $cluster_name
    }
    #启动集群
    clusterStart {
        clusterStart $AdminControl $cell_name $cluster_name
    }
    #节点同步
    sync {
        sync $AdminControl $AdminConfig $cell_name
    }
    #启动集群
    restartCluster {
        restartCluster $AdminControl $AdminConfig $cell_name $cluster_name
    }
    #启动节点
    start {
        start $AdminControl $AdminConfig $nodes $cell_name $app_name
    }
    default {puts "use first argv uninstall,install,parentLast,clusterStop,clusterStart,sync,restartCluster,start"}
}
puts "exit"
```

#### 2.创建 tools.jacl
``` bash
proc clusterStop {AdminControl cell_name cluster_name} {
    puts "clusterStop---"
    set cluster [$AdminControl complateObjectName cell=$cell_name,type=Cluster,name=$cluster_name,*]
    $AdminControl invoke $cluster stop
    puts "clusterStop-success"
}
proc clusterStart {AdminControl cell_name cluster_name} {
    puts "clusterStart---"
    set clusterMgr [$AdminControl completeObjectName cell=$cell_name,type=ClusterMgr,*]
    $AdminControl invoke $clusterMgr retrieveClusters
    set cluster [$AdminControl complateObjectName cell=$cell_name,type=Cluster,name=$cluster_name,*]
    $AdminControl invoke $cluster start
    puts "clusterStart-success"
}
proc restartCluster {AdminControl AdminConfig cell_name cluster_name} {
    puts "restartCluster---"
    $AdminControl invoke [$AdminControl queryNames cell=$cell_name,type=Cluster,name=$cluster_name,*] rippleStart
    $AdminConfig save
    puts "restartCluster-success"
}
proc uninstall {AdminApp AdminConfig war_id} {
    puts "uninstall---"
    $AdminApp uninstall $war_id
    $AdminConfig save
    puts "uninstall-success"
}
proc install {AdminApp AdminConfig install_path war_name war_id cell_name cluster_name contextroot} {
    puts "install---"
    set add_webserver "+WebSphere:cell=Cell0,node=AppNode01,server=webserver1"
    if {$websever_flag == 1} {set cluster $cluster_name$add_webserver}
    $AdminApp install $install_path/$war_name [subst {-cell $cell_name -appname war_id -contextroot $contextroot -MapWebModToVH {{$war_id $war_name,WEB-INF/web.xml default_host}}}]
    $AdminConfig save
    puts "install-success"
}
proc parentLast {AdminConfig war_id} {
    puts "classloaderMode-parent-last---"
    set deployments [$AdminConfig getid /Deployment:$war_id/]
    set deploymentObject [$AdminConfig showAttribute $deployments deployedObject]
    set myModules [lindex [$$AdminConfig showAttribute $deploymentObject modules] 0]
    foreach module $myModules {
        if {[regexp WebModuleDeployment $module] == 1} {
            $AdminConfig modify $module {{classloaderMode PARENT_LAST}}
        }
    }
    $AdminConfig save
    puts "classloaderMode-parent-last-success"
}
proc sync {AdminControl AdminConfig cell_name} {
    puts "sync---"
    set node_names [$AdminControl queryNames cell=$cell_name,type=NodeSync,*]
    foreach name $node_names {
        puts $name
        $AdminControl invoke $name sync
    }
    $AdminConfig save
    puts "sync-success"
}
proc start {AdminControl AdminConfig nodes cell_name $app_name} {
    puts "start---"
    foreach node $nodes {
        catch {$AdminControl invoke [$AdminControl queryNames cell=$cell_name,type=ApplicationManager,node=$node,*] startApplication $$app_name} error
        if { $error != "" } {
            puts "failed to start $node:$error"
        }
    }
    $AdminConfig save
    puts "start-end"
}
```
#### 3.创建 paramXXX.jacl
``` bash
#设置应用名称
set app_name 包名(不需要后缀)
#打印应用名称
puts "app_name:$app_name"
#设置集群名称
set cluster_name 集群名称
#设置war包名称
set war_name "$app_name.war"
#设置warId
set war_id "_war"
set war_id $app_name$war_id
#设置单元格名称 根据需求设置，可以通过was安装页面右侧帮助查看上一个操作的脚本编制命令
set cell_name Cell0
#设置安装包路径
set install_path 安装包所在路径
#设置上下文
set contextroot 服务上下文
#是否需要增加webserver
set webserver_flag 0
```
#### 4.启动 wsadmin 工具
从 bin 目录中输入以下命令以启动 wsadmin 工具并连接到服务器
``` bash
#was安装目录bin
cd $WAS_HOME"/bin"
#启动命令及参数 $1 操作 $2 文件名参数 $3 脚本目录
param_file="param_"$2
./wsadmin.sh -conntype SOAP -user 用户名 -password 密码 -lang jacl -f 目录/cmd.jacl $1 $2 $3
```

### 其他命令
从 bin 目录中输入以下命令以启动 wsadmin 工具并连接到服务器
``` bash
bin>wsadmin -lang jython
```
从 bin 目录中输入以下命令，以本地方式并使用 Jython 脚本语言启动 wsadmin 工具
``` bash
wsadmin -conntype none -lang jython
```
使用 wsadmin 命令指定 Jython V2.1
``` bash
wsadmin -conntype soap -usejython21 true -f test.py
```
