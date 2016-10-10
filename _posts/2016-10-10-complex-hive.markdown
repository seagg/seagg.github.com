---
layout: post
title: "Hive中复杂count语句"
description: 
categories:
tags: 
---

最近用hive比较多，昨天需要统计数据表中满足特定条件的ip的个数，而且类似需求不止一个，最开始想的笨方法是每个需求搞个sql之后union起来，但太复杂，当时想要是能在count里的distinct基础上再加些条件就好了，之后就在网上找到了如下复杂count的示例。

```sql
SELECT
    type
    , count(*)
    , count(DISTINCT u)
    , count(CASE WHEN plat=1 THEN u ELSE NULL END)
    , count(DISTINCT CASE WHEN plat=1 THEN u ELSE NULL END)
    , count(CASE WHEN (type=2 OR type=6) THEN u ELSE NULL END)
    , count(DISTINCT CASE WHEN (type=2 OR type=6) THEN u ELSE NULL END)
FROM t
WHERE dt in ("2012-1-12-02", "2012-1-12-03")
GROUP BY type
ORDER BY type;
```
