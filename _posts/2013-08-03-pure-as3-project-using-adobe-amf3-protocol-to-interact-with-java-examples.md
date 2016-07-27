---
layout: post
title: 纯AS3项目利用 Adobe AMF3 协议与 Java 交互，示例
date: 2013-08-03 02:30
author: admin
comments: true
categories: [ActionScript3, Java]
---

	package 
	{ 
	    import flash.net.NetConnection; 
	    import flash.net.ObjectEncoding; 
	    import flash.net.Responder; 
	
	    public class Amf3ServiceInvoker  
	    {              
	
	       private var nc : NetConnection = new NetConnection();    
	       private var responder : Responder = null; 
	       private const amf3URL : String = “http://gcc.waylau.com:8080/messagebroker/amf”; 
	
	       public function Amf3ServiceInvoker(result : Function, error : Function) 
	       {                     
	           responder = new Responder(result, error);  
	           super(); 
	       } 
	
	       public function getWeather2(city: String) : void{ 
	           nc.objectEncoding = ObjectEncoding.AMF3;  
	           nc.connect(amf3URL);      
	           nc.call(“amf3ServiceInvoker.getWeather2″, responder, city); 
	       } 
	
	    } 
	}