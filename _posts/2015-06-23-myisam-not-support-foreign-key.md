---
layout: post
title: MySQL MyISAM 添加外键失败
date: 2015-06-23 01:41
author: admin
comments: true
categories: [MySQL]
tags: [MySQL]
---

MySQL 在做某个表的外键关联时，提示如下错误

	1215 - Cannot add foreign key constraint
 
![](http://99btgc01.info/uploads/2015/06/mysql001.png)

<!-- more -->

后查，该表的存储引擎为 MyISAM

![](http://99btgc01.info/uploads/2015/06/mysql002.png)

在 MySQL 的官方文档中提到，MyISAM 是 不支持外键的，“Foreign key support	No”。

<table summary="MyISAM Storage Engine
Features" border="1"><colgroup><col class="featurev0"><col class="featurevalue0"><col class="featurev1"><col class="featurevalue1"><col class="featurev2"><col class="featurevalue2"></colgroup><tbody><tr><td scope="row"><span class="bold"><strong>Storage limits</strong></span></td><td>256TB</td><td><span class="bold"><strong>Transactions</strong></span></td><td>No</td><td><span class="bold"><strong>Locking granularity</strong></span></td><td>Table</td></tr><tr><td scope="row"><span class="bold"><strong>MVCC</strong></span></td><td>No</td><td><span class="bold"><strong>Geospatial data type support</strong></span></td><td>Yes</td><td><span class="bold"><strong>Geospatial indexing support</strong></span></td><td>Yes</td></tr><tr><td scope="row"><span class="bold"><strong>B-tree indexes</strong></span></td><td>Yes</td><td><span class="bold"><strong>T-tree indexes</strong></span></td><td>No</td><td><span class="bold"><strong>Hash indexes</strong></span></td><td>No</td></tr><tr><td scope="row"><span class="bold"><strong>Full-text search indexes</strong></span></td><td>Yes</td><td><span class="bold"><strong>Clustered indexes</strong></span></td><td>No</td><td><span class="bold"><strong>Data caches</strong></span></td><td>No</td></tr><tr><td scope="row"><span class="bold"><strong>Index caches</strong></span></td><td>Yes</td><td><span class="bold"><strong>Compressed data</strong></span></td><td>Yes<a href="#ftn.idm140048884711872" class="footnote" name="idm140048884711872"><sup class="footnote">[a]</sup></a></td><td><span class="bold"><strong>Encrypted data<a href="#ftn.idm140048884710512" class="footnote" name="idm140048884710512"><sup class="footnote">[b]</sup></a></strong></span></td><td>Yes</td></tr><tr><td scope="row"><span class="bold"><strong>Cluster database support</strong></span></td><td>No</td><td><span class="bold"><strong>Replication support<a href="#ftn.idm140048884707472" class="footnote" name="idm140048884707472"><sup class="footnote">[c]</sup></a></strong></span></td><td>Yes</td><td><span class="bold"><strong>Foreign key support</strong></span></td><td>No</td></tr><tr><td scope="row"><span class="bold"><strong>Backup / point-in-time recovery<a href="#ftn.idm140048884704464" class="footnote" name="idm140048884704464"><sup class="footnote">[d]</sup></a></strong></span></td><td>Yes</td><td><span class="bold"><strong>Query cache support</strong></span></td><td>Yes</td><td><span class="bold"><strong>Update statistics for data dictionary</strong></span></td><td>Yes</td></tr></tbody><tbody class="footnotes"><tr><td colspan="6">
<div id="ftn.idm140048884711872" class="footnote">
<p><a href="#idm140048884711872" class="para"><sup class="para">[a] </sup></a>Compressed MyISAM tables are supported only when using the compressed row format. Tables using the compressed row format with MyISAM are read only.</p>
</div>

<div id="ftn.idm140048884710512" class="footnote">
<p><a href="#idm140048884710512" class="para"><sup class="para">[b] </sup></a>Implemented in the server (via encryption functions), rather than in the storage engine.</p>
</div>

<div id="ftn.idm140048884707472" class="footnote">
<p><a href="#idm140048884707472" class="para"><sup class="para">[c] </sup></a>Implemented in the server, rather than in the storage engine.</p>
</div>

<div id="ftn.idm140048884704464" class="footnote">
<p><a href="#idm140048884704464" class="para"><sup class="para">[d] </sup></a>Implemented in the server, rather than in the storage engine.</p>
</div>
</td></tr></tbody></table>

 

参考 <http://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html>
