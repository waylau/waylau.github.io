---
layout: post
title: SQLServer 查询每个表的行数
date: 2014-04-22 08:51
author: admin
comments: true
categories: [SQLServer]
tags: [SQLServer,查询,行数]

查询每个表的行数

```
select a.name AS name,b.rows AS rows INTO #Temp1
from EEMS.dbo.sysobjects a LEFT JOIN EEMS.dbo.sysindexes b
ON( a.id=b.id ) WHERE a.type='u' ORDER BY b.rows DESC

SELECT name,max(rows) FROM #Temp1 GROUP BY NAME ORDER BY max(rows) DESC
```