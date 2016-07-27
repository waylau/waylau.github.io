---
layout: post 
title: 用Jersey构建RESTful服务2--JAVA对象转成XML输出
date: 2014-03-17 04:04 
author: admin 
comments: true
categories: [Java, Jersey, REST]
tags: [Java, Jersey, RESTful]
---

##一、 总体说明
XML和JSON 是最为常用的数据交换格式。本例子演示如何将java对象，转成XML输出。
##二、流程
* 1.在上文的例子中，创建一个包“com.waylau.rest.bean”
* 2.在该包下创建一个JAVA类"User"

```java
	package com.waylau.rest.bean;  
	import javax.xml.bind.annotation.XmlRootElement;  
	/* *
	 * 用户 bean 
	 * @author waylau.com 
	 * 2014-3-17 
	 */  
	@XmlRootElement  
	public class User {  
	      
	    private String userId;  
	    private String userName;  
	    private String age;  
	      
	    public User() {};  
	      
	    public User(String userId, String userName, String age) {  
	        this.userId = userId;  
	        this.userName = userName;  
	        this.age = age;  
	    }  
	    public String getUserId() {  
	        return userId;  
	    }  
	    public void setUserId(String userId) {  
	        this.userId = userId;  
	    }  
	    public String getUserName() {  
	        return userName;  
	    }  
	    public void setUserName(String userName) {  
	        this.userName = userName;  
	    }  
	    public String getAge() {  
	        return age;  
	    }  
	    public void setAge(String age) {  
	        this.age = age;  
	    }  
	}  
```

注意：该类上面增加了一个注解“@XmlRootElement”，在将该类转化成XML时，说明这个是XML的根节点。

* 3.在“com.waylau.rest.resources”中，增加资源“UserResource“，代码如下：

```java
	package com.waylau.rest.resources;  
	  
	import java.util.ArrayList;  
	import java.util.HashMap;  
	import java.util.List;  
	import java.util.Map;  
	  
	import javax.ws.rs.Path;  
	import javax.ws.rs.Produces;  
	import javax.ws.rs.PathParam;  
	import javax.ws.rs.core.MediaType;  
	import javax.ws.rs.DELETE;  
	import javax.ws.rs.GET;  
	import javax.ws.rs.POST;  
	import javax.ws.rs.PUT;  
	  
	import com.waylau.rest.bean.User;  
	  
	@Path("/users")  
	public class UserResource {  
	    private static Map<String,User> userMap = new HashMap<String,User>();//存储用户  
	     /** 
	     * 查询所有 
	     * @return 
	     */  
	    @GET  
	    @Produces(MediaType.APPLICATION_XML)  
	    public List<User> getAllUsers(){       
	        List<User> users = new ArrayList<User>();  
	        User u1 = new User("001","WayLau","26");  
	        User u2 = new User("002","King","23");  
	        User u3 = new User("003","Susan","21");  
	          
	        userMap.put(u1.getUserId(), u1);  
	        userMap.put(u2.getUserId(), u2);  
	        userMap.put(u3.getUserId(), u3);  
	          
	        users.addAll( userMap.values() );  
	        return users;  
	    }  
	      
	    @GET  
	    @Path("/getUserXml")  
	    @Produces(MediaType.APPLICATION_XML)  
	    public User getUserXml() {  
	     User user  = new User();  
	     user.setAge("21");  
	     user.setUserId("004");  
	     user.setUserName("Amand");  
	     return user;  
	    }  
	}  
```

其中MediaType.APPLICATION_XML 说明了是以XML形式输出

在浏览器输入<http://localhost:8089/RestDemo/rest/users/getUserXml>，输出单个对象
<img src="http://h.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=30ef47f39c2f07085b052a05d91fc9a4/267f9e2f07082838a80989a8ba99a9014c08f102.jpg?referer=276bf8ae700e0cf3f9e07bcb3f00&x=.jpg"/>
在浏览器输入 <http://localhost:8089/RestDemo/rest/users>  输出对象的集合
<img src="http://e.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=9cd4b18cb8a1cd1101b672258929b9c1/d000baa1cd11728b2318d99ecafcc3cec3fd2c50.jpg?referer=ac9db1cfaf4bd1135dda82028c4e&x=.jpg"/>



**本章源码**：[https://github.com/waylau/RestDemo/tree/master/jersey-demo2-xml](https://github.com/waylau/RestDemo/tree/master/jersey-demo2-xml)
