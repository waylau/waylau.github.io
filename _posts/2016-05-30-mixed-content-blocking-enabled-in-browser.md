---
layout: post
title: 在浏览器里启用混合内容
date: 2016-05-30 01:41
author: admin
comments: true
categories: [Web]
tags: [Web,Browser]
---

本文介绍了在浏览器里什么是混合内容，为什么要禁用混合内容，以及在各种浏览器里面如何启用混合内容。

<!-- more -->

## 什么是混合内容

HTTP 是一种从网页服务器将信息传递到您的浏览器的系统。HTTP 并不是安全的，所以当您访问一个通过 HTTP 提供的页面时，您的连接正面临窃听和中间人攻击的风险。大多数网站都使用 HTTP 提供服务，因为它们不涉及敏感信息的传输，因而无需采取加密措施。

当您访问一个完全通过 HTTPS 提供的页面时（例如银行服务），您将看到地址栏中有绿色挂锁图标（详见站点标识按钮）。这表示您的连接经过身份验证和加密，从而防止窃听和中间人攻击。

但是，如果您访问的 HTTPS 页面中含有 HTTP 内容，即使页面主体内容以 HTTPS 提供，HTTP 的部分仍然可被攻击者读取和篡改。当某个 HTTPS 页面含有 HTTP 内容时，我们称它为“混合内容（Mixed Content）”。这种页面仅被部分加密，尽管有些人认为这样是安全的，但事实并非如此。

## 为啥要禁用混合内容

上面其实已经讲到了。由于混合内容中 HTTP 的部分会对安全造成威胁，所以各大浏览器厂商都会禁用混合内容。

比如，你有一个 HTTPS 的应用，恰巧应用里面调用了一个第三方的 HTTP 接口，那么，默认情况下，对这个 HTTP 接口的请求，是会被浏览器拒绝的，告警如下(Chrome 浏览器)：

```
Mixed Content: The page at 'https://59.255.134.5/gov/' was loaded over HTTPS, but requested an insecure script 'http://59.255.134.3:28080/js/maps.js'. This request has been blocked; the content must be served over HTTPS.
```

## 如何在浏览器里面启用混合内容

上面的例子，如果第三方接口没有提供   HTTPS 服务，但你又想调用这个接口，那么你可以设置你的浏览器，来启用混合内容。

### Google Chrome 

方法1：

右上角盾牌，点击“加载不安全的脚本”。

缺点：每次重启浏览器，都需要重新点击设置，比较繁琐。

![](/images/post/20160530-mixed-content-google.png)
 


方法2：

Chrome 桌面快捷方式上右击，“属性”，在“目标”路径的后面添加
`--allow-running-insecure-content` 参数。打开浏览器后就会自动启动混合模式。

![](/images/post/20160530-mixed-content-google-2.png)



效果：

![](/images/post/20160530-mixed-content-google-3.png)

### IE

方法1：

浏览器对有风险的脚本进行了拦截，点击“显示所有内容”。

缺点：每次重启浏览器，都需要重新点击，比较繁琐。

![](/images/post/20160530-mixed-content-ie.png)


方法2：

对 IE 进行设置，启动显示混合内容。

![](/images/post/20160530-mixed-content-ie.png)

### Firefox 

方法1：

在地址栏单击盾牌图标 ，并在下拉菜单中单击“选项”按钮，在弹出的菜单中选择“暂时解除保护”。

缺点：每次重启浏览器，都需要重新点击设置，比较繁琐。

![](/images/post/20160530-mixed-content-firefox.png)

方法2：

在浏览器输入 about:config 切换到设置页面。
设置 `security.mixed_content.block_active_content` 选项值为 `false`

![](/images/post/20160530-mixed-content-firefox-2.png)

## 参考引用

* <https://support.mozilla.org/zh-CN/kb/%E4%B8%8D%E5%AE%89%E5%85%A8%E7%9A%84%E5%86%85%E5%AE%B9%E5%A6%82%E4%BD%95%E5%BD%B1%E5%93%8D%E6%88%91%E7%9A%84%E5%AE%89%E5%85%A8?redirectlocale=en-US&as=u&redirectslug=how-does-content-isnt-secure-affect-my-safety&utm_source=inproduct>