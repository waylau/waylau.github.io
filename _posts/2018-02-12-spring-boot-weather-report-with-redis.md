---
layout: post
title: Spring Boot 使用 Redis 提升天气预报应用的并发访问能力
date: 2018-02-10 00:22
author: admin
comments: true
categories: [Spring Boot,Redis]
tags: [Spring Boot,Redis]
---

有时，为了提升整个网站的性能，我们会将经常需要访问数据缓存起来，这样，在下次查询的时候，能快速的找到这些数据。
缓存的使用与系统的时效性有着非常大的关系。当我们的系统时效性要求不高时，则选择使用缓存是极好的。当系统要求的时效性比较高时，则并不适合用缓存。
本文，我们将演示如何通过集成 Redis 服务器来进行数据的缓存，以提高微服务的并发访问能力。

<!-- more -->
 
## 为啥我们需要缓存

在之前的文章中，我们已经介绍了如何使用 Spring Boot 来快速实现一个天气预报服务应用`micro-weather-basic`（见 <https://waylau.com/spring-boot-weather-report/>）。通过该应用，能实现简单的天气查询。

天气数据接口，本身时效性不是很高，而且又因为是 Web 服务，在调用过程中，本身是存在延时的。所以，采用缓存，一方面可以有效减轻访问天气接口服务带来的延时问题，另一方面，也可以减轻天气接口的负担，提高并发访问量。

特别地，我们是使用的第三方免费的天气 API，这些 API 往往对用户的调用次数及频率有一定的限制。所以为了减轻天气 API 提供方的负荷，我们并不需要实时去调用其第三方接口。

在`micro-weather-basic`的基础上，我们构建了一个`micro-weather-redis`项目，作为示例。

## 开发环境

为了演示本例子，需要采用如下开发环境：

* JDK 8
* Gradle 4.0
* Spring Boot Web Starter 2.0.0.M4
* Apache HttpClient 4.5.3
* Spring Boot Data Redis Starter 2.0.0.M4
* Redis 3.2.100


## 项目配置

Spring Boot Data Redis 提供了 Spring Boot 对 Redis 的开箱即用的功能。在原有的依赖的基础上，添加 Spring Boot Data Redis Starter 的依赖。

```groovy
// 依赖关系
dependencies {
	//...
 
	// 添加 Spring Boot Data Redis Starter 依赖
	compile('org.springframework.boot:spring-boot-starter-data-redis')

 	//...
}
```

## 下载安装、运行 Redis 

在 Linux 平台上安装 Redis 比较简单，可以参考官方文档来即可，详见<https://github.com/antirez/redis>。

而在 Windows 平台，微软特别为 Redis 制作了安装包，下载地址见 <https://github.com/MicrosoftArchive/redis/releases>。本书所使用的案例，也是基于该安装包来进行的。双击 redis-server.exe 文件，就能快速启动 Redis 服务器了。

安装后，Redis 默认运行在 <localhost:6379> 地址端口 




## 修改 WeatherDataServiceImpl

修改 WeatherDataServiceImpl，增加了 StringRedisTemplate 用于操作 Redis。


```java
@Service
public class WeatherDataServiceImpl implements WeatherDataService {

	private final static Logger logger = LoggerFactory.getLogger(WeatherDataServiceImpl.class);  
	
	@Autowired
	private RestTemplate restTemplate;
	
	@Autowired
	private StringRedisTemplate stringRedisTemplate;
	
	private final String WEATHER_API = "http://wthrcdn.etouch.cn/weather_mini";

	private final Long TIME_OUT = 1800L; // 缓存超时时间
	
	@Override
	public WeatherResponse getDataByCityId(String cityId) {
		String uri = WEATHER_API + "?citykey=" + cityId;
		return this.doGetWeatherData(uri);
	}

	@Override
	public WeatherResponse getDataByCityName(String cityName) {
		String uri = WEATHER_API + "?city=" + cityName;
		return this.doGetWeatherData(uri);
	}

	private WeatherResponse doGetWeatherData(String uri) {
		ValueOperations<String, String> ops = this.stringRedisTemplate.opsForValue();
		String key = uri; // 将调用的 URI 作为缓存的key
		String strBody = null;

		// 先查缓存，没有再查服务
		if (!this.stringRedisTemplate.hasKey(key)) {
			logger.info("未找到 key " + key);
			
			ResponseEntity<String> response = restTemplate.getForEntity(uri, String.class);
			

			if (response.getStatusCodeValue() == 200) {
				strBody = response.getBody();
			}

			ops.set(key, strBody, TIME_OUT, TimeUnit.SECONDS);
		} else {
			logger.info("找到 key " + key + ", value=" + ops.get(key));
			
			strBody = ops.get(key);
		}

		ObjectMapper mapper = new ObjectMapper();
		WeatherResponse weather = null;

		try {
			weather = mapper.readValue(strBody, WeatherResponse.class);
		} catch (IOException e) {
			logger.error("JSON反序列化异常！",e);
		}

		return weather;
	}

}
```

修改了 doGetWeatherData 方法，增加了 Redis 数据的判断：

* 当存在某个key（天气接口的URI，是唯一代表某个地区的天气数据）时，我们就从 Redis 里面拿缓存数据；
* 当不存在某个key（没有初始化数据或者数据过期了），从去天气接口里面去取最新的数据，并初始化到 Redis 中；
* 由于天气数据更新频率的特点（基本上一个小时或者半个小时更新一次），所以，我们在 Redis 里面设置了30分钟的超时时间。

其中，当然 StringRedisTemplate 跟 RedisTemplate 功能类似，都是封装了对 Redis 的一些常用的操作。区别在于，StringRedisTemplate 更加专注于基于字符串的操作，毕竟，在目前咱们的天气预报应用中，数据的格式主要是 JSON 字符串。

ValueOperations 接口，封装了大部分的简单的 K-V 操作。

同时，我们也使用了日志框架，用来记录运行过程，及日常的信息。

## 测试、运行

首先，在进行测试前，需要将 Redis 服务器启动起来。

我们可以通过在一个时间段内，多次访问同一个个天气接口来测试效果，比如，接口 <http://localhost:8080/weather/cityId/101280601>。为了缩短测试的时间，我们可以将 Redis 的超时时间缩短一点，比如10秒。这样，你就不用苦等30分钟才能验证数据是否过期了。

```
2017-10-18 23:51:41.762  INFO 15220 --- [nio-8080-exec-6] c.w.s.c.w.s.WeatherDataServiceImpl       : 未找到 key http://wthrcdn.etouch.cn/weather_mini?citykey=101280601
2017-10-18 23:51:43.649  INFO 15220 --- [nio-8080-exec-5] c.w.s.c.w.s.WeatherDataServiceImpl       : 找到 key http://wthrcdn.etouch.cn/weather_mini?citykey=101280601, value={"data":{"yesterday":{"date":"17日星期二","high":"高温 29℃","fx":"无持续风向","low":"低温 22℃","fl":"<![CDATA[<3级]]>","type":"多云"},"city":"深圳","aqi":"35","forecast":[{"date":"18日星期三","high":"高温 29℃","fengli":"<![CDATA[<3级]]>","low":"低温 23℃","fengxiang":"无持续风向","type":"多云"},{"date":"19日星期四","high":"高温 29℃","fengli":"<![CDATA[<3级]]>","low":"低温 23℃","fengxiang":"无持续风向","type":"多云"},{"date":"20日星期五","high":"高温 30℃","fengli":"<![CDATA[<3级]]>","low":"低温 22℃","fengxiang":"无持续风向","type":"多云"},{"date":"21日星期六","high":"高温 29℃","fengli":"<![CDATA[<3级]]>","low":"低温 21℃","fengxiang":"无持续风向","type":"多云"},{"date":"22日星期天","high":"高温 28℃","fengli":"<![CDATA[<3级]]>","low":"低温 21℃","fengxiang":"无持续风向","type":"多云"}],"ganmao":"各项气象条件适宜，无明显降温过程，发生感冒机率较低。","wendu":"25"},"status":1000,"desc":"OK"}
2017-10-18 23:51:46.700  INFO 15220 --- [nio-8080-exec-7] c.w.s.c.w.s.WeatherDataServiceImpl       : 找到 key http://wthrcdn.etouch.cn/weather_mini?citykey=101280601, value={"data":{"yesterday":{"date":"17日星期二","high":"高温 29℃","fx":"无持续风向","low":"低温 22℃","fl":"<![CDATA[<3级]]>","type":"多云"},"city":"深圳","aqi":"35","forecast":[{"date":"18日星期三","high":"高温 29℃","fengli":"<![CDATA[<3级]]>","low":"低温 23℃","fengxiang":"无持续风向","type":"多云"},{"date":"19日星期四","high":"高温 29℃","fengli":"<![CDATA[<3级]]>","low":"低温 23℃","fengxiang":"无持续风向","type":"多云"},{"date":"20日星期五","high":"高温 30℃","fengli":"<![CDATA[<3级]]>","low":"低温 22℃","fengxiang":"无持续风向","type":"多云"},{"date":"21日星期六","high":"高温 29℃","fengli":"<![CDATA[<3级]]>","low":"低温 21℃","fengxiang":"无持续风向","type":"多云"},{"date":"22日星期天","high":"高温 28℃","fengli":"<![CDATA[<3级]]>","low":"低温 21℃","fengxiang":"无持续风向","type":"多云"}],"ganmao":"各项气象条件适宜，无明显降温过程，发生感冒机率较低。","wendu":"25"},"status":1000,"desc":"OK"}
2017-10-18 23:51:50.513  INFO 15220 --- [nio-8080-exec-8] c.w.s.c.w.s.WeatherDataServiceImpl       : 找到 key http://wthrcdn.etouch.cn/weather_mini?citykey=101280601, value={"data":{"yesterday":{"date":"17日星期二","high":"高温 29℃","fx":"无持续风向","low":"低温 22℃","fl":"<![CDATA[<3级]]>","type":"多云"},"city":"深圳","aqi":"35","forecast":[{"date":"18日星期三","high":"高温 29℃","fengli":"<![CDATA[<3级]]>","low":"低温 23℃","fengxiang":"无持续风向","type":"多云"},{"date":"19日星期四","high":"高温 29℃","fengli":"<![CDATA[<3级]]>","low":"低温 23℃","fengxiang":"无持续风向","type":"多云"},{"date":"20日星期五","high":"高温 30℃","fengli":"<![CDATA[<3级]]>","low":"低温 22℃","fengxiang":"无持续风向","type":"多云"},{"date":"21日星期六","high":"高温 29℃","fengli":"<![CDATA[<3级]]>","low":"低温 21℃","fengxiang":"无持续风向","type":"多云"},{"date":"22日星期天","high":"高温 28℃","fengli":"<![CDATA[<3级]]>","low":"低温 21℃","fengxiang":"无持续风向","type":"多云"}],"ganmao":"各项气象条件适宜，无明显降温过程，发生感冒机率较低。","wendu":"25"},"status":1000,"desc":"OK"}
2017-10-18 23:51:53.140  INFO 15220 --- [nio-8080-exec-9] c.w.s.c.w.s.WeatherDataServiceImpl       : 未找到 key http://wthrcdn.etouch.cn/weather_mini?citykey=101280601
```

从上述日志可以看到，第一次（23:51:41）访问接口时，没有找到 Redis 里面的数据，所以，就初始化了数据。
后面几次访问，都是访问 Redis 里面的数据。最后一次（23:51:53），由于超时了，所以 Redis 里面又没有数据了，所以又会拿天气接口的数据。

## 源码

本章节的源码，见 <https://github.com/waylau/spring-cloud-tutorial/> samples目录下的 `micro-weather-redis`目录下。


## 参考资料

* https://waylau.com/spring-boot-weather-report/
* Spring Boot 教程：<https://github.com/waylau/spring-boot-tutorial>
* Spring Cloud 教程：<https://github.com/waylau/spring-cloud-tutorial>
* Spring Boot 开发企业级博客系统：<https://waylau.com/spring-boot-blog-video-release/>
* 从天气项目看Spring Cloud微服务治理：<https://waylau.com/spring-cloud-video-release/>
* 原文同步至：<https://waylau.com/spring-boot-weather-report-with-redis/>



