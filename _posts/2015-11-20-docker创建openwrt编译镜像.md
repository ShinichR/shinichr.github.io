---
layout: post
title: "docker创建openwrt编译镜像"
description: "docker创建openwrt编译镜像"
categories: [linux,docker]
tags: []
---

### docker 安装

通过Docker源安装最新版本

要安装最新的 Docker 版本，首先需要安装 apt-transport-https 支持，之后通过添加源来安装。

	$ sudo apt-get install apt-transport-https
	$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9
	$ sudo bash -c "echo deb https://get.docker.io/ubuntu docker main > /etc/apt/sources.list.d/docker.list"
	$ sudo apt-get update
	$ sudo apt-get install lxc-docker


安装之后启动 Docker 服务。

	$ sudo service docker start

###创建openwrt镜像

* 获取最新ubuntu镜像

	sudo docker pull ubuntu:latest

串口相似打印如下

	Pulling repository ubuntu
	ab8e2728644c: Pulling dependent layers
	511136ea3c5a: Download complete
	5f0ffaa9455e: Download complete
	a300658979be: Download complete
	904483ae0c30: Download complete
	ffdaafd1ca50: Download complete
	d047ae21eeaf: Download complete

下载完成后可以用**sudo docker images** 查看你有的镜像

	REPOSITORY                      TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
	router                          openwrt_complie     e6cd8542b3f5        16 minutes 
	ubuntu                          latest              e9ae3c220b23        11 days ago         187.9 MB

接下来就要进入镜像去下载编译软件了，其实也可以写一个Dockerfile来弄

使用该镜像，创建一个容器，让其中运行 bash 应用

	$ sudo docker run -it ubuntu:latest /bin/bash
	root@fe7fc4bd8fc9:/#

进入之后就可以把它当做一个小型的ubuntu虚拟机了，只不过里面软件很少，要编译openwrt需要进行以下步骤：
	
创建用户

	useradd router
	passwd router

更新:

	apt-get update
	apt-get upgrade

安装软件：
	
	apt-get install gcc g++ binutils patch bzip2 flex bison make autoconf unzip sharutils subversion libncurses5-dev ncurses-term zlib1g-dev xz-utils gawk git python wget 


需要的基本都安装完毕了，接下来就需要svn co 一个openwrt代码进行编译即可，编译之前可以先commit这个镜像，后面作其他用
	
	sudo docker commit -m "add complie" -a "router complie" fe7fc4bd8fc9 ubuntu:router_compile

上面的 -m类似svn的commit加日志功能 e9ae3c220b23 就是容器应用的id，就是上面的**root@fe7fc4bd8fc9:/#**

ubuntu:router_compile就是一个新的tag，commit之后会在ubuntu:latest上面再加一层

接下来就可以使用**ubuntu:router_compile** 了

	sudo docker run -it ubuntu:router_compile /bin/bash

注意:

* 因为ubuntu:router_compile是基于ubuntu:latest的，所以如果删除**( docker rmi)** ubuntu:latest 是不可行的
* 如果是在编译后commit，那这个镜像可能会有3,4个G，commit会保存一定时间，所以不要以为它卡住了，我安装编译环境之前commit是600M+，编译之后就是4.8G了

 
###镜像打包

openwrt 编译环境也是挺难维护的，可以团队没人搞一个编译环境，编译出来的image后面会越来越差异化，所以用docker创建出来的镜像可以方便团队多人使用，就如最早的拷贝虚拟机一样，这个镜像可轻量化更方便。

	#打包
	sudo docker save router:complie > router_complie.tar

	#load镜像
	sudo docker load < router_complie.tar

团队的话还可以创建私有仓库管理镜像,暂不谈.