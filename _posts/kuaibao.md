

【余额宝快报】微信公众平台数据分析报告
========================================================

2013-10-05
--------------------------

1 概述
-----------------------





### 1.1  报告所涉数据来源


本报告所涉主要数据均来自「余额宝快报」微信公共平台所收集的数据，时间范围为2013年7月23日起至2013年10月7日止。部分用作对比数据（如各省市网民数）来自中国互联网络中心2013年发布的《第31次中国互联网网络发展状况统计报告》。
  
「余额宝快报」微信公共平台于7月13号注册，后台开发于7月21日完成，从23日开始产生完整的信息数据记录，所以本报告以2013年7月23日起至2013年10月7日止，14个自然日的数据为样本。


  
### 1.2	为什么要做这样的报告分析


「余额宝快报」微信公共平台推出以来受到用户欢迎，目前累计用户超过5000，每天处理1800次以上的用户互动查询，但用户及每天互动的消息数增长速度并未达到预期。通过本报告，了解细化用户的需求，了解他们的活跃时间，推出个性化的内容及服务。

1.3 数据准备

  
2 	数据分析与可视化
-----------------------

2.1	整体概况

截至2013-8-5日，余额宝快报共有1058位用户，其中7月23日起至8月5日共新增用户821名。




2.2 基金收益分析
-------------------

```r

load(file = "kuaibao.RData")

# <U+6BCF><U+65E5><U+6536><U+76CA><U+5206><U+6790>
summary(kuaibao.fund$profit)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##    1.15    1.21    1.22    1.25    1.26    1.51
```

```r
mean(kuaibao.fund$profit)
```

```
## [1] 1.252
```

```r
sd(kuaibao.fund$profit)
```

```
## [1] 0.07796
```

```r


# <U+6BCF><U+65E5><U+6536><U+76CA><U+5206><U+6790>
summary(kuaibao.fund$profit)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##    1.15    1.21    1.22    1.25    1.26    1.51
```

```r
mean(kuaibao.fund$profit)
```

```
## [1] 1.252
```

```r
sd(kuaibao.fund$profit)
```

```
## [1] 0.07796
```

```r

# <U+6BCF><U+65E5><U+6536><U+76CA><U+7EDF><U+8BA1>

# <U+6563><U+70B9><U+56FE>
ggplot(kuaibao.fund, aes(x = day, y = profit)) + geom_point(color = "#009E73") + 
    geom_smooth(color = "#D55E00")
```

```
## Error: could not find function "ggplot"
```

```r

# <U+7BB1><U+987B><U+56FE>
ggplot(kuaibao.fund, aes(x = day, y = profit)) + geom_point(color = "#009E73") + 
    geom_boxplot(colour = "#56B4E9", alpha = 0.1)
```

```
## Error: could not find function "ggplot"
```

```r

# <U+76F4><U+65B9><U+56FE>
ggplot(kuaibao.fund, aes(x = profit)) + geom_histogram(aes(y = ..count..), binwidth = 0.02, 
    colour = "black", fill = "white") + geom_density(alpha = 0.2, fill = "#FF6666")
```

```
## Error: could not find function "ggplot"
```

```r

```




2.3 消息统计分析
----------------



3  信息挖掘
-------------


```r
summary(cars)
```

```
##      speed           dist    
##  Min.   : 4.0   Min.   :  2  
##  1st Qu.:12.0   1st Qu.: 26  
##  Median :15.0   Median : 36  
##  Mean   :15.4   Mean   : 43  
##  3rd Qu.:19.0   3rd Qu.: 56  
##  Max.   :25.0   Max.   :120
```


You can also embed plots, for example:


```r
plot(cars)
```

![plot of chunk unnamed-chunk-3](unnamed-chunk-3.png) 


