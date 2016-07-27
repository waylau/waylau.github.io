---
layout: post
title: Spring singleton bean 与 prototype bean 的依赖
date: 2015-05-07 02:41
author: admin
comments: true
categories: [Spring]
tags: [Spring,singleton,prototype]
---

##问题

我们知道，Spring bean 默认的 scope 是 singleton（单例），但有些场景（比如多线程）需要每次调用都生成一个实例，
此时 scope 就应该设为 prototype。如：

<!-- more -->

    @Component
    @Scope("prototype")
    public class DadTask implements Runnable {
    	static Logger logger = Logger.getLogger(DadTask.class);
    	
    	@Autowired
    	DadDao dadDao;
    	
    	private String name;
    	/**
    	 * 
    	 */
    	public DadTask setDadTask(String name) {
    		this.name = name;
    		return this;
    	}
    
    	/* (non-Javadoc)
    	 * @see java.lang.Runnable#run()
    	 */
    	@Override
    	public void run() {
    		logger.info("DadTask:"+this + ";DadDao:"+dadDao + ";"+dadDao.sayHello(name) );
    		//logger.info("Dad:"+name);
    	}
    
    }
    
但是，如果 singlton bean 依赖 prototype bean ，通过依赖注入方式, prototype bean 在 singlton bean 实例化时会创建一次(只一次)，
比如：

    @Service
    public class UserService {
    
    	@Autowired
    	private DadTask dadTask;
 
    	public void startTask() {
            ScheduledThreadPoolExecutor  scheduledThreadPoolExecutor  = new ScheduledThreadPoolExecutor(2);
    
            scheduledThreadPoolExecutor.scheduleAtFixedRate(dadTask.setDadTask("Lily"), 1000, 2000, TimeUnit.MILLISECONDS);
            scheduledThreadPoolExecutor.scheduleAtFixedRate(dadTask.setDadTask("Lucy"), 1000, 2000, TimeUnit.MILLISECONDS);
    	}
    }
    
我希望调度“Lily” 和 “Lucy” 两个线程，实际上，它只给我初始化一个实例（这样就线程非安全了）。

![](http://99btgc01.info/uploads/2015/05/001%284%29.jpg)

## 解决

如果 singlton bean 想每次都去创建一个新的 prototype bean 的实例, 需要通过方法注入的方式。
可以通过实现 ApplicationContextAware  接口，来获取到 ApplicationContext  实例，
继而通过 getBean 方法来获取到 prototype bean 的实例。我们的程序需要修改如下：

    @Service
    public class UserService implements ApplicationContextAware {
     
    	@Autowired
    	private DadTask dadTask;
    	
    	private ApplicationContext applicationContext;
     
    	public void startTask() {
            ScheduledThreadPoolExecutor  scheduledThreadPoolExecutor  = new ScheduledThreadPoolExecutor(2);
            
            // 每次都拿到 DadTask 的实例
            dadTask = applicationContext.getBean("dadTask", DadTask.class);
            scheduledThreadPoolExecutor.scheduleAtFixedRate(dadTask.setDadTask("Lily"), 1000, 2000, TimeUnit.MILLISECONDS);
            dadTask = applicationContext.getBean("dadTask", DadTask.class);
            scheduledThreadPoolExecutor.scheduleAtFixedRate(dadTask.setDadTask("Lucy"), 1000, 2000, TimeUnit.MILLISECONDS);
    	}
    
    	@Override
    	public void setApplicationContext (ApplicationContext applicationContext) throws BeansException {
            this.applicationContext = applicationContext;
    		
    	}
    }
    
    
OK ，问题解决

![](http://99btgc01.info/uploads/2015/05/001%282%29.jpg)

## 源码

见 <https://github.com/waylau/spring-framework-4-demos> 中  `beanScope` 目录

## 参考

* [Spring Framework 4.x参考文档](https://github.com/waylau/spring-framework-4-reference)
