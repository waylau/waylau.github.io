---
layout: post
title: Apache Hadoop 入门教程
date: 2016-08-28 01:41
author: admin
comments: true
categories: [Hadoop]
tags: [Hadoop]
---

 
Apache Hadoop 是一个由 Apache 基金会所开发的分布式系统基础架构。可以让用户在不了解分布式底层细节的情况下，开发出可靠、可扩展的分布式计算应用。

Apache Hadoop 框架，允许用户使用简单的编程模型来实现计算机集群的大型数据集的分布式处理。它的目的是支持从单一服务器到上千台机器的扩展，充分利用了每台机器所提供本地计算和存储，而不是依靠硬件来提供高可用性。其本身被设计成在应用层检测和处理故障的库，对于计算机集群来说，其中每台机器的顶层都被设计成可以容错的，以便提供一个高度可用的服务。

Apache Hadoop 的框架最核心的设计就是：HDFS 和 MapReduce。HDFS 为海量的数据提供了存储，而 MapReduce 则为海量的数据提供了计算。

<!-- more -->

## Apache Hadoop 简介

正如上一节 MapReduce 所提到的那样，Apache Hadoop 受到了 Google  的 GFS 和 MapReduce 的启发，而前者产生了 Apache Hadoop 的分布式文件系统  NDFS (Nutch Distributed File System) ，而后者也被纳入到 Apache Hadoop 作为核心组件之一。

Apache Hadoop 的雏形开始于2002年的 Apache 的 Nutch。Nutch 是一个开源 Java 实现的搜索引擎。它提供了我们运行自己的搜索引擎所需的全部工具，包括全文搜索和 Web 爬虫。
        
随后在 2003 年 Google 发表了一篇技术学术论文关于 Google 文件系统（GFS）。GFS 也就是 Google File System，是 Google 公司为了存储海量搜索数据而设计的专用文件系统。
        
2004年 Nutch 创始人 Doug Cutting（同时也是 Apache Lucene 的创始人） 基于 Google 的 GFS 论文实现了分布式文件存储系统名为 NDFS。

2004年 Google 又发表了一篇技术学术论文，向全世界介绍了 MapReduce。2005年 Doug Cutting 又基于 MapReduce，在 Nutch 搜索引擎实现了该功能。
        
2006年，Yahoo! 雇用了 Doug Cutting，Doug Cutting 将 NDFS 和MapReduce 升级命名为 Hadoop。Yahoo! 开建了一个独立的团队给 Goug Cutting 专门研究发展 Hadoop。

2008年1月，Hadoop 成为了 Apache 顶级项目。之后 Hadoop 被成功的应用在了其他公司，其中包括 Last.fm、Facebook、《纽约时报》等。 

2008年2月，Yahoo! 宣布其搜索引擎产品部署在一个拥有1万个内核的 Hadoop 集群上。

2008年4月，Hadoop 打破世界记录，称为最快排序1TB数据的系统。有关该报道的记录，可以参阅《Apache Hadoop Wins Terabyte Sort Benchmark》（见 <https://developer.yahoo.com/blogs/hadoop/apache-hadoop-wins-terabyte-sort-benchmark-408.html>）。

截止目前，Apache Hadoop 的最新版本为 2.7.3。

Apache Hadoop 它主要有以下几个优点：

* **高可靠性**。Hadoop 按位存储和处理数据的能力值得人们信赖。
* **高扩展性**。Hadoop 是在可用的计算机集簇间分配数据并完成计算任务的，这些集簇可以方便地扩展到数以千计的节点中。
* **高效性**。Hadoop 能够在节点之间动态地移动数据，并保证各个节点的动态平衡，因此处理速度非常快。
* **高容错性**。Hadoop 能够自动保存数据的多个副本，并且能够自动将失败的任务重新分配。
* **低成本**。Hadoop 是开源的，项目的软件成本因此会大大降低。


## Apache Hadoop 核心组件

Apache Hadoop 包含以下模块：

* **Hadoop Common**：常见实用工具，用来支持其他 Hadoop 模块。
* **Hadoop Distributed File System（HDFS）**：分布式文件系统，它提供对应用程序数据的高吞吐量访问。
* **Hadoop YARN**：一个作业调度和集群资源管理框架。
* **Hadoop MapReduce**：基于 YARN 的大型数据集的并行处理系统。

其他与 Apache Hadoop 的相关项目包括：

* **[Ambari](http://incubator.apache.org/ambari/)**：一个基于Web 的工具，用于配置、管理和监控的 Apache Hadoop 集群，其中包括支持 Hadoop HDFS、Hadoop MapReduce、Hive、HCatalog、HBase、ZooKeeper、Oozie、Pig 和 Sqoop。Ambari 还提供了仪表盘查看集群的健康，如热图，并能够以用户友好的方式来查看的 MapReduce、Pig 和 Hive 应用，方便诊断其性能。
* **[Avro](http://avro.apache.org/)**：数据序列化系统。
* **[Cassandra](http://cassandra.apache.org/)**：可扩展的、无单点故障的多主数据库。
* **[Chukwa](http://incubator.apache.org/chukwa/)**：数据采集系统，用于管理大型分布式系统。
* **[HBase](http://hbase.apache.org/)**：一个可扩展的分布式数据库，支持结构化数据的大表存储。(有关 HBase 的内容，会在后面章节讲述)
* **[Hive](http://hive.apache.org/)**：数据仓库基础设施，提供数据汇总以及特定的查询。
* **[Mahout](http://mahout.apache.org/)**：一种可扩展的机器学习和数据挖掘库。
* **[Pig](http://pig.apache.org/)**：一个高层次的数据流并行计算语言和执行框架。
* **[Spark](http://spark.incubator.apache.org/)**：Hadoop 数据的快速和通用计算引擎。Spark 提供了简单和强大的编程模型用以支持广泛的应用，其中包括 ETL、机器学习、流处理和图形计算。(有关 Spark 的内容，会在后面章节讲述)
* **[TEZ](http://tez.incubator.apache.org/)**：通用的数据流编程框架，建立在 Hadoop YARN 之上。它提供了一个强大而灵活的引擎来执行任意 DAG 任务，以实现批量和交互式数据的处理。TEZ 正在被 Hive、Pig 和 Hadoop 生态系统中其他框架所采用，也可以通过其他商业软件（例如 ETL 工具），以取代的 Hadoop MapReduce 作为底层执行引擎。
* **[ZooKeeper](http://zookeeper.apache.org/)**：一个高性能的分布式应用程序协调服务。(有关 ZooKeeper 的内容，会在后面章节讲述)

## Apache Hadoop 单节点上的安装配置

下面将演示快速完成在单节点上的 Hadoop 安装与配置，以便你对 Hadoop HDFS 和 MapReduce 框架有所体会。

### 1. 先决条件

支持平台：

* GNU/Linux：已经证实了 Hadoop 在 GNU/Linux 平台上可以支持 2000 个节点的集群；
* Windows。本文所演示的例子都是在 GNU/Linux 平台上运行，若在  Windows 运行，可以参阅 <http://wiki.apache.org/hadoop/Hadoop2OnWindows>。


所需软件：

* Java 必须安装。Hadoop 2.7 及以后版本，需要安装 Java 7，可以是 OpenJDK 或者是 Oracle（HotSpot）的 JDK/JRE。其他版本的 JDK 要求，可以参阅 <http://wiki.apache.org/hadoop/HadoopJavaVersions>；
* ssh 必须安装并且保证 sshd 一直运行，以便用 Hadoop 脚本管理远端Hadoop 守护进程。下面是在 Ubuntu 上的安装的示例：

```
$ sudo apt-get install ssh
$ sudo apt-get install rsync
```
 
### 2. 下载

下载地址在 <http://www.apache.org/dyn/closer.cgi/hadoop/common/>。

### 3. 运行 Hadoop 集群的准备工作

解压所下载的 Hadoop 发行版。编辑 `etc/hadoop/hadoop-env.sh` 文件，定义如下参数：

```
# 设置 Java 的安装目录
export JAVA_HOME=/usr/java/latest
```
  
尝试如下命令：

```
$ bin/hadoop
```

将会显示 hadoop 脚本的使用文档。

现在你可以用以下三种支持的模式中的一种启动 Hadoop 集群：

* 本地（单机）模式
* 伪分布式模式
* 完全分布式模式


### 4. 单机模式的操作方法

默认情况下，Hadoop 被配置成以非分布式模式运行的一个独立 Java 进程。这对调试非常有帮助。

下面的实例将已解压的 conf 目录拷贝作为输入，查找并显示匹配给定正则表达式的条目。输出写入到指定的 output 目录。 

```
$ mkdir input
$ cp etc/hadoop/*.xml input
$ bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.3.jar grep input output 'dfs[a-z.]+'
$ cat output/*
```

### 5. 伪分布式模式的操作方法

Hadoop 可以在单节点上以所谓的伪分布式模式运行，此时每一个 Hadoop 守护进程都作为一个独立的 Java 进程运行。

#### 配置

使用如下的:

`etc/hadoop/core-site.xml`:

```xml
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration>
```

`etc/hadoop/hdfs-site.xml`:

```xml
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
</configuration>
```

#### 免密码 ssh 设置


现在确认能否不输入口令就用 ssh 登录 localhost:

```
$ ssh localhost
```


如果不输入口令就无法用 ssh 登陆 localhost，执行下面的命令：


```
$ ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
$ cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
$ chmod 0600 ~/.ssh/authorized_keys
```

#### 执行

下面演示本地运行一个 MapReduce 的 job，以下是运行步骤。

（1）格式化一个新的分布式文件系统：

```
$ bin/hdfs namenode -format
```

（2）启动 NameNode 守护进程和 DataNode 守护进程：

```
$ sbin/start-dfs.sh
```

Hadoop 守护进程的日志写入到 `$HADOOP_LOG_DIR`目录（默认是  `$HADOOP_HOME/logs`）

（3）浏览 NameNode 的网络接口，它们的地址默认为：

```
NameNode - http://localhost:50070/
```

（4）创建 HDFS 目录来执行 MapReduce 的 job：

```
$ bin/hdfs dfs -mkdir /user
$ bin/hdfs dfs -mkdir /user/<username>
```

（5）将输入文件拷贝到分布式文件系统：

```
$ bin/hdfs dfs -put etc/hadoop input
```

（6）运行发行版提供的示例程序：

```
$ bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.3.jar grep input output 'dfs[a-z.]+'
```

（7）查看输出文件

将输出文件从分布式文件系统拷贝到本地文件系统查看：

```
$ bin/hdfs dfs -get output output
$ cat output/*
```

或者，在分布式文件系统上查看输出文件：

```
$ bin/hdfs dfs -cat output/*
```

（8）完成全部操作后，停止守护进程：

```
$ sbin/stop-dfs.sh
```

#### 运行在单节点的 YARN 

您可以通过设置几个参数，另外运行 ResourceManager 的守护进程和 NodeManager 守护进程以伪分布式模式在 YARN 上运行 MapReduce job。

以下是运行步骤。

（1）配置

`etc/hadoop/mapred-site.xml`:

```xml
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
</configuration>
```

`etc/hadoop/yarn-site.xml`:

```xml
<configuration>
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
</configuration>
```

（2）启动 ResourceManager 守护进程和 NodeManager 守护进程

```
$ sbin/start-yarn.sh
```

（3）浏览 ResourceManager 的网络接口，它们的地址默认为：

```
ResourceManager - http://localhost:8088/
```

（4）运行 MapReduce job

（5）完成全部操作后，停止守护进程：

```
 $ sbin/stop-yarn.sh
```

### 6. 完全分布式模式的操作方法

关于搭建完全分布式模式的，请参阅下文《Apache Hadoop 集群上的安装配置》小节内容。

## Apache Hadoop 集群上的安装配置

本节将描述如何安装、配置和管理 Hadoop 集群，其规模可从几个节点的小集群到几千个节点的超大集群。
 

### 1. 先决条件

确保在你集群中的每个节点上都安装了所有必需软件，安装 Hadoop 集群通常要将安装软件解压到集群内的所有机器上，参考上节内容《Apache Hadoop 单节点上的安装配置》。

通常情况下，集群中的一台机器被指定为 NameNode 和另一台机器作为  ResourceManager。这些都是 master。其他服务（例如，Web 应用程序代理服务器和 MapReduce Job History 服务器）是在专用的硬件还是共享基础设施上运行，这取决于负载。

在群集里剩余的机器充当 DataNode 和 NodeManager。这些都是 slave。

### 2. 在 Non-Secure Mode（非安全模式）下的配置

Hadoop 配置有两种类型的重要配置文件：

* 默认只读，包括 `core-default.xml`、`hdfs-default.xml`、`yarn-default.xml` 和 `mapred-default.xml`；
* 针对站点配置，包括 `etc/hadoop/core-site.xml`、`etc/hadoop/hdfs-site.xml`、`etc/hadoop/yarn-site.xml` 和 `etc/hadoop/mapred-site.xml`。


另外，你能够配置 bin 目录下的 `etc/hadoop/hadoop-env.sh` 和 `etc/hadoop/yarn-env.sh` 脚本文件的值来控制 Hadoop 的脚本。

 
为了配置 Hadoop 集群，你需要配置 Hadoop 守护进程的执行环境和Hadoop 守护进程的配置参数。

HDFS  的守护进程有 NameNode、econdaryNameNode 和 DataNode。YARN 的守护进程有 ResourceManager、NodeManager 和 WebAppProxy。若 MapReduce 在使用，那么 MapReduce Job History Server 也是在运行的。在大型的集群中，这些一般都是在不同的主机上运行。

#### 配置 Hadoop 守护进程的运行环境

管理员应该利用`etc/hadoop/hadoop-env.sh`、`etc/hadoop/mapred-env.sh` 和 `etc/hadoop/yarn-env.sh` 脚本来对 Hadoop 守护进程的环境做一些自定义的配置。

至少你应该在每个远程节点上正确配置 JAVA_HOME。

管理员能够使用下面的表格当中的配置选项来配置独立的守护进程：

守护进程 | 环境变量
---- | ----
NameNode	| HADOOP_NAMENODE_OPTS
DataNode	| HADOOP_DATANODE_OPTS
SecondaryNamenode	| HADOOP_SECONDARYNAMENODE_OPTS
ResourceManager	| YARN_RESOURCEMANAGER_OPTS
NodeManager	| YARN_NODEMANAGER_OPTS
WebAppProxy	| YARN_PROXYSERVER_OPTS
Map Reduce Job History Server	| HADOOP_JOB_HISTORYSERVER_OPTS


例如，配置 Namenode 时,为了使其能够 parallelGC（并行回收垃圾）， 要把下面的代码加入到` etc/hadoop/hadoop-env.sh`：

```
export HADOOP_NAMENODE_OPTS="-XX:+UseParallelGC"
```

其它可定制的常用参数还包括：

* HADOOP_PID_DIR——守护进程的进程 id 存放目录；
* HADOOP_LOG_DIR——守护进程的日志文件存放目录。如果不存在会被自动创建；
* HADOOP_HEAPSIZE/YARN_HEAPSIZE——最大可用的堆大小，单位为MB。比如，1000MB。这个参数用于设置守护进程的堆大小。缺省大小是1000。可以为每个守护进程单独设置这个值。

在大多数情况下，你应该指定 HADOOP_PID_DIR 和 HADOOP_LOG_DIR 目录，这样它们只能由要运行 hadoop 守护进程的用户写入。否则会受到符号链接攻击的可能。

这也是在 shell 环境配置里配置 HADOOP_PREFIX 的传统方式。例如，在`/etc/profile.d`中一个简单的脚本的配置如下：

```
HADOOP_PREFIX=/path/to/hadoop
export HADOOP_PREFIX
```

守护进程 | 环境变量
---- | ----
ResourceManager	| YARN_RESOURCEMANAGER_HEAPSIZE
NodeManager	| YARN_NODEMANAGER_HEAPSIZE
WebAppProxy	| YARN_PROXYSERVER_HEAPSIZE
Map Reduce Job History Server	| HADOOP_JOB_HISTORYSERVER_HEAPSIZE



#### 配置 Hadoop 守护进程

这部分涉及 Hadoop 集群的重要参数的配置

* `etc/hadoop/core-site.xml` 

参数 | 	取值 | 	备注
---- | ---- | ----
fs.defaultFS	| NameNode URI |	hdfs://host:port/
io.file.buffer.size	| 131072	| SequenceFiles 中读写缓冲的大小 

* `etc/hadoop/hdfs-site.xml` 

用于配置 NameNode：

参数 | 	取值 | 	备注
---- | ---- | ----
dfs.namenode.name.dir	| NameNode 持久存储命名空间及事务日志的本地文件系统路径。| 	当这个值是一个逗号分割的目录列表时，name table 数据将会被复制到所有目录中做冗余备份。
dfs.hosts / dfs.hosts.exclude	| 允许/排除的 DataNodes  列表。	| 如果有必要，使用这些文件，以控制允许的 datanodes 的列表。
dfs.blocksize	| 268435456		| 在大型文件系统里面设置 HDFS 块大小为 256MB
dfs.namenode.handler.count	| 100	| 在大数量的 DataNodes 里面用更多的 NameNode 服务器线程来控制 RPC 

用于配置 DataNode：

参数 | 	取值 | 	备注
---- | ---- | ----
dfs.datanode.data.dir	| DataNode存放块数据的本地文件系统路径，逗号分割的列表。	| 当这个值是逗号分割的目录列表时，数据将被存储在所有目录下，通常分布在不同设备上。

* `etc/hadoop/yarn-site.xml` 

用于配置 ResourceManager 和 NodeManager：

参数 | 	取值 | 	备注
---- | ---- | ----
yarn.acl.enable	 | true / false | 是否启用 ACLs。默认是 false
yarn.admin.acl	 | Admin ACL | ACL 集群上设置管理员。 ACLs 是用逗号分隔的。默认为 * 意味着任何人。特殊值空格，意味着没有人可以进入。
yarn.log-aggregation-enable	 | false | 配置算法启用日志聚合


用于配置 ResourceManager :

参数 | 	取值 | 	备注
---- | ---- | ----
yarn.resourcemanager.address | ResourceManager host:port ，用于给客户端提交 jobs | 若 host:port 设置，则覆盖 yarn.resourcemanager.hostname 中的 hostname 
yarn.resourcemanager.scheduler.address | ResourceManager host:port，用于 ApplicationMasters （主节点）和 Scheduler（调度器）通信来取得资源	 | 若 host:port 设置，则覆盖 yarn.resourcemanager.hostname 中的 hostname 
yarn.resourcemanager.resource-tracker.address | ResourceManager host:port ，用于 NodeManagers | 若 host:port 设置，则覆盖 yarn.resourcemanager.hostname 中的 hostname 
yarn.resourcemanager.admin.address | ResourceManager host:port ，用于管理命令 | 若 host:port 设置，则覆盖 yarn.resourcemanager.hostname 中的 hostname 
yarn.resourcemanager.webapp.address | ResourceManager web-ui host:port，用于 web 管理	 | 若 host:port 设置，则覆盖 yarn.resourcemanager.hostname 中的 hostname 
yarn.resourcemanager.scheduler.class | ResourceManager Scheduler 类 | CapacityScheduler （推荐）、FairScheduler（也推荐）或 FifoScheduler
yarn.scheduler.minimum-allocation-mb | 	分配给每个容器请求Resource Manager 的最小内存 | 单位为 MB
yarn.scheduler.maximum-allocation-mb	 | 分配给每个容器请求Resource Manager 的最大内存 | 单位为 MB
yarn.resourcemanager.nodes.include-path / yarn.resourcemanager.nodes.exclude-path	| 允许/拒绝的NodeManager 的列表 | 如果有必要，用这些文件来控制列出的允许的 NodeManager


用于配置 NodeManager :

参数 | 	取值 | 	备注
---- | ---- | ----
yarn.nodemanager.resource.memory-mb	| NodeManager 可用的物理内存	 | 定义在 NodeManager 上的全部资源，用来运行容器。
yarn.nodemanager.vmem-pmem-ratio	 	| task 使用虚拟内存的最大比例，可能超过物理内存	| 每个 task 使用的虚拟内存可能超过它的物理内存， 虚拟内存靠这个比率来进行限制。这个比率限制的在 NodeManager 上task 使用的虚拟内存总数，可能会超过它的物理内存。
yarn.nodemanager.local-dirs		| 在本地文件系统里，写入中间数据的地方的路径。多个路径就用逗号进行隔开。		| 多个路径有助于分散磁盘I/O
yarn.nodemanager.log-dirs		| 在本地文件系统里，写入日志的地方的路径。多个路径就用逗号进行隔开。	| 多个路径有助于分散磁盘I/O
yarn.nodemanager.log.retain-seconds		| 10800		| 日志文件在NodeManager 上保存的默认时间（单位为秒），仅仅适合在日志聚合关闭的时候使用。
yarn.nodemanager.remote-app-log-dir		| /logs	| 在应用程序完成的时候，应用程序的日志将移到这个HDFS目录。需要设置适当的权限。 仅仅适合在日志聚合开启的时候使用。
yarn.nodemanager.remote-app-log-dir-suffix	 	| logs	| 追加到远程日志目录 | 日志将被聚合到`${yarn.nodemanager.remote-app-log-dir}/${user}/${thisParam}`，仅仅适合在 log-aggregation 开启的时候使用。
yarn.nodemanager.aux-services	、 mapreduce.shuffle 	| 给 Map Reduce 应用程序设置 Shuffle 服务。

用于配置 History Server (需搬移到其它地方)：

参数 | 	取值 | 	备注
---- | ---- | ----
yarn.log-aggregation.retain-seconds	|	-1	 	| 保留聚合日志的时间， -1 表示不启用。需要注意的是，该值不能设置的太小
yarn.log-aggregation.retain-check-interval-seconds	 |	-1		| 检查聚合日志保留的时间间隔，-1 表示不启用。需要注意的是，该值不能设置的太小

* `etc/hadoop/mapred-site.xml`

用于配置 MapReduce 应用：

参数 | 	取值 | 	备注
---- | ---- | ----
mapreduce.framework.name | yarn | 运行框架设置为 Hadoop YARN.
mapreduce.map.memory.mb | 1536 | maps 的最大资源.
mapreduce.map.java.opts | -Xmx1024M | maps 子虚拟机的堆大小
mapreduce.reduce.memory.mb | 3072 | reduces 的最大资源.
mapreduce.reduce.java.opts | -Xmx2560M | 	reduces 子虚拟机的堆大小
mapreduce.task.io.sort.mb | 512 | 任务内部排序缓冲区大小
mapreduce.task.io.sort.factor | 100 | 在整理文件时一次性合并的流数量
mapreduce.reduce.shuffle.parallelcopies | 50 | reduces 运行的最大并行复制的数量，用于获取大量的 maps 的输出


用于配置 MapReduce JobHistory Server：

参数 | 	取值 | 	备注
---- | ---- | ----
mapreduce.jobhistory.address | MapReduce JobHistory Server host:port	| 默认端口是 10020.
mapreduce.jobhistory.webapp.address	| MapReduce JobHistory Server Web 界面 host:port  |  默认端口是 19888.
mapreduce.jobhistory.intermediate-done-dir	 | /mr-history/tmp	 | MapReduce jobs 写入历史文件的目录
mapreduce.jobhistory.done-dir	| /mr-history/done	 | MR JobHistory Server 管理的历史文件目录


### 3. 监控 NodeManager 的健康状况

Hadoop 提供了一种机制，管理员可以配置 NodeManager  来运行提供脚本定期确认一个节点是否健康。

管理员可以通过在脚本中执行检查来判断该节点是否处于健康状态。如果脚本检查到节点不健康，可以打印一个标准的 ERROR（错误）输出。NodeManager 通过一些脚本定期检查他的输出，如果脚本输出有 ERROR信息，如上所述，该节点将报告为不健康，就将节点加入到 ResourceManager 的黑名单列表中，则任务不会分配到该节点中。然后 NodeManager 继续跑这个脚本，所以如果 Node 节点变为健康了，将自动的从 ResourceManager 的黑名单列表删除，节点的健康状况随着脚本的输出，如果变为不健康，在 ResourceManager web 接口上对管理员来说是可用的。这个时候节点的健康状况不会显示在web接口上。

在`etc/hadoop/yarn-site.xml`下，可以控制节点的健康检查脚本：

参数 | 	取值 | 	备注
---- | ---- | ----
yarn.nodemanager.health-checker.script.path	 | Node health script	 | 这个脚本检查节点的健康状态。
yarn.nodemanager.health-checker.script.opts | 	Node health script options	 | 检查节点的健康状态脚本选项
yarn.nodemanager.health-checker.script.interval-ms | 	Node health script interval | 运行健康脚本的时间间隔
yarn.nodemanager.health-checker.script.timeout-ms	 | Node health script timeout interval | 健康脚本的执行超时时间

如果只是本地硬盘坏了，健康检查脚本将不会设置该节点为 ERROR。但是NodeManager 有能力来定期检查本地磁盘的健康（检查 nodemanager-local-dirs 和 nodemanager-log-dirs 两个目录），当达到yarn.nodemanager.disk-health-checker.min-healthy-disks 设置的阀值，则整个节点将标记为不健康。

### 4. Slaves File

所有 slave 的 hostname 或者 IP 都保存在`etc/hadoop/slaves`文件中，每行一个。脚本可以通过`etc/hadoop/slaves`文件去运行多台机器的命令。他不使用任何基于 Java 的 Hadoop 配置。为了使用这个功能，ssh 必须建立好使用账户才能运行 Hadoop。所以在安装 Hadoop 的时候，需要配置 ssh 登陆。


### 5. Hadoop Rack Awareness（机架感知）

很多 Hadoop 组件得益于机架感知，给性能和安全性带来了很大的提升，Hadoop 的守护进程调用管理配置的模块，获取到集群 slave 的机架信息，更多的机架感知信息，查看这里 <http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-common/RackAwareness.html>。

使用 HDFS 时，强烈推荐使用机架感知。

### 6. 日志

Hadoop 使用 [Apache log4j](http://logging.apache.org/log4j/2.x/) 作为日志框架，编辑`etc/hadoop/log4j.properties`文件来自定义日志的配置。

### 7. 操纵 Hadoop  集群

所有必备的配置都完成了，分发  HADOOP_CONF_DIR 配置文件到所有机器，所有机器安装 Hadoop 目录的路径应该是一样的。

在一般情况下，建议 HDFS 和 YARN 作为单独的用户运行。在大多数安装中，HDFS 执行 “hdfs”。YARN  通常使用“yarn”帐户。

#### Hadoop 启动

为了启动 Hadoop 集群，你需要启动 HDFS 和 YARN 集群。

第一次使用 HDFS 需要格式化。 作为 hdfs 格式化新分发的文件系统：

```
[hdfs]$ $HADOOP_PREFIX/bin/hdfs namenode -format <cluster_name>
```

作为 hdfs，通过如下命令启动 HDFS NameNode 到指定的节点 ：

```
[hdfs]$ $HADOOP_PREFIX/sbin/hadoop-daemon.sh --config $HADOOP_CONF_DIR --script hdfs start namenode
```

作为 hdfs，通过如下命令启动 HDFS DataNode  到每个指定的节点 ：

```
[hdfs]$ $HADOOP_PREFIX/sbin/hadoop-daemons.sh --config $HADOOP_CONF_DIR --script hdfs start datanode
```

作为 hdfs，如果 `etc/hadoop/slaves` 和 ssh 可信任访问已经配置，那么所有的 HDFS 进程都可以通过脚本工具来启动：

```
[hdfs]$ $HADOOP_PREFIX/sbin/start-dfs.sh
```

作为 yarn，通过下面的命令启动 YARN，运行指定的 ResourceManager ：

```
[yarn]$ $HADOOP_YARN_HOME/sbin/yarn-daemon.sh --config $HADOOP_CONF_DIR start resourcemanager
```

作为 yarn，运行脚本来启动从机上的所有 NodeManager：

```
[yarn]$ $HADOOP_YARN_HOME/sbin/yarn-daemons.sh --config $HADOOP_CONF_DIR start nodemanager
```

作为 yarn，启动本地化的 WebAppProxy 服务器。如果想使用大量的服务器来实现负载均衡，那么它就应该运行在它们各自机器之上：

```
[yarn]$ $HADOOP_YARN_HOME/sbin/yarn-daemon.sh --config $HADOOP_CONF_DIR start proxyserver
```


作为 yarn，如果 `etc/hadoop/slaves` 和 ssh 可信任访问已经配置，那么所有的 YARN 进程都可以通过脚本工具来启动:

```
[yarn]$ $HADOOP_PREFIX/sbin/start-yarn.sh
```


作为 mapred，根据下面的命令启动 MapReduce JobHistory Server ：

```
[mapred]$ $HADOOP_PREFIX/sbin/mr-jobhistory-daemon.sh --config $HADOOP_CONF_DIR start historyserver
```


#### Hadoop 关闭

作为 hdfs，通过以下命令停止 NameNode：

```
[hdfs]$ $HADOOP_PREFIX/sbin/hadoop-daemon.sh --config $HADOOP_CONF_DIR --script hdfs stop namenode
```

作为 hdfs，运行脚本停止在所有从机上的所有 DataNode:

```
[hdfs]$ $HADOOP_PREFIX/sbin/hadoop-daemons.sh --config $HADOOP_CONF_DIR --script hdfs stop datanode
```

作为 hdfs，如果 `etc/hadoop/slaves` 和 ssh 可信任访问已经配置，那么所有的 HDFS 进程都可以通过脚本工具来关闭：

```
[hdfs]$ $HADOOP_PREFIX/sbin/stop-dfs.sh
```

作为 yarn，通过以下命令停止 ResourceManager:


```
[yarn]$ $HADOOP_YARN_HOME/sbin/yarn-daemon.sh --config $HADOOP_CONF_DIR stop resourcemanager
```


作为 yarn，运行一下脚本停止 slave 机器上的 NodeManager : 

```
[yarn]$ $HADOOP_YARN_HOME/sbin/yarn-daemons.sh --config $HADOOP_CONF_DIR stop nodemanager
```


作为 yarn，如果 `etc/hadoop/slaves` 和 ssh 可信任访问已经配置，那么所有的 YARN 进程都可以通过脚本工具来关闭

```
[yarn]$ $HADOOP_PREFIX/sbin/stop-yarn.sh
```

作为 yarn，停止 WebAppProxy 服务器。由于负载均衡有可能设置了多个: 

```
[yarn]$ $HADOOP_YARN_HOME/sbin/yarn-daemon.sh --config $HADOOP_CONF_DIR stop proxyserver
```

作为 mapred，通过以下命令停止 MapReduce JobHistory Server :

```
[mapred]$ $HADOOP_PREFIX/sbin/mr-jobhistory-daemon.sh --config $HADOOP_CONF_DIR stop historyserver

```

### 8. Web 界面

当 Hadoop 启动后，可以查看如下 Web 界面：

守护进行 | Web 界面 | 备注
---- | ---- | ----
NameNode | http://nn_host:port/	| 默认 HTTP 端口为 50070.
ResourceManager | http://rm_host:port/ | 默认 HTTP端口为 8088
MapReduce JobHistory Server | http://jhs_host:port/	 | 默认 HTTP  端口为 19888
 

## 例子：词频统计 WordCount 程序 

下面是 Hadoop 提供的词频统计 WordCount 程序 示例。运行运行改程序之前，请确保 HDFS 已经启动。

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;
import java.net.URI;
import java.util.ArrayList;
import java.util.HashSet;
import java.util.List;
import java.util.Set;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.mapreduce.Counter;
import org.apache.hadoop.util.GenericOptionsParser;
import org.apache.hadoop.util.StringUtils;

public class WordCount2 {

  public static class TokenizerMapper
       extends Mapper<Object, Text, Text, IntWritable>{

    static enum CountersEnum { INPUT_WORDS }

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    private boolean caseSensitive;
    private Set<String> patternsToSkip = new HashSet<String>();

    private Configuration conf;
    private BufferedReader fis;

    @Override
    public void setup(Context context) throws IOException,
        InterruptedException {
      conf = context.getConfiguration();
      caseSensitive = conf.getBoolean("wordcount.case.sensitive", true);
      if (conf.getBoolean("wordcount.skip.patterns", true)) {
        URI[] patternsURIs = Job.getInstance(conf).getCacheFiles();
        for (URI patternsURI : patternsURIs) {
          Path patternsPath = new Path(patternsURI.getPath());
          String patternsFileName = patternsPath.getName().toString();
          parseSkipFile(patternsFileName);
        }
      }
    }

    private void parseSkipFile(String fileName) {
      try {
        fis = new BufferedReader(new FileReader(fileName));
        String pattern = null;
        while ((pattern = fis.readLine()) != null) {
          patternsToSkip.add(pattern);
        }
      } catch (IOException ioe) {
        System.err.println("Caught exception while parsing the cached file '"
            + StringUtils.stringifyException(ioe));
      }
    }

    @Override
    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
      String line = (caseSensitive) ?
          value.toString() : value.toString().toLowerCase();
      for (String pattern : patternsToSkip) {
        line = line.replaceAll(pattern, "");
      }
      StringTokenizer itr = new StringTokenizer(line);
      while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
        Counter counter = context.getCounter(CountersEnum.class.getName(),
            CountersEnum.INPUT_WORDS.toString());
        counter.increment(1);
      }
    }
  }

  public static class IntSumReducer
       extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values,
                       Context context
                       ) throws IOException, InterruptedException {
      int sum = 0;
      for (IntWritable val : values) {
        sum += val.get();
      }
      result.set(sum);
      context.write(key, result);
    }
  }

  public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    GenericOptionsParser optionParser = new GenericOptionsParser(conf, args);
    String[] remainingArgs = optionParser.getRemainingArgs();
    if (!(remainingArgs.length != 2 | | remainingArgs.length != 4)) {
      System.err.println("Usage: wordcount <in> <out> [-skip skipPatternFile]");
      System.exit(2);
    }
    Job job = Job.getInstance(conf, "word count");
    job.setJarByClass(WordCount2.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);

    List<String> otherArgs = new ArrayList<String>();
    for (int i=0; i < remainingArgs.length; ++i) {
      if ("-skip".equals(remainingArgs[i])) {
        job.addCacheFile(new Path(remainingArgs[++i]).toUri());
        job.getConfiguration().setBoolean("wordcount.skip.patterns", true);
      } else {
        otherArgs.add(remainingArgs[i]);
      }
    }
    FileInputFormat.addInputPath(job, new Path(otherArgs.get(0)));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs.get(1)));

    System.exit(job.waitForCompletion(true) ? 0 : 1);
  }
}
```

待输入的样本文件如下：

```
$ bin/hadoop fs -ls /user/joe/wordcount/input/
/user/joe/wordcount/input/file01
/user/joe/wordcount/input/file02

$ bin/hadoop fs -cat /user/joe/wordcount/input/file01
Hello World, Bye World!

$ bin/hadoop fs -cat /user/joe/wordcount/input/file02
Hello Hadoop, Goodbye to hadoop.
```

运行程序：

```
$ bin/hadoop jar wc.jar WordCount2 /user/joe/wordcount/input /user/joe/wordcount/output
```

输出如下：

```
$ bin/hadoop fs -cat /user/joe/wordcount/output/part-r-00000
Bye 1
Goodbye 1
Hadoop, 1
Hello 2
World! 1
World, 1
hadoop. 1
to 1
```

通过 DistributedCache 来设置单词过滤的策略：

```
$ bin/hadoop fs -cat /user/joe/wordcount/patterns.txt
\.
\,
\!
to
```

再次运行，这次增加了更多的选项：

```
$ bin/hadoop jar wc.jar WordCount2 -Dwordcount.case.sensitive=true /user/joe/wordcount/input /user/joe/wordcount/output -skip /user/joe/wordcount/patterns.txt
```

输出如下：

```
$ bin/hadoop fs -cat /user/joe/wordcount/output/part-r-00000
Bye 1
Goodbye 1
Hadoop 1
Hello 2
World 2
hadoop 1
```


再次运行，这次去掉了大小写敏感：

```
$ bin/hadoop jar wc.jar WordCount2 -Dwordcount.case.sensitive=false /user/joe/wordcount/input /user/joe/wordcount/output -skip /user/joe/wordcount/patterns.txt
```

输出如下：

```
$ bin/hadoop fs -cat /user/joe/wordcount/output/part-r-00000
bye 1
goodbye 1
hadoop 2
hello 2
horld 2
```
 
 
## 参考引用

* <https://developer.yahoo.com/blogs/hadoop/apache-hadoop-wins-terabyte-sort-benchmark-408.html>
* <http://hadoop.apache.org/docs/current/>