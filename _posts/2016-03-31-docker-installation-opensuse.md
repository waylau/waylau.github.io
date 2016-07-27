---
layout: post
title: Docker 在 openSUSE 下的安装、使用
date: 2016-03-31 02:41
author: admin
comments: true
categories: [Docker,openSUSE]
tags: [Docker,openSUSE,安装,容器,VirtualBox]
---
本文介绍了 Docker 在 openSUSE 环境下的详细安装过程，并尝试运行一个简单 Spring Boot 的 image 来感受下 Docker 的风采。

<!-- more -->

## 什么是 Docker

![](https://docs.docker.com/dist/assets/images/logo.png)

Docker 是为开发人员和系统管理员用于构建、发布、并运行分布式应用程序的开放式平台。该平台由 Docker 引擎（一个便携、轻巧的运行时和打包工具） 和 Docker Hub （一个共享应用程序和自动化工作流的云服务）等组成。Docker 可以使应用程序从组件迅速组装并消除了开发、质量保证和生产环境之间的摩擦问题。这样一来，IT部门可以更快地发布，而这些应用程序不管是运行在笔记本电脑、数据中心的虚拟机，还是任何的云，其运行过程和结果都是一致的。

更多有关 Docker 的介绍，可以参阅《[简述 Docker](http://waylau.com/ahout-docker/)》 一文。

## 前置条件

* 64-bit 系统
* kernel 3.10+
* openSUSE 13.2+。本例使用的是 [openSUSE Leap 42.1](http://software.opensuse.org/421/zh_CN)（如果你没有 openSUSE 系统，可以尝试在虚拟机里面装个 openSUSE 系统，可以参阅《[Oracle VM VirtualBox 安装和使用](http://waylau.com/about-oracle-vm-virtualbox/)》《[CentOS 7 安装、配置](http://waylau.com/centos-7-installation-and-configuration/)》）

使用 `uname -r` 检查 kernel 版本

    $ uname -r
    4.1.12-1-default

建议你使用最新的系统，以为一直的 bug 都会在新的 kernel 发布中修复。

## 安装

openSUSE 13.2 起，自带了仓库，所以无需添加

### 安装 openSUSE 国内镜像源

网络原因，访问 openSUSE 速度慢且不稳定，建议采用国内镜像库，下面例子是使用的 aliyun

```
zypper addrepo -f http://mirrors.aliyun.com/opensuse/update/leap/42.1/oss  openSUSE-42.1-Update-Oss

zypper addrepo -f http://mirrors.aliyun.com/opensuse/update/leap/42.1/non-oss/ openSUSE-42.1-Update-Non-Oss

zypper addrepo -f http://mirrors.aliyun.com/opensuse/distribution/leap/42.1/repo/oss/ openSUSE-42.1-Oss

zypper addrepo -f http://mirrors.aliyun.com/opensuse/distribution/leap/42.1/repo/non-oss/  openSUSE-42.1-Non-Oss

zypper addrepo -f http://mirrors.aliyun.com/packman/openSUSE_Leap_42.1/ aliyun-packman
```
 


### 安装 Docker 包

    $ sudo zypper in docker
    

输出如下：

```
linux-aihn:~ # zypper in docker
Retrieving repository 'openSUSE-Leap-42.1-Update' metadata --------------------------[|]

Warning: Digest verification failed for file '7256e9794decb02046412d2dc8ab5fa5a17875c1303f31663094da5ede29e933-app-icons.tar.gz'
[/var/cache/zypp/raw/repo-updateQVITjj/repodata/7256e9794decb02046412d2dc8ab5fa5a17875c1303f31663094da5ede29e933-app-icons.tar.gz]

  expected 7256e9794decb02046412d2dc8ab5fa5a17875c1303f31663094da5ede29e933
  but got  a6be6f0868adbffa79d87856f6f3de4317a1e5c3f923be66eed899c4b559850b

Accepting packages with wrong checksums can lead to a corrupted system and in extreme cases even to a system compromise.

However if you made certain that the file with checksum 'a6be..' is secure, correct
and should be used within this operation, enter the first 4 characters of the checksum
to unblock using this file on your own risk. Empty input will discard the file.

Unblock or discard? [a6be/? shows all options] (discard): discard
Retrieving repository 'openSUSE-Leap-42.1-Update' metadata ......................[error]
Repository 'openSUSE-Leap-42.1-Update' is invalid.
[repo-update|http://download.opensuse.org/update/leap/42.1/oss/] Valid metadata not found at specified URL
Please check if the URIs defined for this repository are pointing to a valid repository.
Skipping repository 'openSUSE-Leap-42.1-Update' because of the above error.
Loading repository data...
Reading installed packages...
Resolving package dependencies...

The following 6 NEW packages are going to be installed:
  bridge-utils docker git-core git-gui gitk perl-Error

6 new packages to install.
Overall download size: 10.6 MiB. Already cached: 0 B. After the operation, additional
50.7 MiB will be used.
Continue? [y/n/? shows all options] (y): y
Retrieving package perl-Error-0.17021-3.2.noarch   (1/6),  28.3 KiB ( 49.8 KiB unpacked)
Retrieving: perl-Error-0.17021-3.2.noarch.rpm ....................................[done]
Retrieving package bridge-utils-1.5-21.3.x86_64    (2/6),  32.0 KiB ( 66.0 KiB unpacked)
Retrieving: bridge-utils-1.5-21.3.x86_64.rpm .....................................[done]
Retrieving package git-core-2.6.2-3.1.x86_64       (3/6),   3.9 MiB ( 25.7 MiB unpacked)
Retrieving: git-core-2.6.2-3.1.x86_64.rpm ..........................[done (267.4 KiB/s)]
Retrieving package gitk-2.6.2-3.1.x86_64           (4/6), 217.2 KiB (705.6 KiB unpacked)
Retrieving: gitk-2.6.2-3.1.x86_64.rpm ............................................[done]
Retrieving package git-gui-2.6.2-3.1.x86_64        (5/6), 296.1 KiB (  1.3 MiB unpacked)
Retrieving: git-gui-2.6.2-3.1.x86_64.rpm .........................................[done]
Retrieving package docker-1.9.1-13.1.x86_64        (6/6),   6.2 MiB ( 22.9 MiB unpacked)
Retrieving: docker-1.9.1-13.1.x86_64.rpm ...........................[done (272.8 KiB/s)]
Checking for file conflicts: .....................................................[done]
(1/6) Installing: perl-Error-0.17021-3.2 .........................................[done]
(2/6) Installing: bridge-utils-1.5-21.3 ..........................................[done]
(3/6) Installing: git-core-2.6.2-3.1 .............................................[done]
(4/6) Installing: gitk-2.6.2-3.1 .................................................[done]
(5/6) Installing: git-gui-2.6.2-3.1 ..............................................[done]
(6/6) Installing: docker-1.9.1-13.1 ..............................................[done]
Additional rpm output:
creating group docker...
Updating /etc/sysconfig/docker...
```

## 启动 docker 守护进程

    $ sudo  systemctl start docker.service

## 配置让 docker 服务随系统自动启动

    $ sudo systemctl enable docker.service
    
docker 会创建一个以`docker`命名的用户组，添加用户到这个用户组

    sudo /usr/sbin/usermod -a -G docker <username>

## 卸载 docker 

卸载 Docker 包:

    $ sudo zypper rm docker
    
上述命令不会卸载 images、containers、volumes, 或者用户创建在主机的配置文件。若想卸载干净上述所有 images、containers、volumes,  执行:

    $ rm -rf /var/lib/docker

用户创建在主机的配置文件需手动删除。

## 拉取 image 到本地

可以将 Docker Bub 存储的 image 拉取到本地，执行 

    docker pull waylau/docker-spring-boot
    

输出如下：

```
linux-aihn:~ # docker pull waylau/docker-spring-boot
Using default tag: latest
Pulling repository docker.io/waylau/docker-spring-boot
3694bcee2712: Download complete 
a2e9c1527ca5: Download complete 
20f4b5a54fc6: Download complete 
7a220626bc9e: Download complete 
620bbe9b8954: Download complete 
a0b5e5298dc5: Download complete 
2f8558117280: Download complete 
d2ee7992d027: Download complete 
Status: Downloaded newer image for waylau/docker-spring-boot:latest
docker.io/waylau/docker-spring-boot: this image was pulled from a legacy registry.  Important: This registry version will not be supported in future versions of docker.
```
## 运行 image 本地镜像

执行：

    docker run -p 8080:8080 -t waylau/docker-spring-boot

最后成功的界面如下：

```
linux-aihn:~ # docker run -p 8080:8080 -t waylau/docker-spring-boot

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v1.3.3.RELEASE)

2016-03-31 02:33:53.043  INFO 1 --- [           main] c.waylau.docker_spring_boot.Application  : Starting Application v1.0.0 on 5dfa67f23534 with PID 1 (/app.jar started by root in /)
2016-03-31 02:33:53.096  INFO 1 --- [           main] c.waylau.docker_spring_boot.Application  : No active profile set, falling back to default profiles: default
2016-03-31 02:33:55.050  INFO 1 --- [           main] ationConfigEmbeddedWebApplicationContext : Refreshing org.springframework.boot.context.embedded.AnnotationConfigEmbeddedWebApplicationContext@64dac4db: startup date [Thu Mar 31 02:33:54 GMT 2016]; root of context hierarchy
2016-03-31 02:34:03.254  INFO 1 --- [           main] o.s.b.f.s.DefaultListableBeanFactory     : Overriding bean definition for bean 'beanNameViewResolver' with a different definition: replacing [Root bean: class [null]; scope=; abstract=false; lazyInit=false; autowireMode=3; dependencyCheck=0; autowireCandidate=true; primary=false; factoryBeanName=org.springframework.boot.autoconfigure.web.ErrorMvcAutoConfiguration$WhitelabelErrorViewConfiguration; factoryMethodName=beanNameViewResolver; initMethodName=null; destroyMethodName=(inferred); defined in class path resource [org/springframework/boot/autoconfigure/web/ErrorMvcAutoConfiguration$WhitelabelErrorViewConfiguration.class]] with [Root bean: class [null]; scope=; abstract=false; lazyInit=false; autowireMode=3; dependencyCheck=0; autowireCandidate=true; primary=false; factoryBeanName=org.springframework.boot.autoconfigure.web.WebMvcAutoConfiguration$WebMvcAutoConfigurationAdapter; factoryMethodName=beanNameViewResolver; initMethodName=null; destroyMethodName=(inferred); defined in class path resource [org/springframework/boot/autoconfigure/web/WebMvcAutoConfiguration$WebMvcAutoConfigurationAdapter.class]]
2016-03-31 02:34:11.228  INFO 1 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat initialized with port(s): 8080 (http)
2016-03-31 02:34:11.424  INFO 1 --- [           main] o.apache.catalina.core.StandardService   : Starting service Tomcat
2016-03-31 02:34:11.440  INFO 1 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet Engine: Apache Tomcat/8.0.32
2016-03-31 02:34:12.574  INFO 1 --- [ost-startStop-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2016-03-31 02:34:12.575  INFO 1 --- [ost-startStop-1] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 17670 ms
2016-03-31 02:34:16.786  INFO 1 --- [ost-startStop-1] o.s.b.c.e.ServletRegistrationBean        : Mapping servlet: 'dispatcherServlet' to [/]
2016-03-31 02:34:16.839  INFO 1 --- [ost-startStop-1] o.s.b.c.embedded.FilterRegistrationBean  : Mapping filter: 'characterEncodingFilter' to: [/*]
2016-03-31 02:34:16.842  INFO 1 --- [ost-startStop-1] o.s.b.c.embedded.FilterRegistrationBean  : Mapping filter: 'hiddenHttpMethodFilter' to: [/*]
2016-03-31 02:34:16.867  INFO 1 --- [ost-startStop-1] o.s.b.c.embedded.FilterRegistrationBean  : Mapping filter: 'httpPutFormContentFilter' to: [/*]
2016-03-31 02:34:16.868  INFO 1 --- [ost-startStop-1] o.s.b.c.embedded.FilterRegistrationBean  : Mapping filter: 'requestContextFilter' to: [/*]
2016-03-31 02:34:20.432  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerAdapter : Looking for @ControllerAdvice: org.springframework.boot.context.embedded.AnnotationConfigEmbeddedWebApplicationContext@64dac4db: startup date [Thu Mar 31 02:33:54 GMT 2016]; root of context hierarchy
2016-03-31 02:34:21.405  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/]}" onto public java.lang.String com.waylau.docker_spring_boot.Application.home()
2016-03-31 02:34:21.451  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error],produces=[text/html]}" onto public org.springframework.web.servlet.ModelAndView org.springframework.boot.autoconfigure.web.BasicErrorController.errorHtml(javax.servlet.http.HttpServletRequest,javax.servlet.http.HttpServletResponse)
2016-03-31 02:34:21.457  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error]}" onto public org.springframework.http.ResponseEntity<java.util.Map<java.lang.String, java.lang.Object>> org.springframework.boot.autoconfigure.web.BasicErrorController.error(javax.servlet.http.HttpServletRequest)
2016-03-31 02:34:22.033  INFO 1 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2016-03-31 02:34:22.037  INFO 1 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2016-03-31 02:34:22.658  INFO 1 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**/favicon.ico] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2016-03-31 02:34:23.787  INFO 1 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2016-03-31 02:34:24.497  INFO 1 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat started on port(s): 8080 (http)
2016-03-31 02:34:24.567  INFO 1 --- [           main] c.waylau.docker_spring_boot.Application  : Started Application in 39.766 seconds (JVM running for 46.766)
```
### 访问项目

如果程序正确运行，浏览器访问 <http://localhost:8080/>，可以看到页面 “Hello Docker World.” 字样。

## 查看 docker image

可以看到 pull 回来的这个 image 的一个情况、大小等信息。

```
linux-aihn:~ # docker images
REPOSITORY                  TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
waylau/docker-spring-boot   latest              3694bcee2712        10 days ago         180.8 MB
```

## 源码

获取项目源码，
<https://github.com/waylau/docker-demos> 中的 `samples/spring-boot`

获取项目镜像， <https://hub.docker.com/r/waylau/docker-spring-boot/>
    
    
## 参考

* <https://docs.docker.com/engine/installation/linux/SUSE/>
* 《[Oracle VM VirtualBox 安装和使用](http://waylau.com/about-oracle-vm-virtualbox/)》
* 《[CentOS 7 安装、配置](http://waylau.com/centos-7-installation-and-configuration/)》