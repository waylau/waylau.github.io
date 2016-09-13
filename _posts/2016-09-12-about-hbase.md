---
layout: post
title: Apache HBase 入门教程
date: 2016-09-12 01:41
author: admin
comments: true
categories: [HBase]
tags: [HBase]
---

Apache HBase 是一个分布式的、面向列的开源数据库。正如上一节所提到的，该技术来源于 Google 的 Bigtable。就像 Bigtable 利用了 GFS 所提供的分布式数据存储一样，Apache HBase 在 Hadoop 之上提供了类似于Bigtable 的能力。HBase 是 Apache 的 Hadoop 项目的子项目。HBase 不同于一般的关系数据库，它是一个适合于非结构化数据存储的数据库。另一个不同的是 Apache HBase 基于列的而不是基于行的模式。

<!-- more -->

HBase 由 Powerset 在2007年创建，最初是  Hadoop 项目的一部分，之后演变成 Apache 的顶级项目。下面是 HBase 演变的一个简短概述：

* 2006年11月：Google 发布了关于 Bigtable 的论文；
* 2007年2月：HBase 初始化原型在 Hadoop 项目中创建，可以详见 <https://issues.apache.org/jira/browse/HBASE-287>；
* 2007年10月：在 Hadoop 0.15.0 中发布了第一个“可用”版本的 HBase；
* 2008年1月：Hadoop 称为 Apache 的顶级项目，HBase 称为 Hadoop 的子项目；
* 2008年10月：HBase 0.18.1 发布；
* 2009年1月：HBase 0.19.0 发布；
* 2009年9月：HBase 0.20.0 发布，性能有明显的提升；
* 2010年5月：HBase 称为 Apache 的顶级项目；
* 2010年6月：HBase 0.89.20100621，第一个开发版本；
* 2011年1月：HBase 0.90.0 发布，稳定性和持久性有很大提升
* 2012年1月：HBase 0.92.0 发布，注重 coprocessor（协处理器） 和 security（安全控制）；
* 2012年5月：HBase 0.94.0 发布，注重性能提升；
* 2013年10月：HBase 0.96.0 发布，注重 singularity；
* 2014年2月：HBase 0.98.0 发布；
* 2015年2月：HBase 1.0.0 发布。

截止目前 Apache HBase 的最新版本为 1.2.2。


Apache HBase 的官方网站为：<http://hbase.apache.org/>。


## Apache HBase 简介

Apache HBase（Hadoop Database） 是一个高可靠性、高性能、面向列、可伸缩的分布式存储系统，利用 HBase 技术可在廉价 PC 上搭建起大规模结构化存储集群。

HBase 是 Google Bigtable 的开源实现，类似 Google Bigtable 利用 GFS 作为其文件存储系统，HBase 利用 Hadoop HDFS 作为其文件存储系统；Google 运行 MapReduce 来处理 Bigtable 中的海量数据，HBase 同样利用 Hadoop MapReduce 来处理 HBase 中的海量数据；Google Bigtable 利用 Chubby 作为协同服务，HBase 利用 Zookeeper 作为对应。

HBase 是一种“NoSQL”数据库。“NoSQL”是一个通用词表示数据库不是RDBMS ，后者支持 SQL 作为主要访问手段。有许多种 NoSQL 数据库，比如，BerkeleyDB 是本地 NoSQL 数据库例子，而 HBase 是大型分布式数据库。从技术上来说, HBase 更像是“Data Store（数据存储）”多于“Data Base（数据库）”，因为 HBase 缺少很多 RDBMS 的特性，如列类型、第二索引、触发器、高级查询语言等。

然而，HBase 有许多特征同时支持线性化和模块化扩充。HBase 集群通过增加 RegionServer 进行扩展，而且只需要将它可以放到普通的服务器中即可。例如，如果集群从10个扩充到20个 RegionServer，存储空间和处理容量都同时翻倍。RDBMS 也能进行扩充，但仅针对某个点——特别是对一个单独数据库服务器的大小——同时，为了更好的性能，需要依赖特殊的硬件和存储设备，而 HBase 并不需要这些。

HBase 具有如下特性：

* **强一致性读写**：HBase 不是“eventually consistent（最终一致性）”数据存储。这让它很适合高速计数聚合类任务；
* **自动分片(Automatic sharding)**： HBase 表通过 region 分布在集群中。数据增长时，region 会自动分割并重新分布；
* **RegionServer 自动故障转移**；
* **Hadoop/HDFS 集成**：HBase 支持开箱即用地支持 HDFS 作为它的分布式文件系统；
* **MapReduce**： HBase 通过 MapReduce 支持大并发处理；
* **Java 客户端 API**：HBase 支持易于使用的 Java API 进行编程访问；
* **Thrift/REST API**：HBase 也支持 Thrift 和 REST 作为非 Java 前端的访问；
* **Block Cache 和 Bloom Filter**：对于大容量查询优化， HBase 支持 Block Cache 和 Bloom Filter；
* **运维管理**：HBase 支持 JMX 提供内置网页用于运维。

### 1. HBase 的应用场景

HBase 不适合所有场景。

首先，确信有足够多数据，如果有上亿或上千亿行数据，HBase 是很好的备选。如果只有上千或上百万行，则用传统的R DBMS 可能是更好的选择。因为所有数据可以在一两个节点保存，集群其他节点可能闲置。

其次，确信可以不依赖所有 RDBMS 的额外特性（例如，列数据类型、 第二索引、事务、高级查询语言等）。

第三，确信你有足够的硬件。因为 HDFS 在小于5个数据节点时，基本上体现不出它的优势。

虽然，HBase 能在单独的笔记本上运行良好，但这应仅当成是开发阶段的配置。



### 2. Hbase 的优缺点 

Hbase 的优点：

* 列的可以动态增加，并且列为空就不存储数据，节省存储空间
* Hbase 自动切分数据，使得数据存储自动具有水平扩展
* Hbase 可以提供高并发读写操作的支持
* 与 Hadoop MapReduce 相结合有利于数据分析
* 容错性
* 版权免费
* 非常灵活的模式设计（或者说没有固定模式的限制）
* 可以跟 Hive 集成，使用类 SQL 查询
* 自动故障转移
* 客户端接口易于使用
* 行级别原子性，即，PUT 操作一定是完全成功或者完全失败

Hbase 的缺点：

* 不能支持条件查询，只支持按照 row key 来查询
* 容易产生单点故障（在只使用一个 HMaster 的时候）
* 不支持事务
* JOIN 不是数据库层支持的，而需要用 MapReduce
* 只能在逐渐上索引和排序
* 没有内置的身份和权限认证


### 3. HBase 与 Hadoop/HDFS 的差异

[HDFS](http://hadoop.apache.org/hdfs/) 是分布式文件系统，适合保存大文件。官方宣称它并非普通用途文件系统，不提供文件的个别记录的快速查询。另一方面，HBase 基于 HDFS，并能够提供大表的记录快速查找和更新。这有时会可能引起概念混乱。HBase 内部将数据放到索引好的“StoreFiles”存储文件中，以便提供高速查询，而存储文件位于 HDFS中。

如果想了解 HBase 更深层次的内容，推荐阅读 Lars George 的《HBase: The Definitive Guide》。


## Apache HBase 基本概念

在 HBase 的数据被存储在表中，具有行和列。这和关系数据库（RDBMS中）的术语是重叠，但在概念上它们不是一类。相反，应该将 HBase 的表当作是一个多维的 map 结构而更容易让人理解。

### 1. 术语

* **Table（表）**：HBase table 由多个 row 组成。
* **Row（行）**：每一 row 代表着一个数据对象，每一 row 都是以一个 row key（行键）和一个或者多个 column 组成。row key 是每个数据对象的唯一标识的，按字母顺序排序，即 row 也是按照这个顺序来进行存储的。所以，row key 的设计相当重要，一个重要的原则是，相关的 row 要存储在接近的位置。比如网站的域名，row key 就是域名，在设计时要将域名反转（例如，org.apache.www、org.apache.mail、org.apache.jira），这样的话， Apache 相关的域名在 table 中存储的位置就会非常接近的。

* **Column（列）**：column 由 column family 和 column qualifier 组成，由冒号（`:`）进行进行间隔。比如`family:qualifier`。

* **Column Family（列族）**：在 HBase，column family  是 一些 column 的集合。一个 column family 所有 column 成员是有着相同的前缀。比如， courses:history 和 courses:math 都是 courses 的成员。冒号（`:`）是  column family  的分隔符，用来区分前缀和列名。column 前缀必须是可打印的字符，剩下的部分列名可以是任意字节数组。column family 必须在  table 建立的时候声明。column  随时可以新建。在物理上，一个的 column family 成员在文件系统上都是存储在一起。因为存储优化都是针对 column family 级别的，这就意味着，一个 column family 的所有成员的是用相同的方式访问的。

* **Column Qualifier（列限定符）**：column family 中的数据通过 column qualifier 来进行映射。column qualifier 也没有特定的数据类型，以二进制字节来存储。比如某个 column family “content”，其 column qualifier 可以设置为  “content:html” 和 “content:pdf”。虽然 column family 是在 table 创建时就固定了，但 column qualifier 是可变的，可能在不同的 row 之间有很大不同。


* **Cell（单元格）**：cell 是 row、column family 和 column qualifier 的组合，包含了一个值和一个 timestamp，用于标识值的版本。

* **Timestamp（时间戳）**：每个值都会有一个 timestamp，作为该值特定版本的标识符。默认情况下，timestamp 代表了当数据被写入 RegionServer 的时间，但你也可以在把数据放到 cell 时指定不同的 timestamp。

### 2. map

HBase/Bigtable 的核心数据结构就是 [map](http://en.wikipedia.org/wiki/Associative_array)。不同的编程语言针对 map 有不同的术语，比如 associative array（PHP）、associative array（Python），Hash（Ruby）或 Object （JavaScript）。

简单来说，map 就是 key-value 对集合。下面是一个用 JSON 格式来表达 map 的例子：

```json
{
  "zzzzz" : "woot",
  "xyz" : "hello",
  "aaaab" : "world",
  "1" : "x",
  "aaaaa" : "y"
}
```

### 3. 分布式


毫无疑问，HBase/Bigtable 都是建立在分布式系统上的，HBase 基于  Hadoop Distributed File System (HDFS) 或者 Amazon's Simple Storage Service（S3），而 Bigtable 使用 Google File System（GFS）。它们要解决的一个问题就是数据的同步。这里不讨论如何做到数据同步。 HBase/Bigtable 可以部署在成千上万的机器上来分散访问压力。 

### 4. 排序

和一般的 map 实现有所区别，HBase/Bigtable 中的 map 是按字母顺序严格排序的。这就是说，对于 row key 是“aaaaa”的旁边 row key 应该是 “aaaab”，而与 row key 是“zzzzz”离得较远。

还是以上面的 JSON 为例，一个排好序的例子如下：

```json
{
  "1" : "x",
  "aaaaa" : "y",
  "aaaab" : "world",
  "xyz" : "hello",
  "zzzzz" : "woot"
}
```
 
在一个大数据量的系统里面，排序很重要，特别是 row key 的设置策略决定了查询的性能。比如网站的域名，row key 就是域名，在设计时要将域名反转（例如，org.apache.www、org.apache.mail、org.apache.jira）。

### 5. 多维

多维 map，即 map 里面嵌套 map。例如：

```json
{
  "1" : {
    "A" : "x",
    "B" : "z"
  },
  "aaaaa" : {
    "A" : "y",
    "B" : "w"
  },
  "aaaab" : {
    "A" : "world",
    "B" : "ocean"
  },
  "xyz" : {
    "A" : "hello",
    "B" : "there"
  },
  "zzzzz" : {
    "A" : "woot",
    "B" : "1337"
  }
}
```
### 6. 时间版本

在查询中不指定时间，返回的将是最近的一个时间的版本。如果给出 timestamp，返回的将是早于这个时间的数值。例如： 查询  row/column  是“aaaaa”/“A:foo”的，将返回 y；查询 row/column/timestamp 是“aaaaa”/“A:foo”/10的，将返回 m；查询 row/column/timestamp 是“aaaaa”/“A:foo”/2的，将返回 null。

```json
{
  // ...
  "aaaaa" : {
    "A" : {
      "foo" : {
        15 : "y",
        4 : "m"
      },
      "bar" : {
        15 : "d",
      }
    },
    "B" : {
      "" : {
        6 : "w"
        3 : "o"
        1 : "w"
      }
    }
  },
  // ...
}
```


### 7. 概念视图

下面表格是一个名为 webtable 的 table ，包含了两个 row（`com.cnn.www` 和 `com.example.www`）和三个 column family（`contents`、 `anchor`和`people`）。第一个 row（`com.cnn.www`）中，`anchor` 包含了两个 column（`anchor:cssnsi.com`和 `anchor:my.look.ca`），`contents`包含了一个 column（`contents:html`）。在这个例子里面，row key 是`com.cnn.www`的 row 包含了5个版本，而 row key 是`com.example.www`的 row 包含了1个版本。column qualifier 为 `contents:html`包含了给定网站的完整的 HTML。column family 是`anchor`的每个 qualifier 包含了网站的链接。人们列族代表与网站相关的人。column family 是`people`关联的是网站的人物资料。

Row Key | Timestamp	| ColumnFamily contents	| ColumnFamily anchor | ColumnFamily people
---- | ---- | ---- | ---- | ----
"com.cnn.www" |	t9 |		  |	anchor:cnnsi.com = "CNN"  |	
"com.cnn.www" |	t8 |		  |	anchor:my.look.ca = "CNN.com"  |	
"com.cnn.www" |	t6 |	contents:html = "<html>..."	  |	  |	
"com.cnn.www" |	t5 |	contents:html = "<html>..."	  |	  |	
"com.cnn.www" |	t3 |	contents:html = "<html>..."	  |	  |	


在这个表中显示为空的 cell 不占用空间，这使得 HBase 变得“稀疏”。除了表格方式来展现数据试图，也使用使用多维 map，如下：

```json
{
  "com.cnn.www": {
    contents: {
      t6: contents:html: "<html>..."
      t5: contents:html: "<html>..."
      t3: contents:html: "<html>..."
    }
    anchor: {
      t9: anchor:cnnsi.com = "CNN"
      t8: anchor:my.look.ca = "CNN.com"
    }
    people: {}
  }
  "com.example.www": {
    contents: {
      t5: contents:html: "<html>..."
    }
    anchor: {}
    people: {
      t5: people:author: "John Doe"
    }
  }
}
```

### 8. 物理视图

尽管在概念视图里，table 可以被看成是一个稀疏的 row 的集合。但在物理上，它的是按照 column family 存储的。新的 column qualifier （column_family:column_qualifier）可以随时添加进已有的 column family 。

下表是一个  ColumnFamily `anchor`：

Row Key	| Timestamp	| Column Family `anchor`
---- | ---- | ---
"com.cnn.www"	| t9	| anchor:cnnsi.com = "CNN"
"com.cnn.www"	| t8	| anchor:my.look.ca = "CNN.com"

下表是一个  ColumnFamily `contents`：

Row Key	| Timestamp	| ColumnFamily `contents:`
---- | ---- | ---
"com.cnn.www"	| t6	| contents:html = "<html>..."
"com.cnn.www"	| t5	| contents:html = "<html>..."
"com.cnn.www"	| t3	| contents:html = "<html>..."

值得注意的是在上面的概念视图中空白 cell 在物理上是不存储的，因为根本没有必要存储。因此若一个请求为要获取 t8 时间的`contents:html`，他的结果就是空。相似的，若请求为获取 t9 时间的`anchor:my.look.ca`，结果也是空。但是，如果不指明 timestamp，将会返回最新时间的 column。例如，如果请求为获取行键为“com.cnn.www”，没有指明 timestamp 的话，返回的结果是 t6 下的`contents:html`，t9下的`anchor:cnnsi.com`和 t8 下`anchor:my.look.ca`。


### 9. 数据模型操作

四个主要的数据模型操作是 Get、Put、Scan 和 Delete。通过 Table 实例进行操作。有关 Table 的 API 可以参见 <http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/client/Table.html>。

#### Get

Get 返回特定 row 的属性。 Get 通过 Table.get 执行。有关 Get 的 API 可以参见 <http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/client/Get.html>。

#### Put

Put 要么向 table 增加新 row（如果 key 是新的）或更新 row（如果 key 已经存在）。 Put 通过 Table.put（writeBuffer）或 Table.batch（非 writeBuffer）执行。有关 Put 的 API 可以参见 <http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/client/Put.html>。

#### Scan

Scan 允许多个 row 特定属性迭代。

下面是一个在 Table 表实例上的 Scan 示例。假设 table 有几行 row key 为“row1”、“row2”、“row3”，还有一些 row key 值为“abc1”、 “abc2” 和“abc3”。下面的示例展示 Scan 实例如何返回“row”打头的 row。

```java
public static final byte[] CF = "cf".getBytes();
public static final byte[] ATTR = "attr".getBytes();
...

Table table = ...      // instantiate a Table instance

Scan scan = new Scan();
scan.addColumn(CF, ATTR);
scan.setRowPrefixFilter(Bytes.toBytes("row"));
ResultScanner rs = table.getScanner(scan);
try {
  for (Result r = rs.next(); r != null; r = rs.next()) {
    // process result...
  }
} finally {
  rs.close();  // always close the ResultScanner!
}
```

注意，通常最简单的方法来指定用于 scan 停止点是采用 InclusiveStopFilter 类，其 API 可以参见 <http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/filter/InclusiveStopFilter.html>。


#### Delete

Delete 用于从 table 中删除 row。Delete 通过 Table.delete 执行。有关 Delete 的 API 可以参见 <http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/client/Delete.html>。

HBase 没有修改数据的合适方法。所以 delete 通过创建名为 tombstones 的新标志进行处理。这些 tombstones 和死去的值，会在 major compaction 时清除掉。

 
 
## 参考引用

* <http://hbase.apache.org/book.html>
* Shashwat Shriparv.Learning Hbase中文版.周彦伟，蒲聪，娄帅译.北京：电子工业出版社，2015
* Lars George.HBase: The Definitive Guide, 2nd Edition.Sebastopol：O'Reilly Media，2016
* <http://jimbojw.com/wiki/index.php?title=Understanding_Hbase_and_BigTable>