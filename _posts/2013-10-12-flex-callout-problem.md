---
layout: post
title: 解决 Callout位置不更新的问题
date: 2013-10-12 02:49
author: admin
comments: true
categories: [Flex]
tags: [Callout, Flex]
---
<div>spark 手机 Callout组件可以实现子组件的分类</div>
<div>如下效果</div>
<div><img class="alignnone" alt="" src="http://e.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=a1757e5b9058d109c0e3a9b7e163bd82/574e9258d109b3dee0a3f94dcebf6c81800a4cf2.jpg?referer=bfefebe8fe039245f8a2d43fac90&amp;x=.jpg" width="360" height="394" /></div>
<div></div>
<div>但是在使用过程中发现Callout位置老是固定不变，</div>
<div>虽然里面的数据变了。</div>
<div>removeAllElements();</div>
<div>close();</div>
<div>也不好使。</div>
<div>查API 找到updatePopUpPosition();</div>
<div>需要在 close();之后使用，搞掂！</div>
