---
layout: post
title: Flex4 Label文本换行
date: 2014-03-12 02:57
author: admin
comments: true
categories: [Flex]
---
图标中的文本如果太长就显示不全了。
<img alt="图片" src="http://b.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=2ae08ac641a98226bcc12b22bab9c83c/b64543a98226cffcdf33b701bb014a90f603ea6c.jpg?referer=cbf55f5af0d3572c3ff5a9ec5622&amp;x=.jpg" data-img-idx="0" data-src="http://b.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=2ae08ac641a98226bcc12b22bab9c83c/b64543a98226cffcdf33b701bb014a90f603ea6c.jpg?referer=cbf55f5af0d3572c3ff5a9ec5622&amp;x=.jpg" />
查API ，
Label继承了TextBase，而 TextBase有maxDisplayedLines属性，表述如下：
确定是否截断文本以及在何处截断文本的整数。
<div>截断文本意味着使用截断指示符（如 "..."）替换超额文本。截断指示符与区域设置相关；它是由 "core" 资源包中的 "truncationIndicator" 资源指定的。</div>
<div>如果值为 0，则不会发生截断。相反，如果文本不在组件的界限内，则将只是剪辑文本。</div>
<div>如果值为正整数，则会根据需要截断文本以将行数减少至此整数。</div>
<div>如果值为 -1，则会截断该文本以显示将完全放在组件的高度内的行。</div>
<div>仅当 lineBreak 样式为 "toFit" 时，才执行截断；如果 lineBreak 为 "explicit"，则会忽略此属性的值。</div>
<div>默认值为 0。</div>
<div>
将 maxDisplayedLines 设为2，解决
<img alt="图片" src="http://h.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=92c3db4e79899e517c8e3a11729ca80e/e7cd7b899e510fb3a81d7f24db33c895d1430c78.jpg?referer=1768c59a8418367af49e4aed6c17&amp;x=.jpg" data-img-idx="1" data-src="http://h.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=92c3db4e79899e517c8e3a11729ca80e/e7cd7b899e510fb3a81d7f24db33c895d1430c78.jpg?referer=1768c59a8418367af49e4aed6c17&amp;x=.jpg" /></div>
