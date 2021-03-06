---
layout: post
title: MySQL日期类型选择总结
description: 
category: code 
tags: [ MySQL , time  ]
---
{% include JB/setup %}

近期在做产品的性能调优，发现了很多产品很多待改善的细节，尤其是后台的逻辑和算法，很多经验需要去总结。

经过测试，如果取消后台数据3个主日志表的所有字段的所有索引（保留主键），系统性能能提高一倍。
当前所有的日志表中都会有时间字段，且所有时间字段都会有索引和联合索引，需要仔细考虑优化。
本文主要对MySQL实际那类型进行总结。


MySQL 日期类型：日期格式、所占存储空间、日期范围 比较。 

```

日期类型        存储空间       日期格式                 日期范围 
------------ ---------   --------------------- ----------------------------------------- 
datetime       8 bytes   YYYY-MM-DD HH:MM:SS   1000-01-01 00:00:00 ~ 9999-12-31 23:59:59 
timestamp      4 bytes   YYYY-MM-DD HH:MM:SS   1970-01-01 00:00:01 ~ 2038 
date           3 bytes   YYYY-MM-DD            1000-01-01          ~ 9999-12-31 
year           1 bytes   YYYY                  1901                ~ 2155 

```

在 MySQL 中创建表时，对照上面的表格，很容易就能选择到合适自己的数据类型。不过到底是选择 datetime 还是 timestamp，可能会有点犯难。这两个日期时间类型各有优点：datetime 的日期范围比较大；timestamp 所占存储空间比较小，只是 datetime 的一半。 

另外，timestamp 类型的列还有个特性：默认情况下，在 insert, update 数据时，timestamp 列会自动以当前时间（CURRENT_TIMESTAMP）填充/更新。


一般情况下，我倾向于使用 datetime 日期类型。 

MySQL 时间类型：时间格式、所占存储空间、时间范围。 

```

时间类型        存储空间      时间格式                 时间范围 
------------ ---------   --------------------- ----------------------------------------- 
time           3 bytes   HH:MM:SS              -838:59:59          ~ 838:59:59 

```

这些日期时间类型只能支持到秒级别，不支持毫秒、微秒,毫秒、微秒需要单独用int型保存。 


