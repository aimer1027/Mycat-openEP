# Mycat Openep Docker 平台构建过程

------
在本文档中简单介绍一下如何在 windows 安装 docker 来快速的搭建 Mycat Openep Docker 平台
更详细的资料请看链接 [详细资料链接地址](https://github.com/aimer1027/Mycat-openEP/blob/master/docker/README.md)


# 第三方docker资料

### [docker安装包快速下载](http://pan.baidu.com/s/1dDew2m1)

[docker入门指南][1]

[mac docker安装手册][2]

[windows docker安装手册][3]

# Docker环境准备
>* 在Windows 上安装 Boot2Docker ，该镜像文件链接地址: http://pan.baidu.com/s/1kTm2MUF , 
>* 双击 docker-install.exe 该文件即可，该过程会自动安装 git ，vbox 应用程序(如果之前系统中没有安装的话)
>* 在成功安装之后，会在桌面上看到名为 Boot2Docker Start 的图标，双击它便会启动 docker
>* docker 启动若是成功，会在命令行中显示如下的信息：
```
initializing...
Virtual machine boot2docker-vm already exists
starting...
Waiting for VM and Docker daemon to start...
......
Started.
Docker client does not run on Windows for now. Please use
    "F:\docker\Boot2Docker for Windows\boot2docker.exe" ssh
to SSH into the VM instead.

connecting...
                        ##        .
                  ## ## ##       ==
               ## ## ## ##      ===
           /""""""""""""""""\___/ ===
      ~~~ {~~ ~~~~ ~~~ ~~~~ ~~ ~ /  ===- ~~~
           \______ o          __/
             \    \        __/
              \____\______/
 _                 _   ____     _            _
| |__   ___   ___ | |_|___ \ __| | ___   ___| | _____ _ __
| '_ \ / _ \ / _ \| __| __) / _` |/ _ \ / __| |/ / _ \ '__|
| |_) | (_) | (_) | |_ / __/ (_| | (_) | (__|   <  __/ |
|_.__/ \___/ \___/ \__|_____\__,_|\___/ \___|_|\_\___|_|
boot2docker: 1.2.0
             3.16.1-config-file : e75396e - Fri Aug 22 06:45:30 UTC 2014


```
>* 之后，通过 ssh 工具，远程登录，登录的 ip=192.168.59.103 , username=docker ,port=22, password=tcuser 
>* 在成功远程登录 docker 之后，我们便需要在这里来创建 docker 镜像文件，通过开始给的 [下载链接](http://pan.baidu.com/s/1dDew2m1) 来下载 openep-1.2.tgz 该文件，通过 ssh 工具将该文件发送到远程端
>* 通过如下命令来将该压缩文件中的内容导出生成 docker 镜像文件，
````
gunzip -c openep-1.2.tgz | docker import - [要为生成的镜像文件所起的名称]
````
>* 接下来，要根据镜像文件来生成，docker 容器,不过在此直线需要在当前文件路径下创建如下脚本,并且将其权限更改为 755<br/>
   [脚本1:名称为 run-host.sh](https://github.com/aimer1027/Mycat-openEP/blob/master/docker/run-host.sh) <br/>
   [脚本2:名称为 run.sh](https://github.com/aimer1027/Mycat-openEP/blob/master/docker/run.sh)
>* 根据镜像文件生成容器命令如下所示
```shell
./run-host.sh [刚刚你为镜像文件所起的名字] [你打算给生成容器所起的名字] 

```
>* 根据如下命令来查询所有 docker 镜像文件
```
docker images
```

>* 根据如下命令来查询所有生成 docker 容器的名称
````
docker ps 
````
>* 根据下面的命令来启动 docker 容器
````
docker start [你刚刚为容器所起的名字]
````
>* 至此为止，该容器已经成功运行
>* 接下来，我们只需要登录该正在运行容器即可
>* 使用ssh 远程登录工具 ip=192.168.59.103,username=root, port=2222, password=123456 来远程登录该 openep 容器即可
# 使用说明

组件列表:

| 组件         |  开放端口  | 说明                              		       |
| ----         | -----      | ----                          		           |
| ssh          | 2222       | ssh服务                      		             |
| supervisor   | 9001       | 服务管理 http://192.168.59.103:9001                      		           |
| samba        | 139,445    | 文件共享                    		               |
| scm          |            | git                         		               |
| zsh          |            | 好用的shell                 			            |
| jdk7         |            |                             			           |
| jdk8         |            |                                       	       |
| maven        |            | java构建工具                           		   |
| mycat        | 8066 9066  | 数据库中间件                     		         |
| mysql        | 3306       | 数据库                                		    |
| nginx        | 80         | web服务器                              		    |
| php          |            |                                        		   |
| phpmyadmin   |            |  |
| tomcat       | 8080       | servlet容器                            			 |
| probe        |            | tomcat管理工具http://192.168.59.103:8080/probe |
| zookeeper    | 2181       | 集群工具                               		   |
| ice          | 4061 8888  | rpc中间件                              			|
 

# 如何管理

1. 直接ssh登录2222端口 
2. 通过samba文件共享访问 \\\\hostip\root, (mac是: smb://hostip/root)
3. 通过supervisor http管理服务: http://hostip:9001/
4. 通过probe管理tomcat: http://hostip:8080/probe
5. 通过phpmyadmin管理mysql http://hostip/phpmyadmin

*注1:所有密码都是root 123456*

*注2:如果是nat方式启动,请先用docker ps查看对应映射端口*

# 服务管理

 - 通过supervisor http管理服务: http://hostip:9001/
 - supervisorctl help
  查看帮助
 - supervisorctl status
  查看服务状态
 - supervisorctl start mycat
  启动服务
 - supervisorctl stop mycat
  停止服务
 - supervisorctl restart mycat
  重启服务

# Faq

1. 如何查看映射的端口:
docker ps

2. 如何查看ice sdk安装地址
```bash
rpm -ql ice-java
rpm -ql ice-php
rpm -ql ice-python
```

[1]: http://www.widuu.com/chinese_docker/
[2]: http://www.widuu.com/chinese_docker/installation/macos.html
[3]: http://www.widuu.com/chinese_docker/installation/windows.html