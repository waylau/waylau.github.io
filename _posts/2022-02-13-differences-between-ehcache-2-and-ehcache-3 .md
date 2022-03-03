---
layout: post
title: Ehcache 2升级到Ehcache 3的改动点
date: 2022-02-13 00:22
author: admin
comments: true
categories: [Ehcache]
tags: [Ehcache]
---

本文总结了Ehcache 2升级到Ehcache 3的改动点。


<!-- more -->



## Ehcache 2升级到Ehcache 3的改动点


### 1. 包名更改


Ehcache 2包名如下：


```java
import net.sf.ehcache.Cache;
```

Ehcache 3包名如下：

```java
import org.ehcache.Cache;
```

### 2. 配置文件


Ehcache 2配置文件如下：

```xml
<ehcache>

    <diskStore path="java.io.tmpdir"/>

    <defaultCache
        maxEntriesLocalHeap="10000"
        eternal="false"
        timeToIdleSeconds="120"
        timeToLiveSeconds="120"
        maxEntriesLocalDisk="10000000"
        diskExpiryThreadIntervalSeconds="120"
        memoryStoreEvictionPolicy="LRU">
        <persistence strategy="localTempSwap"/>
    </defaultCache>

</ehcache>
```


Ehcache 3配置文件如下：


```xml
<config
xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'
xmlns='http://www.ehcache.org/v3'
xsi:schemaLocation="http://www.ehcache.org/v3 http://www.ehcache.org/schema/ehcache-core.xsd">
    <cache-template name="example">
        <key-type>java.lang.Long</key-type>
        <value-type>java.lang.String</value-type>
        <heap unit="entries">200</heap>
    </cache-template>
</config>

Ehcache 2 初始化配置方式如下：


```java
try (InputStream fis = this.getClass().getResourceAsStream("/config/ehcache.xml")) {
    manager = new CacheManager(fis);
}
```

Ehcache 3初始化配置方式如下：


```java
final URL myUrl = getClass().getResource("/configs/ ehcache.xml ");

XmlConfiguration xmlConfig = new XmlConfiguration(myUrl);

CacheManager myCacheManager = CacheManagerBuilder.newCacheManager(xmlConfig);
```

### 3. CacheManager类

Ehcache 2的CacheManager有addCacheIfAbsent方法。


```java
public Cache getCacheAddIfAbsent(String cacheName) {

    Cache cache = manager.getCache(cacheName);

    if (cache == null) {
        synchronized (EhcacheManager.class) {
            manager.addCacheIfAbsent(cacheName);
            cache = manager.getCache(cacheName);
        }
    }

    return cache;

}
```

Ehcache 3的CacheManager没有addCacheIfAbsent方法，需自己实现。


```java
private CacheManager manager;

public Cache getCacheAddIfAbsent(String cacheName) {

    Cache cache = manager.getCache(cacheName, String.class, Object.class);

    if (cache == null) {
        try {
            cache = manager.createCache(cacheName, configurationBuilder);
        } catch (IllegalArgumentException e) {
            //cache already exists
            LOGGER.info("cache {} already exists", cacheName);
            cache = manager.getCache(cacheName, String.class, Object.class);
        }
    }

    return cache;

}
```

### 4. 没有了Element的概念

Ehcache 3已经没有了net.sf.ehcache.Element的概念。

Ehcache 2从缓存获取值的方式如下：


```java
Cache cache = ……;

Element element = cache.get("key1");

Serializable value = element.getValue();
```


Ehcache 3从缓存获取值的方式如下：



```java
Cache< String, String> myCache = ……;

String value = myCache.get("key1");
```


Ehcache 3没有了Element这个中间对象，用法上更加简洁。




## 参考引用

* 原本同步至：<https://waylau.com/differences-between-ehcache-2-and-ehcache-3/>
* 该版本在高并发下bug：<https://github.com/ehcache/ehcache3/issues/2787>