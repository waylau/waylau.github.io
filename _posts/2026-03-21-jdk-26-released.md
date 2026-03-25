---
layout: post
title: JDK 26发布
date: 2026-03-21 00:22
author: admin
comments: true
categories: [JDK,Java]
tags: [JDK,Java]
---

JDK 26 是 Oracle 发布的非LTS版本，2026年3月17日正式GA，聚焦**云原生适配、性能优化、安全演进**三大核心方向，包含**6个正式特性+5个预览/孵化特性**，无破坏性核心变更，以下为基于官方发布及指定文章的精准特性整理，所有JEP编号与特性状态均与原文一致。

本文总结了JDK 26发布的新特性。



<!-- more -->



## 安装包下载

主要分为OpenJDK版本和Oracle版本，下载地址如下：

* OpenJDK版本：<https://jdk.java.net/26/>
* Oracle版本：<https://www.oracle.com/java/technologies/downloads/>


上述版本，如果是个人学习用途，则差异不大。但如果是用于商业用途，则需要仔细看好相关的授权。Oracle JDK商用需要遵循 [Oracle No-Fee Terms and Conditions (NFTC)](https://www.oracle.com/downloads/licenses/no-fee-license.html)协议，而OpenJDK根据[GPL v2许可](https://openjdk.java.net/legal/gplv2+ce.html)获得许可。 


更多有关Java的基本知识，可以参阅《[Java核心编程](https://item.jd.com/12868796.html)》这本书，描述的非常详细。


## JDK 26 新特性说明




## 一、正式标准化特性（GA，可直接生产使用）
### 1. JEP 516：AOT缓存支持任意GC
- **核心改进**：解决此前AOT缓存仅支持特定GC的问题，将缓存对象以**GC无关的逻辑索引格式**存储，启动时通过后台线程流式加载到堆，而非GC特定的内存映射方式。
- **支持GC**：G1、ZGC、Shenandoah、Serial GC等所有垃圾收集器。
- **核心收益**：Serverless/微服务场景冷启动速度提升（此前AOT可提效约40%），流式加载隐藏磁盘IO延迟，且不影响应用峰值吞吐。
- **适用场景**：云原生弹性扩缩容、低延迟服务启动场景。

### 2. JEP 517：HTTP/3 标准化支持
- **状态**：从JDK 24孵化器模块正式纳入标准`java.net.http` API。
- **核心特性**：基于QUIC协议，实现TLS 1.3+QUIC 0-RTT握手、彻底解决队头阻塞、多路复用性能提升、移动网络连接迁移更顺畅。
- **使用方式**：通过`.version(HttpClient.Version.HTTP_3)`显式启用，**默认仍为HTTP/2**，并可透明降级至HTTP/1.1。
```java
// 启用HTTP/3示例
var client = HttpClient.newBuilder()
    .version(HttpClient.Version.HTTP_3)
    .build();
```

### 3. JEP 522：G1 GC 吞吐量优化
- **核心机制**：引入**双卡表（Dual Card Table）**，实现应用线程与GC优化器线程的读写分离。
- **具体改进**：应用线程无锁更新第一张卡表，GC线程处理第二张卡表，消除同步竞争，**大幅降低写屏障开销**。
- **收益**：高吞吐量场景（批处理、大数据计算）性能提升显著，大堆应用稳定性增强。

### 4. JEP 496：ML-DSA 后量子密码算法（正式版）
- **核心定位**：Java平台首个标准化后量子签名算法，应对量子计算对RSA/ECC的潜在威胁。
- **关键特性**：基于NIST标准化的格密码理论，SUN安全提供者实现，x86_64平台AVX2指令集优化，性能较实验版显著提升。
- **使用方式**：**不替换默认签名算法**，需开发者显式选用，适合安全合规、长期数据保护场景。

### 5. JEP 504：移除Applet API
- **移除背景**：JDK 9标记废弃、JDK 17标记废弃待移除，因现代浏览器与JDK均不再支持Applet。
- **核心影响**：彻底终结浏览器Java小程序时代，减少JDK体积，清除无维护的过时代码，**无业务代码兼容性影响**。

### 6. JEP 500：让Final真正意味着不可变（过渡性变更）
- **核心变化**：通过深度反射（`Unsafe`/`MethodHandles`）修改`final`字段时，**从无提示变为抛出警告**，为未来强制不可变做准备。
- **默认配置**：`--illegal-final-field-mutation=warn`（警告但允许修改）；未来将升级为`deny`（抛出`IllegalAccessException`）。
- **适配建议**：序列化库（Jackson/Gson）迁移至`sun.reflect.ReflectionFactory`，DI/测试框架避免修改final字段，改用构造函数注入。
- **排查命令**：
```bash
# 记录final字段修改行为
java -XX:StartFlightRecording:filename=recording.jfr ...
# 分析记录
jfr print --events jdk.FinalFieldMutation recording.jfr
```

## 二、预览特性（Preview，需开启`--enable-preview`）
### 1. JEP 525：结构化并发（第六次预览）
- **核心设计**：将多线程相关任务组视为单一工作单元，简化并发编程。
- **JDK 26关键API调整**：
  - 新增`Joiner.onTimeout()`：超时可返回默认结果；
  - `Joiner::allSuccessfulOrThrow()`：返回结果列表而非Stream；
  - `open()`静态工厂方法：参数从`Function`改为`UnaryOperator`。
- **核心能力**：自动处理任务取消/超时传播、避免线程资源泄漏、错误处理更清晰、线程dump展示父子任务树。
```java
// 结构化并发使用示例
try (var scope = StructuredTaskScope.open()) {
    var user = scope.fork(() -> findUser());
    var order = scope.fork(() -> fetchOrder());
    scope.join();
    if (user.state() == Subtask.State.SUCCESS && order.state() == Subtask.State.SUCCESS) {
        return new Response(user.get(), order.get());
    }
} // 子任务失败自动取消其他任务
```

### 2. JEP 524：PEM编码支持（第二次预览）
- **核心功能**：官方提供PEM格式的编解码API，支持加密密钥、证书、CRL等密码学对象，**无需依赖Bouncy Castle等第三方库**。
- **适用场景**：DevOps/云原生部署的密钥管理，配合ML-DSA实现后量子密钥证书的便捷管理。

### 3. JEP 530：原始类型模式匹配（第四次预览）
- **核心增强**：`instanceof`和`switch`全面支持8大原始类型（int/long/double/float/char/byte/short/boolean）。
- **关键能力**：
  - `instanceof`：直接检查原始类型并**安全转换**（仅值匹配时转换）；
  - `switch`：新增long/float/double/boolean支持，此前仅支持byte/short/char/int。
- **不兼容变更**：引入严格的Dominance检查，覆盖性case后编写子case会报编译错误。
```java
// instanceof原始类型匹配
int i = 1000;
if (i instanceof byte b) {
    System.out.println("安全转换为byte: " + b);
} else {
    System.out.println("值太大，无法转换: " + i);
}

// switch支持long类型
long id = 10_000_000_000L;
switch (id) {
    case 1L -> System.out.println("普通用户");
    case 10_000_000_000L -> System.out.println("VIP用户");
    default -> System.out.println("其他");
}
```

### 4. JEP 526：惰性常量（第二次预览）
- **核心特性**：声明延迟初始化的常量字段，JVM视为**原生常量**，享受`final`字段的性能优化（常量折叠、内联）。
- **核心优势**：初始化时机灵活（**首次使用时初始化**，而非类加载时），适合复杂初始化逻辑但需编译期常量优化的场景。

## 三、孵化特性（Incubator，实验性）
### JEP 529：Vector API（第十一次孵化）
- **核心设计**：让Java直接表达向量计算，可靠编译为CPU最优向量指令（AVX/SSE/NEON），实现SIMD加速。
- **核心收益**：AI推理、科学计算、图像处理等数据密集型场景，**无需编写JNI代码**即可利用CPU硬件加速，性能优于标量计算。
- **状态**：经十一轮孵化API日趋稳定，是Java高性能计算的核心演进方向。

## 四、JDK 26 核心架构价值
1. **云原生/Serverless**：AOT+任意GC兼顾低启动延迟与低延迟GC，弹性扩缩容效率提升；
2. **微服务通信**：HTTP/3基于QUIC协议，广域网多链接并发场景性能大幅提升；
3. **高吞吐场景**：G1双卡表机制降低写屏障开销，批处理/大数据计算收益显著；
4. **安全合规**：ML-DSA正式版开启Java后量子密码时代，适合金融/政务等数据保护场景；
5. **并发编程**：结构化并发减少线程泄漏，让高并发代码更健壮、可观测性更强；
6. **高性能计算**：Vector API持续孵化，Java可原生利用CPU SIMD指令，替代JNI实现硬件加速。

## 五、官方升级建议（按场景划分）
| 应用场景                | 升级建议                                  |
|-------------------------|-------------------------------------------|
| 生产环境LTS稳定运行     | 继续使用JDK 21 LTS或JDK 25 LTS             |
| 技术尝鲜/测试环境       | 可部署体验，重点测试HTTP/3和AOT+任意GC    |
| 云原生/Serverless       | 值得升级，核心特性对场景提效显著          |
| 安全合规/金融/政务      | 关注ML-DSA，提前布局量子安全架构          |
| 密码学应用开发          | 关注PEM编码，减少第三方库依赖              |
| AI/科学计算/图像处理    | 跟踪Vector API演进，测试硬件加速效果      |

**回滚策略**：非LTS版本无长期维护，若升级遇问题直接回退至当前稳定版本（JDK 21/25）。

## 六、核心亮点总结
| JEP编号 | 特性核心               | 特性类型       | 核心适用场景               |
|---------|------------------------|----------------|----------------------------|
| 516     | AOT缓存支持任意GC      | 正式特性       | 云原生/Serverless          |
| 517     | HTTP/3标准化           | 正式特性       | 微服务跨网通信             |
| 496     | ML-DSA后量子密码       | 正式特性       | 安全合规/长期数据保护      |
| 522     | G1 GC吞吐量优化        | 正式特性       | 批处理/大数据计算          |
| 525     | 结构化并发             | 第六次预览     | 高并发服务开发             |
| 529     | Vector API             | 第十一次孵化   | AI/高性能计算/图像处理     |




## 参考引用

* 本文同步至:  <https://waylau.com/jdk-26-released/>
* <https://waylau.com/jdk-25-released/>
* <https://waylau.com/jdk-24-released/>
* <https://waylau.com/jdk-23-released/>
* <https://waylau.com/jdk-22-released/>
* <https://waylau.com/jdk-21-released/>
* <https://waylau.com/jdk-17-released/>
* <https://waylau.com/jdk-16-released/>
* <https://waylau.com/jdk-15-released/>
* <https://waylau.com/jdk-14-released/>
* 《[Java核心编程](https://item.jd.com/12868796.html)》
* 开源项目“现代Java案例大全” <https://github.com/waylau/modern-java-demos>