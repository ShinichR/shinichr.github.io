---
layout: post
title: "2015-11-15-mgo连接mongod使用"
description: "2015-11-15-mgo连接mongod使用"
categories: [mongo,nosql]
tags: []
---

###mongo安装

centos 安装指南，ubuntu的 apt-get 就ok了

	cd /etc/yum.repos.d/

	touch 10gen.repo 

32位:(把下面信息粘贴到 10gen.repo 文件里)

	[10gen]  
	
	name=10gen Repository  
	
	baseurl=http://downloads-distro.mongodb.org/repo/redhat/os/i686  
	
	gpgcheck=0 

64位:(把下面的信息粘贴到 10gen.repo 文件里)

	[10gen]  
	
	name=10gen Repository  
	
	baseurl=http://downloads-distro.mongodb.org/repo/redhat/os/x86_64  
	
	gpgcheck=0

* 1.mongodb 服务器端安装,运行下面命令

	yum install mongo-10gen-server  

可能需要你确认是否下载安装,输入y 并回车



* 2.mongodb 客户端安装,运行下面命令

		yum install mongo-10gen

同样可能需要你的确认,同上.

* 3.启动mongodb,运行下面的命令

		service mongod start
		chkconfig mongod on

* 4.如果你要启动客户端,直接运行命令

		mongo

###shell 使用

mongod先启动后，mongo敲入就进入了mongo shell了
默认有一个test的db，集合为foo
	
	db （显示当前db name）
	use test (使用db name 为test的)
	db.foo.insert({"lei":"haha"}) (foo集合插入文档)
	db.foo.find()     (会显示{ "_id" : ObjectId("xxxx"), "lei" : "haha" })


###mgo访问并update foo

	package main

	import (
		"fmt"
		"github.com/go-mgo/mgo"
		"github.com/go-mgo/mgo/bson"
	)
	
	const (
		MONGOSERVER = "127.0.0.1:27017"
	)
	
	func main() {
	
		session, err := mgo.Dial(MONGOSERVER) //连接数据库
		if err != nil {
			panic(err)
		}
		defer session.Close()
	
		session.SetMode(mgo.Monotonic, true)
	
		db := session.DB("test")  //数据库名称
		collection := db.C("foo") //如果该集合已经存在的话，则直接返回
		//*****集合中元素数目********
		countNum, err := collection.Count()
		if err != nil {
			panic(err)
		}
		fmt.Println("Things objects count: ", countNum)
	
		err = collection.Update(bson.M{"lei": "haha"}, bson.M{"$set": bson.M{"lei": "hahachange"}})
		if err != nil {
			fmt.Println("update err")
		}
	}


再次db.foo.find()会发现文档已经被修改为{ "_id" : ObjectId("xxxx"), "lei" : "hahachange" }
