---
layout: post
title: "golang sync package 学习"
description: "golang sync package 学习"
category: "golang"
tags: []
---


### 1: sync.WaitGroup

sync.WaitGroup只有3个方法:

* Add()
* done()
* Wait()

其中Done()是Add(-1)的别名。简单的来说，使用Add()添加计数，Done()减掉一个计数，计数不为0, 阻塞Wait()的运行。
package main

	import (
		"fmt"
		"sync"
		"time"
	)
	
	func main() {
		var wg sync.WaitGroup
	
		for i := 0; i < 5; i = i + 1 {
			wg.Add(1)
			go func(n int) {
				// defer wg.Done()
				defer wg.Add(-1)
				EchoNumber(n)
			}(i)
		}
	
		wg.Wait()
	}
	
	func EchoNumber(i int) {
		time.Sleep(3e9)
		fmt.Println(i)
	}

输出结果：
###
	0
	1
	2
	3
	4
###

### 2: sync.Once
Once是多线程开始之前先执行，并且仅执行一次的方法。可以考虑用来做一些初始化操作。对socket close可以使用Once来执行close操作，防止close多次

	package main
	
	import (
		"fmt"
		"sync"
		"time"
	)
	
	var counter int = 0
	
	func main() {
		var once sync.Once
		onceBody := func() {
			time.Sleep(3e9)
			fmt.Println("Only once")
		}
		done := make(chan bool)
		for i := 0; i < 10; i++ {
			j := i
			go func(int) {
				once.Do(onceBody)
				fmt.Println(j)
				done <- true
			}(j)
		}
		for i := 0; i < 10; i++ {
			<-done
		}
	}
