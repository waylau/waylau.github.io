---
layout: post
title: 使用maven编译Java项目
date: 2014-07-17 07:08
author: admin
comments: true
categories: [Maven]
tags: [Maven,Java]
---
 
#综述
本文演示了用Maven编译Java项目 

#需要
* 时间：15分钟
* 文本编辑器或者IDE
* JDK 6 或者更高版本
 
#创建项目
本例主要为了展示Maven，所以Java的项目力求简单。

#创建项目结构
选择一个项目目录，在 *nix系统上使用下面语句

	mkdir -p src/main/java/hello

window下使用命令

	mkdir src\main\java\hello

创建如下结构：

	└── src
    	└── main
        	└── java
            	└── hello

在`src/main/java/hello`目录下创建Java文件`HelloWorld.java`和 `Greeter.java`

`src/main/java/hello/HelloWorld.java`

	package hello;
	
	public class HelloWorld {
	    public static void main(String[] args) {
	        Greeter greeter = new Greeter();
	        System.out.println(greeter.sayHello());
	    }
	}


`src/main/java/hello/Greeter.java`

	package hello;
	
	public class Greeter {
	    public String sayHello() {
	        return "Hello world!";
	    }
	}

现在项目完成，可以用Maven编译了。有关Maven的安装，可以参考[Apache Maven 3.1.0 安装、部署、使用](http://www.waylau.com/apache-maven-3-1-0-installation-deployment-and-use/)

#定义简单的Maven编译
首先，在项目的根目录下创建一个Maven项目定义文件`pom.xml`,该文件主要是说明项目的名称、版本和依赖库

`pom.xml`

	<?xml version="1.0" encoding="UTF-8"?>
	<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
	    <modelVersion>4.0.0</modelVersion>
	    <groupId>org.springframework</groupId>
	    <artifactId>gs-maven</artifactId>
	    <packaging>jar</packaging>
	    <version>0.1.0</version>
	
	    <build>
	        <plugins>
	            <plugin>
	                <groupId>org.apache.maven.plugins</groupId>
	                <artifactId>maven-shade-plugin</artifactId>
	                <version>2.1</version>
	                <executions>
	                    <execution>
	                        <phase>package</phase>
	                        <goals>
	                            <goal>shade</goal>
	                        </goals>
	                        <configuration>
	                            <transformers>
	                                <transformer
	                                    implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
	                                    <mainClass>hello.HelloWorld</mainClass>
	                                </transformer>
	                            </transformers>
	                        </configuration>
	                    </execution>
	                </executions>
	            </plugin>
	        </plugins>
	    </build>
	</project>


除了&lt;packaging&gt; 元素可选外，其他的元素是构成`pom.xml` 文件的最基本的元素了。它包括以下几个项目的配置：

* &lt;modelVersion&gt; ：POM 模块版本(通常是4.0.0).
* &lt;groupId&gt; ：项目所属的组织编号，通常用域名
* &lt;artifactId&gt; 项目的名称(比如，JAR或者WAR的名称)
* &lt;version&gt; 项目编译的版本号
* &lt;packaging&gt; 项目打包形式，jar或者war

#编译Java代码
运行下面语句编译

	mvn compile

编译完成的`.class`文件将会出现在`target/classes`目录下.如下图
![编译Java代码](http://e.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=0fd2eacb8544ebf86971643ae9c2a617/0d338744ebf81a4c35bd3f01d52a6059242da66f.jpg?referer=b410ea0c33adcbef58234a360225&x=.jpg)

![class](http://h.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=19799d719c510fb37c197792e908b9a8/7a899e510fb30f24bd133fcbca95d143ac4b0369.jpg?referer=07ad60e0347adab464c72f736d28&x=.jpg)

运行项目：

	mvn exec:java -Dexec.mainClass="hello.HelloWorld"

输出如下：
![HelloWorld](http://h.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=ebd4d2505cdf8db1b82e7c613918ac69/35a85edf8db1cb13fee0efdbdf54564e93584b69.jpg?referer=66b45c59cf11728b693ab812b527&x=.jpg)

如果不想直接运行`.class`文件，可以将其打包：

	mvn package

打包完成后，会在`target`目录下生成一个JAR文件，文件名由&lt;artifactId&gt; 和 &lt;version&gt;组成。比如本例，将会根据`pom.xml`生成`gs-maven-0.1.0.jar`

![gs-maven-0.1.0.jar](http://g.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=460dfe20be3eb13540c7b7be9625d9ee/18d8bc3eb13533fa448c41e6aad3fd1f40345bc4.jpg?referer=141ce90c19d5ad6ef3ee50daa5da&x=.jpg)

![gs-maven-0.1.0.jar](http://c.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=f9566573a41ea8d38e227401a7314173/838ba61ea8d3fd1f8b9870f2324e251f94ca5f61.jpg?referer=1768b62283cb39db98d75366993f&x=.jpg)
如果你想安装您的项目的JAR文件到本地Maven仓库，那么你应该调用下面语句：

	mvn install

此时，你的项目代码将会经过编译、测试、打包并拷贝到本地依赖库，提供给其他项目引用。

以上例子源码的地址[https://github.com/waylau/maven-demo](https://github.com/waylau/maven-demo)中的demo1

说到项目依赖，下面说下`声明依赖`



#声明依赖
上面的例子比较简单，没有用到其他库。但是真实的项目可能会引用（依赖）到很多其他库。

下面例子，依赖了`Joda Time`的库。

修改`src/main/java/hello/HelloWorld.java`

	package hello;
	
	import org.joda.time.LocalTime;
	
	public class HelloWorld {
	    public static void main(String[] args) {
			LocalTime currentTime = new LocalTime();
			System.out.println("The current local time is: " + currentTime);
			Greeter greeter = new Greeter();
			System.out.println(greeter.sayHello());
		}
	}


现在运行`mvn compile`将会报错，因为没有声明依赖。在 &lt;project&gt; 节点下插入如下：

	<dependencies>
        <dependency>
            <groupId>joda-time</groupId>
            <artifactId>joda-time</artifactId>
            <version>2.2</version>
        </dependency>
    </dependencies>

![声明依赖](http://b.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=90b4aaf06c061d957946373d4bcf7bec/4d086e061d950a7b3bbd9d0c08d162d9f3d3c96a.jpg?referer=87bc8d9bf4246b60221986443728&x=.jpg)

这段内容就声明了项目的依赖。每个依赖节点&lt;dependency&gt;都由三个子节点组成：

* &lt;groupId&gt; ： 该依赖库所属的组织名称
* &lt;artifactId&gt; ： 依赖的库名
* &lt;version&gt; ： 依赖的库版本

在POM 4中，&lt;dependency&gt; 中还引入了&lt;scope&gt; ，它主要管理依赖的部署。目前&lt;scope&gt; 可以使用5个值： 

* compile，缺省值，适用于所有阶段，会随着项目一起发布。 
* provided，类似compile，期望JDK、容器或使用者会提供这个依赖。如servlet.jar。 
* runtime，只在运行时使用，如JDBC驱动，适用运行和测试阶段。 
* test，只在测试时使用，用于编译和运行测试代码。不会随项目发布。 
* system，类似provided，需要显式提供包含依赖的jar，Maven不会在Repository中查找它。 

现在你运行`mvn compile`或者`mvn package`，Maven会自动下载相关依赖。

完整的`pom.xml`

	<?xml version="1.0" encoding="UTF-8"?>
	<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
	    <modelVersion>4.0.0</modelVersion>
	    <groupId>org.springframework</groupId>
	    <artifactId>gs-maven</artifactId>
	    <packaging>jar</packaging>
	    <version>0.1.0</version>
	
	    <!-- tag::joda[] -->
	    <dependencies>
	        <dependency>
	            <groupId>joda-time</groupId>
	            <artifactId>joda-time</artifactId>
	            <version>2.2</version>
	        </dependency>
	    </dependencies>
	    <!-- end::joda[] -->
	
	    <build>
	        <plugins>
	            <plugin>
	                <groupId>org.apache.maven.plugins</groupId>
	                <artifactId>maven-shade-plugin</artifactId>
	                <version>2.1</version>
	                <executions>
	                    <execution>
	                        <phase>package</phase>
	                        <goals>
	                            <goal>shade</goal>
	                        </goals>
	                        <configuration>
	                            <transformers>
	                                <transformer
	                                    implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
	                                    <mainClass>hello.HelloWorld</mainClass>
	                                </transformer>
	                            </transformers>
	                        </configuration>
	                    </execution>
	                </executions>
	            </plugin>
	        </plugins>
	    </build>
	</project>


运行项目：

	mvn exec:java -Dexec.mainClass="hello.HelloWorld"

输出如下：
![运行项目](http://g.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=df2c5f61ab014c081d3b28a03a407335/b999a9014c086e062b9ded8300087bf40bd1cbc5.jpg?referer=3c4e15e7bc096b63d80e6a6035db&x=.jpg)

以上例子源码的地址[https://github.com/waylau/maven-demo](https://github.com/waylau/maven-demo)中的demo2

参考：[http://spring.io/guides/gs/maven/](http://spring.io/guides/gs/maven/)

[http://www.vineetmanohar.com/2009/11/3-ways-to-run-java-main-from-maven/](http://www.vineetmanohar.com/2009/11/3-ways-to-run-java-main-from-maven/)