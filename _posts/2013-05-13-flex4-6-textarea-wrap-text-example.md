---
layout: post
title: Flex4.6 textArea 文字换行 示例
date: 2013-05-13 01:39
author: admin
comments: true
categories: [Flex]
tags: [Flex,换行]
---
###flex4.6 textArea 文字换行 有 几种实现形式，

####法1：在text文字里面直接加 “&amp;#13;”

	<s:TextArea  text="这是个 TextArea 分行的例子&amp;#13; http://www.waylau.com "/>
 
####法2：采用了 TLF 标记 形式

	<s:TextArea  width="100%" height="340"   >
			<s:content >
				flex 4.6	<s:span color="#FF0000" fontWeight="bold">这是个 TextArea 分行的例子 </s:span><s:br/>
				<s:span color="#008800"> 采用了 TLF 标记 形式  </s:span> <s:br/>

				<s:a href="http://www.waylau.com" target="_blank">技术支持：www.waylau.com </s:a>.</s:content>
		</s:TextArea>