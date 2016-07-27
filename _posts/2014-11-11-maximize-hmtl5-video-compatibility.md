---
layout: post
title: 最大化兼容 html5 视频
date: 2014-11-11 01:41
author: admin
comments: true
categories: [HTML5]
tags: [HTML5,video]
---

HTML5 在 web 页面使用 video 元素呈现视频。但 HTML5 视频并不是最终的解决方案,它不能在所有的浏览器工作。你知道 HTML5 视频真的在网上工作吗? 你担心 HMTL5 视频兼容性吗? 看看这篇文章的建议和解决方案如何最大化 HMTL5 视频兼容性。

<!-- more -->

正如我们所知, web 页面上显示一个视频或电影是没有行业标准的。除了浏览器自身,大多数网站显示视频通过浏览器插件,比如 Flash player。 HTML5 包含一个 video 元素将视频嵌入到web页面。W3C因此引入了在互联网上显示视频的新标准 HTML5 video 元素。 使用HTML5视频没有限制视频编解码器,音频编解码器,或容器格式。为了获得最大的 HMTL5 视频兼容性,可以链接到多个视频文件在一个 video 元素里,用户的浏览器将选择第一个视频文件,它可以自动播放。

这是最近的HTML5视频:

* Internet Explorer 8 没有 HTML5 视频支持,但几乎所有的 IE 用户都有 Adobe Flash 插件。 在本章的后面,我将向您展示如何使用 HTML5视频优雅地转回到 Flash。
* Internet Explorer 9 支持所有的 H.264 视频和 AAC 或 MP3 音频在一个 MP4 容器里。 它也将播放 WebM 视频如果你安装一个第三方的编解码器,假如默认没有在任何版本的 Windows 安装的话。 IE9 不支持其他第三方编解码器(不像 Safari 可以支持播放 QuickTime 可播放的格式)。
* Mozilla Firefox(3.5及以后)支持 Theora 视频和 Vorbis音频在一个 Ogg 容器里。火狐4还支持 WebM。
* Google Chrome(3.0及以后)支持 Theora 视频和 Vorbis音频在一个 Ogg 容器里。 Google Chrome 6.0 还支持 WebM。
* Opera(10.5及以后)支持 Theora 视频和 Vorbis音频在一个 Ogg 容器里。 Opera 10.60 还支持 WebM。
* Safari 在 mac 电脑和 Windows 电脑(3.0及以后)将支持任何QuickTime 所支持的格式。 理论上,您可能需要用户安装第三方QuickTime 插件。 在实践中,很少有用户会这样做。 所以你剩下的格式将会是 QuickTime 开箱即用的支持。 这是一长列表,但不包括WebM,Theora,Vorbis,或 Ogg 容器。 然而, QuickTime 提供支持 H.264 视频(主要配置文件)和 AAC 音频在 MP4 容器里。
* Adobe Flash(9.0.60.184后来)支持  H.264 视频(所有配置文件)和AAC 音频(所有资料)在 MP4 容器里。
手机像苹果的 iPhone 和谷歌的 Android 手机支持 H.264 视频(基线配置)和 AAC 音频(“低复杂度”)在 MP4 容器里。

你会发现没有单一的容器和编解码器能在所有 HTML5 浏览器工作。 由于许可和其他问题,在不久的将来这是不可能改变。

你可以参考这篇文章: [HTML5视频浏览器兼容性问题和解决方案](http://www.html5videoplayer.net/html5video/html5-video-browser-compatibility/) !

对于最大的兼容性,可以遵循你的HTML5视频工作波形工作流。

1. 让你的视频的一个版本,使用 WebM (VP8 + Vorbis) 。

2. 创建第二个版本,使用 H.264 视频(基线)和 AAC 音频(“低复杂度”)在 MP4 容器里。

3. 第三个版本,使用 Theora 视频和 Vorbis音频在一个 Ogg 容器里。

4. 链接到所有三个视频文件在一个 video 元素里。

```html

	<video controls="controls" poster="http://www.html5videoplayer.net/poster/toystory.jpg" width="640" height="360">
		<source src="http://www.html5videoplayer.net/videos/toystory.mp4" type="video/mp4" />
		<source src="http://www.html5videoplayer.net/videos/toystory.ogv" type="video/ogg" />
		<source src="http://www.html5videoplayer.net/videos/toystory.webm" type="video/webm" />
	</video>
```

结果如下：

<video controls="controls" poster="http://www.html5videoplayer.net/poster/toystory.jpg" width="640" height="360">
<source src="http://www.html5videoplayer.net/videos/toystory.mp4" type="video/mp4" />
<source src="http://www.html5videoplayer.net/videos/toystory.ogv" type="video/ogg" />
<source src="http://www.html5videoplayer.net/videos/toystory.webm" type="video/webm" />
</video>

最大化 HTML5 视频兼容所有浏览器,您也可以 嵌套 Flash 的 object 元素从 video 元素中 回退。 你甚至可以包括不同的 HTML5 本地视频的下载链接。

通过结合 HTML5 和 Flash,你应该能够得到的最大 HMTL5 视频兼容性在几乎所有的浏览器和设备中。 不要忘记,即使所有这些不工作,你仍然可以直接下载链接为HTML5的本地视频。

*参考：*[http://www.html5videoplayer.net/html5video/maximize-hmtl5-video-compatibility/](http://www.html5videoplayer.net/html5video/maximize-hmtl5-video-compatibility/)
