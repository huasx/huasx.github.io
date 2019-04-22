---
layout:     post
title:      Go Channel Goroutine
subtitle:   channel goroutine
            
date:       2018-08-30
author:     Ricky
header-img: img/post-bg-e2e-ux.jpg
catalog: true
tags:
    - GO
    - Channel
    - Routine
---
# 前言

>写Golang也有一段时间了，一直写的都是一些数据处理，例如struct转json，json转map之类，几乎没有涉及到并发类的东西，但是高效简洁的并发才是Go语言的精髓所有，如果没有使用到
这个功能，那等于没有使用Go
>今天就简单看下go语言里面的channel 和 goroutine

```Golang
    package main
    
    import (
    	"fmt"
    	"strconv"
    	"sync"
    )
    
    func main() {
    	var group sync.WaitGroup
    	emps := 20
    	ch := make(chan string, emps)
    	ch1 := make(chan struct{})
    
    	//读取ch的数据，
    	go func() {
    		for v := range ch {
    
    			fmt.Println("pppppppppppp==========", v)
    		}
    		ch1 <- struct {}{} //执行完成之后，写入一个信号
    	}()
    
    
    
    	for e := 0; e < emps; e++ {
    		fmt.Println("for e", e)
    		group.Add(1)
    		go func() {
    			defer group.Done()
    			fmt.Println("go for e", e)
    			//time.Sleep(time.Duration(rand.Intn(200)) * time.Millisecond)
    			ch <- strconv.Itoa(e)
    		}()
    	}
    
    	group.Wait()
    	close(ch)
    
    	<-ch1 //主程序读取到信号之后 退出
    }

```

# 解读
>上面这段代码，首先定义了一个叫做ch的有缓冲channel，有缓冲的通道里面最多只能存20个信号，如果没有被读取，那么将无法继续存入
>主程序在开始的时候，就启动了一个goroutine读取ch的信号
>下面的for循环，是开启了20个并发，每个并发都往ch通道里面传递了一个数据
>主程序必须等待所有的并发都执行完成，所有有一个group的wait，每启动一个goroutine就add一个，同时，每一个goroutine执行完成，都需要
调用group.Done()来告知主程序我已经执行完成，最后要关闭这个ch通道
>ch通道关闭之后，上面的for range 就不会在继续读取，但是，这里也是一个goroutine，通道关闭之后，自然不会继续循环，但是主程序并不知道这个goroutine的
任务何时才执行完成，所以需要等这个goroutine执行完成之后，再写一个通道ch1 主程序在接收到这个信号之后再退出

