---
layout: post
title: Flex   addChild与addElement的区别
date: 2011-01-01 00:22
author: admin
comments: true
categories: [Flex]
tags: [addChild,addElement]
---
* addChild()接受的是一个DisplayObject对象，addElement()接受的是一个IVisualElement对象，IVisualElement是在Flex 4组件中开始应用的，所以Flex 3当中的组件没有实现IVisualElement接口，再所以Flex 3当中的容器用的是addChild()。
* 而Flex 4中的spark组件已经实现了IVisualElement接口，所以可以用addElement()，在Flex 4中使用Flex 3的组件和其它没有实现IVisualElement接口的组件时，可以使用addElement(UIComponent(flex_3_comp))，就相当于用UIComponent来做一下包装。 
* 在Flex 4容器中，其实内建了一个Group类型的contentGroup用来存放容器中的元素，而正是Group类定义了addElement()，所以Flex 4容器不用addChild()，而是用addElement()
* 如果是spark(如s:Group)容器，用addElement。如果是hola(如mx:Hbox)容器，就用addChild。

参考：<http://help.adobe.com/en_US/flex/using/WS2db454920e96a9e51e63e3d11c0bf62b90-7fe9.html>
