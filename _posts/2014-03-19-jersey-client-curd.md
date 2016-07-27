---
layout: post
title: 用Jersey构建RESTful服务4--通过jersey-client客户端调用Jersey的Web服务模拟CURD
date: 2014-03-19 03:13
author: admin
comments: true
categories: [Java, Jersey, REST]
tags: [Java, Jersey, RESTful]
---
#一、总体说明

通过jersey-client接口，创建客户端程序，来调用Jersey实现的RESTful服务，实现增、删、改、查等操作。
服务端主要是通过内存的方式，来模拟用户的增加、删除、修改、查询等操作。

#二、创建服务端

1.在上文项目中，
在“com.waylau.rest.resources.UserResource“中修改代码，
首先创建一个HashMap，用来保存添加的用户

	private static Map<String,User> userMap  = new HashMap<String,User>();  

2.创建增、删、改、查 用户资源等操作

```java

    /** 
     * 增加 
     * @param user 
     */  
    @POST  
    @Consumes({MediaType.APPLICATION_XML, MediaType.APPLICATION_JSON})  
    public void createStudent(User user)  
    {  
        userMap.put(user.getUserId(), user );  
    }  
      
    /** 
     * 删除 
     * @param id 
     */  
    @DELETE  
    @Path("{id}")  
    public void deleteStudent(@PathParam("id")String id){  
        userMap.remove(id);  
    }  
      
    /** 
     * 修改 
     * @param user 
     */  
    @PUT  
    @Consumes(MediaType.APPLICATION_XML)  
    public void updateStudent(User user){  
        userMap.put(user.getUserId(), user );  
    }  
   
    /** 
     * 根据id查询 
     * @param id 
     * @return 
     */  
    @GET  
    @Path("{id}")  
    @Produces({MediaType.APPLICATION_XML, MediaType.APPLICATION_JSON})  
    public User getUserById(@PathParam("id") String id){  
        User u = userMap.get(id);  
        return u;  
    }  
     
    /** 
     * 查询所有 
     * @return 
     */  
    @GET  
    @Produces({MediaType.APPLICATION_XML, MediaType.APPLICATION_JSON})  
    public List<User> getAllUsers(){       
        List<User> users = new ArrayList<User>();     
        users.addAll( userMap.values() );    
        return users;  
    }  

```    

#三、创建客户端程序

1.创建包“com.waylau.rest.client”，在包下建一个UserClient.java，代码如下：

```java

	package com.waylau.rest.client;  
	  
	import javax.ws.rs.client.Client;  
	import javax.ws.rs.client.ClientBuilder;  
	import javax.ws.rs.client.Entity;  
	import javax.ws.rs.client.WebTarget;  
	import javax.ws.rs.core.MediaType;  
	import javax.ws.rs.core.Response;  
	  
	import org.codehaus.jackson.jaxrs.JacksonJsonProvider;  
	  
	import com.waylau.rest.bean.User;  
	  
	   
	/** 
	 * 用户客户端，用来测试资源 
	 * @author waylau.com 
	 * 2014-3-18 
	 */  
	public class UserClient {  
	  
	    private static String serverUri = "http://localhost:8089/RestDemo/rest";  
	    /** 
	     * @param args 
	     */  
	    public static void main(String[] args) {  
	        addUser();  
	        getAllUsers();  
	        updateUser();  
	        getUserById();  
	        getAllUsers();  
	        delUser();  
	        getAllUsers();  
	  
	    }  
	    /** 
	     * 添加用户 
	     */  
	     private static void addUser() {  
	         System.out.println("****增加用户addUser****");  
	         User user = new User("006","Susan","21");    
	         Client client = ClientBuilder.newClient();  
	         WebTarget target = client.target(serverUri + "/users");  
	         Response response = target.request().buildPost(Entity.entity(user, MediaType.APPLICATION_XML)).invoke();  
	         response.close();  
	    }  
	       
	    /** 
	     * 删除用户 
	     */  
	     private static void delUser() {  
	         System.out.println("****删除用户****");  
	         Client client = ClientBuilder.newClient();  
	         WebTarget target = client.target(serverUri + "/users/006");  
	         Response response = target.request().delete();  
	         response.close();  
	    }  
	       
	       
	    /** 
	     * 修改用户 
	     */  
	     private static void updateUser() {  
	         System.out.println("****修改用户updateUser****");  
	         User user = new User("006","Susan","33");    
	         Client client = ClientBuilder.newClient();  
	         WebTarget target = client.target(serverUri + "/users");  
	         Response response = target.request().buildPut( Entity.entity(user, MediaType.APPLICATION_XML)).invoke();  
	         response.close();  
	    }  
	    /** 
	     * 根据id查询用户 
	     */  
	     private static void getUserById() {  
	         System.out.println("****根据id查询用户****");  
	         Client client = ClientBuilder.newClient().register(JacksonJsonProvider.class);// 注册json 支持  
	         WebTarget target = client.target(serverUri + "/users/006");  
	         Response response = target.request().get();  
	         User user = response.readEntity(User.class);  
	         System.out.println(user.getUserId() + user.getUserName()  +  user.getAge());  
	         response.close();  
	    }  
	    /** 
	     * 查询所有用户 
	     */  
	     private static void getAllUsers() {  
	         System.out.println("****查询所有getAllUsers****");  
	           
	         Client client = ClientBuilder.newClient();  
	  
	         WebTarget target = client.target(serverUri + "/users");  
	         Response response = target.request().get();  
	 		 String value = response.readEntity(String.class);  
	    	 System.out.println(value);  
			 response.close();  //关闭连接  
	     }  
	       
	}  

```

#四、运行
启动服务端项目，运行客户端程序UserClient，控制台输出如下
 
	****增加用户addUser****  
	****查询所有getAllUsers****  
	[{"userId":"006","userName":"Susan","age":"21"}]  
	****修改用户updateUser****  
	****根据id查询用户****  
	006Susan33  
	****查询所有getAllUsers****  
	[{"userId":"006","userName":"Susan","age":"33"}]  
	****删除用户****  
	****查询所有getAllUsers****  
	[]  

#五、总结
1.客户端如果需要进行JSON转换，需要进行JSON注册

	Client client = ClientBuilder.newClient().register(JacksonJsonProvider.class);  

2.WebTarget 指明了要请求的资源的地址

3.target.request(). 后面跟的是请求的方法:POST，GET，PUT或DELETE




**本章源码**：[https://github.com/waylau/RestDemo/tree/master/jersey-demo4-client-curd](https://github.com/waylau/RestDemo/tree/master/jersey-demo4-client-curd)
