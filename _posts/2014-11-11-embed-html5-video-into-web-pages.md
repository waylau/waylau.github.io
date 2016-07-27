---
layout: post
title: 嵌入视频到 html5 页面
date: 2014-11-11 01:40
author: admin
comments: true
categories: [HTML5]
tags: [HTML5,video]
---

HTML5 的到来之前,每个视频的播放都需要通过 web 浏览器中通过第三方浏览器插件。最初我们亲眼目睹 RealPlayer，接着是 Windows Media Player 和 QuickTime, 现在是 Flash,这是目前主导的插件,播放大量的网络视频。

<!-- more -->

HTML5 引入了一个新的标准在 web 页面中嵌入和播放视频,无任何第三方插件,只需使用一个视频元素。 似乎是容易的,对吗? 但是你会发现没有这样的一个视频格式,适用于目前所有 HTML5 web 浏览器。

最大兼容浏览器 HTML5 视频,我们推荐你的视频编码转成如下HTML5视频格式，并在将它们嵌入到你的网页。

* H.264(Baseline) 在 mp4 容器。
* VP8/WEBM 在 webm 容器。
* Theroa/Vorbis 在 ogv 容器。

如果你只有一个 HTML5 视频文件嵌入到网页,您可以简单地链接到它的 src 属性

##Embed HTML5 video single source 嵌入单一来源视频

	<video src="samplevideo.webm" width="640" height="480" autoplay></video>

##Embed HTML5 video multiple sources 嵌入多来源视频

多个视频上面所提到的,是为了最大的 HTML5 视频浏览器兼容性，我们需要3个不同版本的视频。 您可以在web页面中嵌入多个 HTML5 视频在HTML5 视频元素,例子:

源代码详见下面：

	<video width="640" height="360" controls>
	<source src="http://www.html5videoplayer.net/videos/toystory.mp4" type="video/mp4" />
	<source src="http://www.html5videoplayer.net/videos/toystory.webm" type="video/webm" />
	<source src="http://www.html5videoplayer.net/videos/toystory.ogv" type="video/ogg" />
	</video>

结果如下 
<video width="640" height="360" controls>
<source src="http://www.html5videoplayer.net/videos/toystory.mp4" type="video/mp4" />
<source src="http://www.html5videoplayer.net/videos/toystory.webm" type="video/webm" />
<source src="http://www.html5videoplayer.net/videos/toystory.ogv" type="video/ogg" />
</video>

##HTML5 video with poster image 添加海报图片

现在,让我们去将海报图片添加到HTML5视频。

	<video poster="http://www.html5videoplayer.net/poster/toystory.jpg" width="640" height="360" controls>
	<source src="http://www.html5videoplayer.net/videos/toystory.mp4" type="video/mp4" />
	<source src="http://www.html5videoplayer.net/videos/toystory.webm" type="video/webm" />
	<source src="http://www.html5videoplayer.net/videos/toystory.ogv" type="video/ogg" />
	</video>

结果如下

<video poster="http://www.html5videoplayer.net/poster/toystory.jpg" width="640" height="360" controls>
<source src="http://www.html5videoplayer.net/videos/toystory.mp4" type="video/mp4" />
<source src="http://www.html5videoplayer.net/videos/toystory.webm" type="video/webm" />
<source src="http://www.html5videoplayer.net/videos/toystory.ogv" type="video/ogg" />
</video>

##HTML5 video autoplay 视频自动播放

如何播放 HTML5 视频? 只需添加 autoplay=”autoplay” 或简单的autoplay 到 video 标签即可。 如下

	<video autoplay="autoplay" poster="http://www.html5videoplayer.net/poster/toystory.jpg" width="640" height="360" controls>

或

	<video poster="http://www.html5videoplayer.net/poster/toystory.jpg" width="640" height="360" autoplay controls>

结果详见下表 

<video poster="http://www.html5videoplayer.net/poster/toystory.jpg" width="640" height="360" autoplay controls>


*参考：*[http://www.html5videoplayer.net/html5video/embed-html5-video-into-web-pages/](http://www.html5videoplayer.net/html5video/embed-html5-video-into-web-pages/)