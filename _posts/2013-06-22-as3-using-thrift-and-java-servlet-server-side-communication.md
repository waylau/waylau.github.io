---
layout: post
title:   AS3使用thrift的与JHava servlet服务器端通信
date:   2013-06-22 02:11
author: admin
comments: true
categories: [ActionScript3,Java]
tags: [ActionScript3,Java,Thrift]
---
##一、基本概念

Thrift是一个软件框架，用来进行可扩展且跨语言的服务的开发。它结合了功能强大的软件堆栈和代码生成引擎，以构建在 C++, Java, Python, PHP, Ruby, Erlang, Perl, Haskell, C#, Cocoa, JavaScript, Node.js, Smalltalk, and OCaml 等等编程语言间无缝结合的、高效的服务。

Thrift最初由facebook开发，07年四月开放源码，08年5月进入apache孵化器。thrift允许你定义一个简单的定义文件中的数据类型和服务接口。以作为输入文件，编译器生成代码用来方便地生成RPC客户端和服务器通信的无缝跨编程语言。

官网地址：<http://thrift.apache.org>

###1.数据类型

基本类型： bool：布尔值，true 或 false，对应 Java 的 boolean byte：8 位有符号整数，对应 Java 的 byte i16：16 位有符号整数，对应 Java 的 short i32：32 位有符号整数，对应 Java 的 int i64：64 位有符号整数，对应 Java 的 long double：64 位浮点数，对应 Java 的 double string：utf-8编码的字符串，对应 Java 的 String 结构体类型： struct：定义公共的对象，类似于 C 语言中的结构体定义，在 Java 中是一个 JavaBean 容器类型： list：对应 Java 的 ArrayList set：对应 Java 的 HashSet map：对应 Java 的 HashMap 异常类型： exception：对应 Java 的 Exception 服务类型： service：对应服务的类

###2.服务端编码基本步骤：

实现服务处理接口impl 创建TProcessor 创建TServerTransport 创建TProtocol 创建TServer 启动Server 3.客户端编码基本步骤：

创建Transport 创建TProtocol 基于TTransport和TProtocol创建 Client 调用Client的相应方法 4.数据传输协议

TBinaryProtocol : 二进制格式. TCompactProtocol : 压缩格式 TJSONProtocol : JSON格式 TSimpleJSONProtocol : 提供JSON只写协议, 生成的文件很容易通过脚本语言解析 tips:客户端和服务端的协议要一致

##二、as3与java通信示例

test.thrift namespace java com.xzw.thrift namespace as3 com.xzw.thrift struct User{ 1:i32 userId, 2:string loginName, 3:string password, 4:string name }

	exception UserNotFound{ 
	    1:string msg  //as3中由于message是关键字，所以避免使用message关键字。 
	} 
	
	service UserService{ 
	    User getUser(1:string loginName) throws (1:UserNotFound unf), 
	    list&lt;User&gt; getUsers() 
	} 
通过命令生成java代码

>thrift --gen java test.thrift

以上命令会生成gen-java的目录拷贝里面的代码到你的项目中。如图com.xzw.thrift就是

工具生成java类。

<img src="http://www.it165.net/uploadfile/2013/0424/20130424094236841.jpg"/>

其中我们需要去实现UserService类中的IFace接口。代码如下： /* * To change this template, choose Tools | Templates * and open the template in the editor. */ package com.xzw.service;

```java
import com.xzw.thrift.User; 
import com.xzw.thrift.UserNotFound; 
import com.xzw.thrift.UserService; 
import java.util.ArrayList; 
import java.util.List; 
import java.util.logging.Logger; 
import org.apache.thrift.TException; 

/** 
 * 
 * @author xzw 
 */ 
public class UserServiceHandler implements UserService.Iface{ 

    public User getUser(String loginName) throws UserNotFound, TException { 
        if(!"xuzhiwei".equals(loginName)){ 
            UserNotFound e = new UserNotFound("用户无法找到！"); 
            throw e; 
        }  
        User user = new User(); 
        user.setUserId(100); 
        user.setLoginName("xuzhiwei"); 
        user.setPassword("123456"); 
        user.setName("user1"); 
        Logger.getLogger("user=&gt;"+user.toString()); 
        return user;  
    } 

    public List&lt;User&gt; getUsers() throws TException { 
       List&lt;User&gt; list = new ArrayList&lt;User&gt;();   
        User user = new User();   
        user.setUserId(100);   
       user.setLoginName("xuzhiwei"); 
        user.setPassword("123456"); 
        user.setName("user1");   
        list.add(user);   
        User user2 = new User();   
        user2.setUserId(200);   
        user2.setLoginName("login2");   
        user2.setPassword("pwd2");   
        user2.setName("user2");   
        list.add(user2);   

         Logger.getLogger("user list=&gt;"+list.toString()); 
        return list;   
    } 

}
```

编写servlet，servlet需要继承thrift提供的类包TServlet即可，不需要去重写doGet，doPost方法。 /* * To change this template, choose Tools | Templates * and open the template in the editor. */ package com.xzw.service;

```java
import com.xzw.thrift.UserService; 
import org.apache.thrift.protocol.TBinaryProtocol; 
import org.apache.thrift.server.TServlet; 

/** 
 * 
 * @author xzw 
 */ 
public class UserServlet extends TServlet{   

     public UserServlet(){   
         super(new UserService.Processor(new UserServiceHandler()),new TBinaryProtocol.Factory(true, true)); 
     }        
}
```
以上就完成服务器端的编写了。

可以使用junit测试一下： /* * To change this template, choose Tools | Templates * and open the template in the editor. */

```java
import com.xzw.thrift.User; 
import com.xzw.thrift.UserService; 
import java.util.List; 
import java.util.logging.Level; 
import java.util.logging.Logger; 
import org.apache.thrift.TException; 
import org.apache.thrift.protocol.TBinaryProtocol; 
import org.apache.thrift.protocol.TProtocol; 
import org.apache.thrift.transport.THttpClient; 
import org.junit.*; 

/** 
 * 
 * @author xzw 
 */ 
public class TestServlet { 

     @Test 
    public void test(){ 
        String serveltUrl = "http://localhost:8080/test_thrift/userServlet"; 
        try { 
            THttpClient thc = new THttpClient(serveltUrl); 
            TProtocol lopFactory = new TBinaryProtocol(thc); 
           UserService.Client client = new UserService.Client(lopFactory); 

           List users = client.getUsers(); 
           System.out.println("--&gt;&gt;"+users.toString()); 
        } catch (TException ex) { 
            Logger.getLogger(TestServlet.class.getName()).log(Level.SEVERE, null, ex); 
        } 
   } 

    @Test 
    public void test2(){ 
        String serveltUrl = "http://localhost:8080/test_thrift/userServlet"; 
        try { 
            THttpClient thc = new THttpClient(serveltUrl); 
            TProtocol lopFactory = new TBinaryProtocol(thc); 
           UserService.Client client = new UserService.Client(lopFactory); 

           User user = client.getUser("xuzhiwei"); 
           System.out.println("--&gt;"+user.toString()); 
        } catch (TException ex) { 
            Logger.getLogger(TestServlet.class.getName()).log(Level.SEVERE, null, ex); 
        } 
   } 
}
```
接下来生成as3代码

>thrift --gen as3 test.thrift www.it165.net

以上代码会生成gen-as3的目录，拷贝目录下的代码到你的工程中。

<img src="http://www.it165.net/uploadfile/2013/0424/20130424094236468.jpg"/>

编写as3客户端类： 

```as3
package { 
import com.xzw.thrift.User; 
import com.xzw.thrift.UserNotFound; 
import com.xzw.thrift.UserService; 
import com.xzw.thrift.UserServiceImpl;

    import flash.display.Sprite; 
    import flash.net.URLRequest; 
    import flash.text.TextField; 

    import org.apache.thrift.protocol.TBinaryProtocol; 
    import org.apache.thrift.protocol.TProtocol; 
    import org.apache.thrift.transport.THttpClient; 

    public class testthrift extends Sprite 
    {  
        private const SERVER_URL:String = "http://localhost:8080/test_thrift/userServlet";  
        private var textField:TextField; 

        public function testthrift() 
        {  
            init(); 
            connServer();  
        } 

        private function init():void 
        { 
            textField = new TextField();  
            textField.width = 300; 
            textField.height = 500;  
            textField.autoSize = "left"; 
            addChild(textField); 
        } 

        private function connServer():void 
        {   //实例化URLRequest 
            var urlRequest:URLRequest = new URLRequest(SERVER_URL); 
            //实例化THttpClient 
            var thc:THttpClient = new THttpClient(urlRequest); 
            var protocol:TProtocol = new TBinaryProtocol(thc); 

            var usImpl:UserService = new UserServiceImpl(protocol); 
            usImpl.getUser("xuzhiwei",onError,onUserSuccess); 
            //usImpl.getUsers(onError,onSuccess); 
        } 

        private function onError(e:UserNotFound):void{ 
            trace(e); 
        } 

        private function onUserSuccess(user:User):void{ 
            trace("success:"); 
            trace(user); 
            textField.text = user.toString(); 
        } 

        private function onSuccess(user:Array):void{ 

            trace(user); 
            textField.text = user.toString(); 
        } 

    } 
}
```

写完了，测试结果

<img src="http://www.it165.net/uploadfile/2013/0424/20130424094236481.jpg"/>

参考：<http://xuzhiwei.blog.51cto.com/978424/1184540>

<http://jnb.ociweb.com/jnb/jnbJun2009.html>