---
layout: post
title: HTML &lt;label&gt; 提升&lt;input&gt;用户体验 
date: 2014-09-17 01:40
author: admin
comments: true
categories: [HTML]
tags: [HTML,label]
---

如果没有 &lt;label&gt; 当然也能显示 &lt;input&gt; 的本文，
比如

	<input type="radio" name="status" checked="true"  value="0"/>改造前
	<input type="radio" name="status" value="2"/>改造后
	<input type="checkbox" name="status1" value="3"/>www.waylau.com

点击组件上的文本不会有任何效果

<input type="radio" name="status" checked="true"  value="0"/>改造前
<input type="radio" name="status" value="2"/>改造后
<input type="checkbox" name="status1" value="3"/>www.waylau.com

但是，加上  &lt;label&gt; 后立马就不同了，可以点击文本来选中组件

	<label><input type="radio" name="status2" checked="true"  value="0"/>改造前</label>
	<label><input type="radio" name="status2" value="2"/>改造后</label>
	<label><input type="checkbox" name="status3" value="3"/>www.waylau.com</label>

<label><input type="radio" name="status2" checked="true"  value="0"/>改造前</label>
<label><input type="radio" name="status2" value="2"/>改造后</label>
<label><input type="checkbox" name="status3" value="3"/>www.waylau.com
</label>