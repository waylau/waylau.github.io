---
layout: post
title: 在 Tomcat 中配置 SSL/TLS 以支持 HTTPS
date: 2016-03-18 01:41
author: admin
comments: true
categories: [Tomcat,SSL]
tags: [Tomcat,SSL,TLS,HTTPS]
---

本件详细介绍了如何通过几个简单步骤在 Tomcat 中配置 SSL/TLS 、使用 JDK 生成自签名的证书，最终实现在应用中支持 HTTPS 协议。

<!-- more -->

## 生产密钥和证书

Tomcat 目前只能操作 JKS、PKCS11、PKCS12 格式的密钥存储库。JKS 是 Java 标准的“Java 密钥存储库”格式，是通过 keytool 命令行工具创建的。该工具包含在 JDK 中。PKCS12 格式一种互联网标准，可以通过 OpenSSL 和 Microsoft 的 Key-Manager 来。

创建一个 keystore 文件保存服务器的私有密钥和自签名证书：

Windows：

	"%JAVA_HOME%\bin\keytool" -genkey -alias tomcat -keyalg RSA

UNIX：

	$JAVA_HOME/bin/keytool -genkey -alias tomcat -keyalg RSA

执行该命令后，首先会提示你提供 keystore 的密码。Tomcat 默认使用的密码是 `changeit`（全部字母都小写），当然你可以指定一个自定义密码（如果你愿意）。同样，你也需要将这个自定义密码在 `server.xml` 配置文件内进行指定，稍后再予以详述。

接下来会提示关于证书的一般信息，比如组织、联系人名称，等等。当用户试图在你的应用中访问一个安全页面时，该信息会显示给用户，所以一定要确保所提供的信息与用户所期望看到的内容保持一致。

最后，还需要输入密钥密码（key password），这个密码是这一证书（而不是存储在同一密码存储库文件中的其他证书）的专有密码。keytool 提示会告诉你，如果按下回车键，则自动使用密码存储库 keystore 的密码。当然，除了这个密码，你也可以自定义自己的密码。如果选择自定义密码，那么不要忘了在 `server.xml` 配置文件中指定这一密码。

下面是详细步骤：

```
C:\Users\admin>"%JAVA_HOME%\bin\keytool" -genkey -alias tomcat -keyalg RSA
输入密钥库口令:
再次输入新口令:
您的名字与姓氏是什么?
  [Unknown]:  waylau
您的组织单位名称是什么?
  [Unknown]:  waylau.com
您的组织名称是什么?
  [Unknown]:  waylau.com
您所在的城市或区域名称是什么?
  [Unknown]:  hangzhou
您所在的省/市/自治区名称是什么?
  [Unknown]:  zhejiang
该单位的双字母国家/地区代码是什么?
  [Unknown]:  china
CN=waylau, OU=waylau.com, O=waylau.com, L=hangzhou, ST=zhejiang, C=china是否正确
?
  [否]:  y

输入 <tomcat> 的密钥口令
        (如果和密钥库口令相同, 按回车):

```

如果操作全部正常，我们现在就会创建一个新的 JKS 密码存储库，该密码库包含一个自签名的证书。创建一个新的 JKS 密码存储库，该密码库包含一个自签名的证书。

该命令将在用户的主目录下创建一个新文件：`.keystore`。

![](http://99btgc01.info/uploads/2016/03/keystore.jpg)

要想指定一个不同的位置或文件名，可以在上述的 keytool 命令上添加 -keystore 参数，后跟到达 keystore 文件的完整路径名。你还需要把这个新位置指定到 `server.xml` 配置文件上，见后文介绍。例如：

Windows：

	"%JAVA_HOME%\bin\keytool" -genkey -alias tomcat -keyalg RSA -keystore \path\to\my\keystore
  
Unix：

	$JAVA_HOME/bin/keytool -genkey -alias tomcat -keyalg RSA -keystore /path/to/my/keystore    
  
  
## 修改配置

取消对 Tomcat 安装目录下 `/conf/server.xml` 中 “SSL HTTP/1.1 Connector” 一项的注释状态，并制定 keystore 的路径和密码：


```xml
 <Connector port="8443" protocol="org.apache.coyote.http11.Http11NioProtocol"
               maxThreads="150" SSLEnabled="true" scheme="https" secure="true"
			   keystoreFile="${user.home}/.keystore" keystorePass="changeit"
               clientAuth="false" sslProtocol="TLS" />
```

Tomcat 指定了 8443 端口为 HTTPS 访问端口。

*如果要隐藏端口号，就要把 Tomcat 的 HTTPS 端口设为 443*

若想把所有 HTTP 请求都转到 HTTPS 协议上，可以修改tomcat的conf下的web.xml，在
   <welcome-file-list> </welcome-file-list> 节点下方 添加如下：

```xml
<security-constraint>  
	<!-- Authorization setting for SSL --> 
	<web-resource-collection >  
		<web-resource-name >SSL</web-resource-name>  
		<url-pattern>/*</url-pattern>  
	</web-resource-collection>  
	<user-data-constraint>  
		<transport-guarantee>CONFIDENTIAL</transport-guarantee>  
	</user-data-constraint>  
</security-constraint>
```xml

其中，<url-pattern> 是配置文件过滤策略，比如，只对 .jsp 的请求自动转化为HTTPS ,配置如下：

```xml
<security-constraint>  
	<web-resource-collection >  
		<web-resource-name >SSL</web-resource-name>  
		<url-pattern>*.jsp</url-pattern>  
	</web-resource-collection>  
	<user-data-constraint>  
		<transport-guarantee>CONFIDENTIAL</transport-guarantee>  
	</user-data-constraint>  
</security-constraint>
 ```xml
 
在 <url-pattern> 中可以配置你希望自动转化的请求路径 /* 、  login.html 、 login.jsp 等等。

## 效果

首先，浏览器访问没有 HTTPS 支持的页面，<http://localhost:8080>

![](http://99btgc01.info/uploads/2016/03/keystore5.jpg)


接着，浏览器访问 HTTPS 的页面：<https://localhost:8443/>

![](http://99btgc01.info/uploads/2016/03/keystore3.jpg)


当用户首次访问你站点上的安全页面时，页面通常会提供给他一个对话框，包含证书相关细节（比如组织及联系方式等），并且询问他是否愿意承认该证书为有效证书，然后再进行下一步的事务。一些浏览器可能会提供一个选项，允许永远承认给出的证书的有效性，这样就不会在用户每次访问站点时打扰他们了。但有些浏览器不会提供这种选项。一旦用户承认了证书的有效性，那么在整个的浏览器会话期间，证书都被认为是有效的。

火狐浏览器提示

```
此连接不受信任

您想使用 Firefox 安全连接至 localhost:8443，但是我们无法确认此连接是否安全。

通常，当您尝试安全连接时，站点会出示受信任的凭据，以证明您访问的是正确的位置。然而现在，此网站的身份无法核实。
怎么办？

如果您过去曾连接到此网站且没有遇到该问题，那么此错误表示可能有人试图冒充该网站，因此您应该停止浏览。

localhost:8443 使用了无效的安全证书。

该证书因为其自签名而不被信任。
该证书仅对 waylau 有效。

（错误码： sec_error_unknown_issuer）

如果您了解现在所发生的一切，您可以让 Firefox 开始信任此站点的凭据。即便您信任此站点，这个错误也可能表明有人试图干涉您的连接。

不要随便添加例外，除非您知道并认同该网站不使用受信任标识的理由。
```


![](http://99btgc01.info/uploads/2016/03/keystore2.jpg)

而谷歌浏览器则提示如下：

![](http://99btgc01.info/uploads/2016/03/keystore4.jpg)



## 总结

虽然 SSL 协议的意图是尽可能有助于提供安全且高效的连接，但从性能角度来考虑，加密与解密是非茶馆耗费计算资源的，因此将整个 Web 应用都运行在 SSL 协议下是完全没有必要的，开发者需要挑选需要安全连接的页面。对于一个相当繁忙的网站来说，通常只会在特定页面上使用 SSL 协议，也就是可能交换敏感信息的页面，比如：登录页面、个人信息页面、购物车结账页面（可能会输入信用卡信息），等等。应用中的任何一个页面都可以通过加密套接字来请求访问，只需将页面地址的前缀 `http:` 换成 `https:` 即可。


## 参考引用

* <http://tomcat.apache.org/tomcat-9.0-doc/ssl-howto.html>