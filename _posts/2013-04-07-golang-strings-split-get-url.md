---
layout: post
title: Golang strings.Split获取字符串中的url/域名的简易方法
date: 2013-04-07 01:18
author: admin
comments: true
categories: [Golang]
tags: [Golang,Split]
---
	package main
	
	import (
		"fmt"
		"strings"
	)
	
	func main() {
		fmt.Println("Hello World!")
	
		a := "golang strings.Split获取字符串中的url/域名的简易方法http://www.waylau.com/golang-strings-split-get-url/"
		a1 := strings.Split(a, "//")[1]
		a2 := strings.Split(a1, "/")[0]
	
		fmt.Println(a1) //输出为：www.waylau.com/golang-strings-split-get-url/
		fmt.Println(a2) //输出为：www.waylau.com
	}

