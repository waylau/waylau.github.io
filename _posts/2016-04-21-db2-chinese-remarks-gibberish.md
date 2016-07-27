---
layout: post
title: DB2 所有数据库表、表字段注释乱码问题的排查及解决方案
date: 2016-04-21 01:41
author: admin
comments: true
categories: [DB2]
tags: [DB2,中文,乱码]
---

问题背景：自2015年以来，国家项目测试平台 DB2 所有表的表名、表字段等中文注释均开始存在乱码问题，严重影响开发人员对于数据库表含义的理解（特别是在数据库设计文档缺乏的情况下）。

本文记录了解决该历史遗留问题的相关步骤，总结遇到的坑，最后提出解决该问题的方案。

<!-- more -->

## 本文所涉及到的环境


* 测试服务器：10.1.3.3:60000/NECC_GJR
    * CentOS release 6.3 (Final)
    * DB2 Express-C 10.1
* 线上服务器：10.30.22.16:60000/NECC_DB
    * SUSE Linux Enterprise Server 11 SP2  (x86_64)
    * DB2 Enterprise Server Edition 10.1
* 本地服务器：192.168.56.101:50000/NECC_DB
    * Oracle VM VirtualBox 5.0.16
    * openSUSE Leap 42.1
    * DB2 Express-C 10.5.7
* 其他客户端软件
    * Toad for DB2 Freeware 5.6
    * Xshell 5

## 数据库中文数据的情况
 
1. 线上数据库情况
 
中文数据显示，Xshell 正常

```sql
db2 => select name from sys_role

NAME                                              
--------------------------------------------------
政府查看员                                        
政府管理员                                        
政府审计员                                        
政府安全员                                        
业务管理员                                        

  5 record(s) selected.
```

2. 测试服务器情况
 
中文数据显示，Xshell 乱码

```sql
db2 => select name from sys_role

NAME                                              
--------------------------------------------------
·дþ񿵾񼞠                                      
·дþ´ʍ𞞠                                     
·дþ°Á´񼞠                                      

  3 record(s) selected.
  
```


中文数据显示，Toad 正常

![](http://99btgc01.info/uploads/2016/04/001.jpg)


## 数据库中文注释的情况
 
1. 线上数据库情况
 
中文注释显示，Xshell 正常

```sql
db2 => select remarks from syscat.columns where tabname=upper('sys_role')

REMARKS                                                                                                                                                                                                                                                       
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
角色状态，1：启用，0：禁用                                                                                                                                                                                                                                    
创建时间                                                                                                                                                                                                                                                      
创建者                                                                                                                                                                                                                                                        
描述                                                                                                                                                                                                                                                          
ID                                                                                                                                                                                                                                                            
NAME                                                                                                                                                                                                                                                          
角色值                                                                                                                                                                                                                                                        

  7 record(s) selected.
```


2. 测试服务器情况
 
中文注释显示，Xshell 乱码

```sql
db2 => select remarks from syscat.columns where tabname=upper('ba_process')

REMARKS                                                                                                                                                                                                                                                       
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
法人代񾀂A_ENTERPRISE.CODE                                                                                                                                                                                                                                
企业id;BA_ENTERPRISE.ID                                                                                                                                                                                                                                     
ID                                                                                                                                                                                                                                                            
񾠤º§工廴þ񾀄IC_INDUSTRY_PROCESS.CODE                                                                                                                                                                                                                    
񾠤º§工廴þ稴þIC_INDUSTRY_PROCESS.NAME                                                                                                                                                                                                                    
񾠤º§工廴þ񾐍                                                                                                                                                                                                                                             

  6 record(s) selected.
  
```


中文注释显示，Toad 乱码

![](http://99btgc01.info/uploads/2016/04/002.jpg)


## 数据库编码情况

1. 线上和本地编码相同，如下：

```sql
db2 => get db cfg

       Database Configuration for Database 

 Database configuration release level                    = 0x0f00
 Database release level                                  = 0x0f00

 Database territory                                      = US
 Database code page                                      = 1208
 Database code set                                       = UTF-8
 Database country/region code                            = 1
 Database collating sequence                             = IDENTITY
 Alternate collating sequence              (ALT_COLLATE) = 
 Number compatibility                                    = OFF
 Varchar2 compatibility                                  = OFF
 Date compatibility                                      = OFF
 Database page size                                      = 4096
 ```
 
2. 测试服务器编码


```sql
db2 => get db cfg

       Database Configuration for Database 

 Database configuration release level                    = 0x0f00
 Database release level                                  = 0x0f00

 Database territory                                      = CN
 Database code page                                      = 1208
 Database code set                                       = UTF-8
 Database country/region code                            = 86
 Database collating sequence                             = IDENTITY
 Alternate collating sequence              (ALT_COLLATE) = 
 Number compatibility                                    = OFF
 Varchar2 compatibility                                  = OFF
 Date compatibility                                      = OFF
 Database page size                                      = 4096
```

3. 总结

三者的编码都是 UTF-8 ,其中，差异在于 `Database territory` 和 `Database country/region code`


## 操作系统编码情况

执行如下指令：

```sql
app@db1:~> locale
LANG=en_US.UTF-8
LC_CTYPE="en_US.UTF-8"
LC_NUMERIC="en_US.UTF-8"
LC_TIME="en_US.UTF-8"
LC_COLLATE="en_US.UTF-8"
LC_MONETARY="en_US.UTF-8"
LC_MESSAGES="en_US.UTF-8"
LC_PAPER="en_US.UTF-8"
LC_NAME="en_US.UTF-8"
LC_ADDRESS="en_US.UTF-8"
LC_TELEPHONE="en_US.UTF-8"
LC_MEASUREMENT="en_US.UTF-8"
LC_IDENTIFICATION="en_US.UTF-8"
LC_ALL=
```

三个服务器的编码相同

## 线上服务器备份还原到本地（DB2 BACKUP/STORE）


1. 联机备份

```sql
db2inst@db1:~> db2 backup db necc_db online 

Backup successful. The timestamp for this backup image is : 20160418154302

db2inst@db1:~> ls
20160323_gov_db.tar.gz  export
backup                  group.ixf
bin                     leon.sql
db2                     NECC_DB.0.db2inst.DBPART000.20160418154302.001
db2inst                 necc_db.sql
db2out                  original.ixf
db2out.tar.gz           outport
db2temp                 output_20160323.tar.gz
db2.zip                 sqllib
enterprise.ixf          update_v3.sql
db2inst@db1:~> 


db2inst@db1:~> exit
logout
Connection to 10.1.3.3 closed. 
app@GJJN-QZ-01:~> scp db2inst@10.1.3.3:NECC_DB.0.db2inst.DBPART000.20160418154302.001 /home/app/patch
Password: 
NECC_DB.0.db2inst.DBPART000.20160418154302.001        100%  848MB 121.2MB/s   00:07    
app@GJJN-QZ-01:~> 


app@GJJN-QZ-02:~> scp -P 10022 app@59.252.16.36:/home/app/patch/NECC_DB.0.db2inst.DBPART000.20160418154302.001 /home/app/patch
Password: 
NECC_DB.0.db2inst.DBPART000.20160418154302.001        100%  848MB  10.9MB/s   01:18    
app@GJJN-QZ-02:~> 
```



![](http://99btgc01.info/uploads/2016/04/003.jpg)

放在要还原的主机的实例目录下

![](http://99btgc01.info/uploads/2016/04/004.jpg)

2. 坑

还原时，提示如下错误：

```sql
db2inst1@waylau:~> db2 restore db necc_db without rolling forward
SQL2547N  The database was not restored because the backup image is from a 
previous release and requires rollforward recovery.
```

3. 填坑：脱机备份

先检查是否有应用在连接数据库，如果有，先断开所有的应用

```
db2 list applications for db necc_db

db2inst@db1:~> db2 list applications for db necc_db

Auth Id  Application    Appl.      Application Id                                                 DB       # of
         Name           Handle                                                                    Name    Agents
-------- -------------- ---------- -------------------------------------------------------------- -------- -----
DB2INST  db2jcc_applica 55536      10.1.6.2.21385.160419031816                                    NECC_DB  1    
DB2INST  db2jcc_applica 54910      10.1.6.2.51666.160419031754                                    NECC_DB  1    

db2inst@db1:~> db2 force applications all
DB20000I  The FORCE APPLICATION command completed successfully.
DB21024I  This command is asynchronous and may not be effective immediately.

db2inst@db1:~> db2 list applications for db necc_db
SQL1611W  No data was returned by Database System Monitor.
db2inst@db1:~> 

db2 backup db necc_db
Backup successful. The timestamp for this backup image is : 20160419112126


db2 list history backup all for necc_db

```

生成 `NECC_DB.0.db2inst.DBPART000.20160419112126.001`

再次执行还原:

```
db2inst1@waylau:~> db2 list db directory
SQL1057W  The system database directory is empty.  SQLSTATE=01606
db2inst1@waylau:~> db2 create db necc_db
DB20000I  The CREATE DATABASE command completed successfully.
db2inst1@waylau:~> db2 list db directory

 System Database Directory

 Number of entries in the directory = 1

Database 1 entry:

 Database alias                       = NECC_DB
 Database name                        = NECC_DB
 Local database directory             = /home/db2inst1
 Database release level               = 10.00
 Comment                              =
 Directory entry type                 = Indirect
 Catalog database partition number    = 0
 Alternate server hostname            =
 Alternate server port number         =

db2inst1@waylau:~> db2 restore database necc_db from "/home/data" taken at 20160419112126  on "/home/db2inst1" dbpath on "/home/db2inst1" into necc_db
SQL2523W  Warning!  Restoring to an existing database that is different from 
the database on the backup image, but have matching names. The target database 
will be overwritten by the backup version.  The Roll-forward recovery logs 
associated with the target database will be deleted.
Do you want to continue ? (y/n) y
SQL2555I  The database was restored and then successfully upgraded to the 
current DB2 release where you issued the RESTORE DATABASE command.
db2inst1@waylau:~> 

```

4. 设置防火墙，允许数据库的端口号

执行：`vi /etc/sysconfig/SuSEfirewall2`

修改成如下内容：

```
FW_SERVICES_EXT_TCP="50000"
FW_SERVICES_EXT_UDP="50000"
```

5. 本地数据库情况

Xshell 、Toad 中文数据和字段注释均正常。

## 线上服务器备份还原到本地（DB2MOVE）

1. 用 DB2MOVE 形式还原数据库

获取到备份文件 db2temp，执行如下命令来还原，先导入生产表结构，再导入表数据：

```
waylau:/home/data # 
db2 -tvf /home/data/db2temp/necc_db.sql

 
db2inst1@waylau:~> cd /home/data/db2temp
db2inst1@waylau:/home/data/db2temp> db2move necc_db IMPORT
```

2. 本地数据库情况

Xshell 、Toad 中文数据和字段注释均正常。


3. 坑

有可能遇到端口不能启动的问题：

```
db2inst@waylau:~> db2set -all  
[g] DB2_COMPATIBILITY_VECTOR=MYS
[g] DB2SYSTEM=waylau.com
[g] DB2INSTDEF=db2inst1
```

设置一个变量 `db2comm=TCPIP`:

```
db2inst@waylau:~> db2set db2comm=TCPIP
db2inst@waylau:~> db2set -all  
[i] DB2COMM=TCPIP
[g] DB2_COMPATIBILITY_VECTOR=MYS
[g] DB2SYSTEM=waylau.com
[g] DB2INSTDEF=db2inst1
db2inst@waylau:~> 
```

## 总结

1. 按照几种方式对线上数据库在本地进行了备份还原，均未出现中文注释乱码问题；
2. 由于时间原因，未在本地重现上述问题，故推测该问题产生的原因是在数据库备份成 sql 文件时，该文件保存的编码格式对中文不友好而乱码，以至于再将该 sql 文件导入数据库时，致使数据库中文注释乱码；
3. 解决方案：尝试使用多种还原方式，将线上数据库在测试服务器上重新还原一个新的数据库，再查看是否有类似问题的产生。


## 后记

后期我在对线上数据库进行更新时，重现了该问题，在通过执行脚本文件形式来对数据库进行更新操作。当我的脚本文件编码格式对中文不友好时，执行后的数据就乱码了：

```
db2inst@db1:~> cat necc_sql_update_20160406.sql
/*
ط֟£º¸ΰπ
ʱ¼䣺2016-4-6

ў¸ŁɠWHITE_PAPER £º½«̹ԐµŖюŗ¢ˍ²¹ȫ£¬²¢½«̹ԐԐĬɏֵµŏΪ²»ԃĬɏֵ¡£

*/
---------ӔЂΪ SQL ½ű¾------------


COMMENT ON DB2INST.WHITE_PAPER ( 
    DHTB IS 'µ组ͬ±Ƨ,
    DLZLMB IS 'µ聦؜Ŀ±襬
    DWDHTB IS 'µ¥λµ组ͬ±Ƨ,
    ENERGY_HD IS 'º˵奬
    ENERGY_KZS IS '¿ʔډ
    ENERGY_MT IS 'ú̿',
    ENERGY_OTHER IS 'Ǥ̻',
    ENERGY_RQ IS 'ȼǸ',
    ENERGY_SD IS 'ˮµ奬
    ENERGY_SW_HD IS 'º˵芵ϯ',
    ENERGY_SW_KZS IS '¿ʔډ򶏯',
    ENERGY_SW_MT IS 'ú̿ʵϯ',
    ENERGY_SW_RQ IS 'ȼǸʵϯ',
    ENERGY_SW_SD IS 'ˮµ芵ϯ',
    ENERGY_SW_SY IS 'ʯԍʵϯ',
    ENERGY_SY IS 'ʯԍ',
    FIELD_GY IS '¹¤ҵ',
    FIELD_JT IS '½»ͨ',
    FIELD_JZ IS '½¨׾ҵ',
    FIELD_NY IS 'ũҵ',
    FIELD_OTHER IS 'Ǥ̻¬Բ',
    FIELD_SH IS 'ʺ»쥬
    FIELD_SY IS 'ʌҵ',
    GDPDH IS 'µ¥λ GDP µ组',
    GDPNH IS 'µ¥λ GDP Ŝº§,
    INDUSTRY_DL IS 'µ聦',
    INDUSTRY_GT IS '¸׌
    INDUSTRY_HG IS '»¯¹¤',
    INDUSTRY_JC IS '½¨²§,
    INDUSTRY_OTHER IS 'Ǥ̻¹¤ҵѐҵ',
    INDUSTRY_SH IS 'ʯ»¯',
    INDUSTRY_YS IS 'Ԑɫ',
    LJJD IS '[¼ƽ󆥬
    NHZLMB IS 'Ŝºŗ݁¿Ŀ±襬
    NXTB IS 'ŜЧͬ±ȣ¨µ¥λ GDP Ŝºō¬±ȣ©',
    REGION_CODE IS 'ȸԲ±ძ',
    REGION_NAME IS 'ȸԲĻ³ħ,
    REGION_TYPE IS 'ȸԲ`э',
    SEASON IS '¼¾¶Ʊ£¬2£¬3£¬4',
    WNMB IS 'ϥŪĿ±襬
    WQJD IS 'βǳ½󆥬
    YEAR IS 'Ū·ۧ,
    ZDH IS '؜µ组',
    ZHNH IS '؛ºЄܺ§,
    ZLTB IS '؜ͬ±ƿ(%)' );

```

而把文档格式保存为“UTF-8 无 BOM”的格式编码，显示正常：

```

app@GJJN-QZ-02:~> cat /home/app/patch/necc_sql_update_20160406.sql
/*
作者：柳伟卫
时间：2016-4-6

修改了 WHITE_PAPER ：将所有的中文注释补全，并将所有有默认值的项改为不用默认值。

*/
---------以下为 SQL 脚本------------

COMMENT ON DB2INST.WHITE_PAPER ( 
    DHTB IS '电耗同比',
    DLZLMB IS '电力总量目标',
    DWDHTB IS '单位电耗同比',
    ENERGY_HD IS '核电',
    ENERGY_KZS IS '可再生',
    ENERGY_MT IS '煤炭',
    ENERGY_OTHER IS '其他',
    ENERGY_RQ IS '燃气',
    ENERGY_SD IS '水电',
    ENERGY_SW_HD IS '核电实物量',
    ENERGY_SW_KZS IS '可再生实物量',
    ENERGY_SW_MT IS '煤炭实物量',
    ENERGY_SW_RQ IS '燃气实物量',
    ENERGY_SW_SD IS '水电实物量',
    ENERGY_SW_SY IS '石油实物量',
    ENERGY_SY IS '石油',
    FIELD_GY IS '工业',
    FIELD_JT IS '交通',
    FIELD_JZ IS '建筑业',
    FIELD_NY IS '农业',
    FIELD_OTHER IS '其他领域',
    FIELD_SH IS '生活',
    FIELD_SY IS '商业',
    GDPDH IS '单位 GDP 电耗',
    GDPNH IS '单位 GDP 能耗',
    INDUSTRY_DL IS '电力',
    INDUSTRY_GT IS '钢铁',
    INDUSTRY_HG IS '化工',
    INDUSTRY_JC IS '建材',
    INDUSTRY_OTHER IS '其他工业行业',
    INDUSTRY_SH IS '石化',
    INDUSTRY_YS IS '有色',
    LJJD IS '累计进度',
    NHZLMB IS '能耗总量目标',
    NXTB IS '能效同比（单位 GDP 能耗同比）',
    REGION_CODE IS '区域编码',
    REGION_NAME IS '区域名称',
    REGION_TYPE IS '区域类型',
    SEASON IS '季度1，2，3，4',
    WNMB IS '五年目标',
    WQJD IS '万企进度',
    YEAR IS '年份',
    ZDH IS '总电耗',
    ZHNH IS '综合能耗',
    ZLTB IS '总量同比?(%)' );

COMMIT;app@GJJN-QZ-02:~> 
```

![](http://99btgc01.info/uploads/2016/04/clipboard.png)

## 参考引用

* <http://www.ibm.com/support/knowledgecenter/SSEPGG_9.5.0/com.ibm.db2.luw.messages.sql.doc/doc/msql02547n.html>
* [《DB2 教程》](https://github.com/waylau/db2-tutorial)