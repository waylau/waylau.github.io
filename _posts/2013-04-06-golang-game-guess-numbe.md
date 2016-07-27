---
layout: post
title: Golang 实现的一个小游戏--猜数字
date: 2013-04-06 01:17
author: admin
comments: true
categories: [Golang,Game]
tags: [Golang,Game]
---
随机生成一个数字，输入一个数字看是否匹对，匹配则结速，反之提示是大了还是小了，

代码如下：
	package main
	
	import (
		"bufio"
		"fmt"
		"math/rand"
		"os"
		"strconv"
		"time"
	)
	var (
		endNum int //设置生成数的范围
	)
	func main() {
		i := createRandomNumber(endNum)
		//fmt.Println("生成规定范围内的整数:", i)    //本句调试用
	
		fmt.Println("请输入整数,范围为:0-", endNum)
	
		flag := true
		reader := bufio.NewReader(os.Stdin)
	
		for flag {
			data, _, _ := reader.ReadLine()
	
			command, err := strconv.Atoi(string(data)) //string to int,并作输入格式判断
			if err != nil {
				fmt.Println("格式不对，请输入数字")
			} else {
	
				fmt.Println("你输入的数字:", command)
	
				if command == i {
					flag = false
					fmt.Println("恭喜你，答对了~")
				} else if command &lt; i {
					fmt.Println("你输入的数字小于生成的数字，别灰心！再来一次~")
				} else if command &gt; i {
					fmt.Println("你输入的数字大于生成的数字，别灰心！再来一次~")
				}
			}
		}
	}
	func init() {
		endNum = 10
	}
	
	//生成规定范围内的整数
	//设置起始数字范围，0开始,endNum截止
	func createRandomNumber(endNum int) int {
		r := rand.New(rand.NewSource(time.Now().UnixNano()))
		return r.Intn(endNum)
	}