---
layout: post
title: golang实现字符串逆序，不允许临时变量的原地逆序
date: 2013-04-06 01:15
author: admin
comments: true
categories: [Golang]
tags: [Golang]
---
	package main
	
	import (
		"fmt"
	)
	
	var (
		s1    string
	)
	func main() {
		fmt.Println(s1)
		fmt.Println(converString(s1))
	}
	func init() {
		s1 = "我爱你waylau.com"
	}
	func reverseString(s string) string {
		str := []rune(s)
	
		for i, j := 0, len(str)-1; i &lt; j; i, j = i+1, j-1 {
			str[i], str[j] = str[j], str[i]
		}
	
		return string(str)
	}

后台输入为

	我爱你waylau.com
	moc.ualyaw你爱我
