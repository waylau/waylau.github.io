---
layout: post
title: Oracle性能优化——NOT EXISTS改为LEFT JOIN性能提升649倍！
date: 2020-02-29 00:22
author: admin
comments: true
categories: [Oracle]
tags: [Oracle]
---

对Oracle进行性能优化，有时只需要做几个简单的转换，比如NOT EXISTS改为LEFT JOIN。

<!-- more -->


有一个SQL使用NOT EXISTS的方式，执行很慢很难出结果。脚本如下：

 

 
```sql
SELECT COUNT(1) FROM (

    SELECT /*+ full(t) */
     t.*
      FROM demands_t t
      LEFT JOIN items_i_t i
        ON t.demand_item = i.item_code
     WHERE t.demand_id IS NOT NULL
       AND t.batch_id = 51115  -- 入参
       AND t.max_bom_level = 3  -- 入参
       AND NOT EXISTS (SELECT /*+ parallel(kk, 10) */
             1
              FROM iscp_atp.pegging_t kk
             WHERE kk.batch_id = 51115
               AND kk.max_bom_level = 3
               AND kk.demand_id = t.demand_id)
       AND t.demand_date >= SYSDATE -7 * 7 -- 入参
       AND t.demand_date < SYSDATE + 1 * 7 -- 入参   
    )
```

改为了LEFT JOIN的方式，性能提升明显。相同数据量下，NOT EXISTS的方式耗时422秒，

 

而LEFT JOIN的方式耗时仅0.65秒，性能提升649倍！


优化后的脚本如下：

 
```sql
SELECT COUNT(1) FROM (

SELECT * FROM (
SELECT /*+ full(t) */
     t.*
      FROM demands_t t
      LEFT JOIN items_i_t i
        ON t.demand_item = i.item_code
     WHERE t.demand_id IS NOT NULL
       AND t.batch_id = 51115  -- 入参
       AND t.max_bom_level = 3  -- 入参
       AND t.demand_date >= SYSDATE -7 * 7 -- 入参
       AND t.demand_date < SYSDATE + 1 * 7 -- 入参
       ) tt left join
       pegging_t kk
       ON kk.demand_id = tt.demand_id
             WHERE kk.demand_id  is null
    )
```
