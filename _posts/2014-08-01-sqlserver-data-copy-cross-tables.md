---
layout: post
title: SQLserver不同数据库不同表之间的复制
date: 2014-08-01 07:40
author: admin
comments: true
categories: [SQLServer]
tags: [SQLServer]
---

1.将`EEMS`库中的`Dec_TownInfo`表中的数据 复制到 `OEM`库的`c_townInfo`中(`c_townInfo`表事先存在)，先要将表的主键设为自增长类型，且列出列名
 
	SET IDENTITY_INSERT  [OEM].[dbo].[c_townInfo] ON
	
	insert into  [OEM].[dbo].[c_townInfo] (id,name,city_id,code,logo) select * FROM [EEMS].[dbo].[Dec_TownInfo]
 

2.将`EEMS`库中的`Dec_TownInfo`表中的数据 复制到 `OEM`库的`c_townInfo`中(`c_townInfo`表事先不存在)

	
	SELECT * INTO  [OEM].[dbo].[c_townInfo] FROM [EEMS].[dbo].[Dec_TownInfo]
 

