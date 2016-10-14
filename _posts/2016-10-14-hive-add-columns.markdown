---
layout: post
title: "Hive中增加列隐藏的问题"
description: 
categories:
tags: 
---

有个hive表`id_zone`，存储按地理位置哈希值汇聚的订单信息，按天划分partition，表建好且跑了几天数据之后，想加经纬度两列进去，就执行了如下语句：

```sql
alter table mid_zone add column (geohash_lat string);
alter table mid_zone add column (geohash_lng string);
```

表见好之后，用`show create table mid_zone`,和`show columns in mid_zone`命令查看发现两列已经建进去了，就又重新跑了前几天的数据，想把经纬度值更新进去，但无论怎么执行，两列都是NULL，起初怀疑是解析地理位置哈希的UDF写的有问题，但测试了好几次都能正确解析出数值，后来又直接把经纬度写了两个固定的数值进去看看，但也写不进去，那就不是UDF的问题了。
后来突然想起之前看到过添加列语句后面可以加cascade关键字，特意查了下，如果不加这个的话，只会修改metadata，也就是新的partition会包含添加的字段，而不会更新旧的partitions，[官方说明](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL)是这样写的：

    The CASCADE|RESTRICT clause is available in Hive 0.15.0.
    ALTER TABLE CHANGE COLUMN with CASCADE command changes the columns of a table's metadata
     ,and cascades the same change to all the partition metadata. 
    RESTRICT is the default, limiting column change only to table metadata.

所以我又把这两列删掉，重新执行了如下命令就好了：

```sql
alter table mid_zone add column (geohash_lat string) cascade;
alter table mid_zone add column (geohash_lng string) cascade;
```
当然，如果partition特别多的话，执行起来会非常慢，因为要更新每个partition。
如果条件允许的话，将表drop掉之后重新create也是可以的。
