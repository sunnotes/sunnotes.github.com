---
layout: post
title: 用rpart和RFM对用户进行分类
description: 
category: statistics 
tags: [ R , rpart , CART , RFM ]
---
{% include JB/setup %}



#1. 目标
笔者通过微信平台收集了一些数据，本文计划对这些数据做数据分析，
希望在13000多名订阅者中找出比较活跃的约1000名进行问卷调查，
约100名进行访谈，为近期功能改版提供意见。

#2. 数据准备

##2.1 用rmysql读取数据
过程不是本文的重点，[详见](https://github.com/sunnotes/Programming-R/blob/master/kuaibao/data/userdata4rfm.R)


##2.2 数据样例

```
> dim(messages)
[1] 220493      5
> head(messages)
  userid             msgtime msg_type type_detail content
1      5 2013-07-23 00:24:03        1       query      cx
2      5 2013-07-23 00:38:35        1       query      ??
3      5 2013-07-23 00:38:41        1       query      cx
4      5 2013-07-23 00:45:03        5 unsubscribe    <NA>
5      5 2013-07-23 00:46:24        5   subscribe    <NA>
6     16 2013-07-23 01:46:28        1     compute Js35924

> load(file ='kuaibao/data/users.RData')
> dim(users)
[1] 13585    12
> head(users)
  userid subscribeday Totalmsgcnt Totaldaycnt D60msgcnt D60daycnt D30msgcnt
1      1   2013-07-21           0           0         0         0         0
2      2   2013-07-21         143          23         0         0         0
3      3   2013-07-21           0           0         0         0         0
4      4   2013-07-21          14           4         0         0         0
5      5   2013-07-21          27           4         0         0         0
6      6   2013-07-21          25          15         0         0         0
  D30daycnt D15msgcnt D15daycnt D7msgcnt D7daycnt
1         0         0         0        0        0
2         0         0         0        0        0
3         0         0         0        0        0
4         0         0         0        0        0
5         0         0         0        0        0
6         0         0         0        0        0
```
其中

- subscribeday表示该用户的订阅时间
- Totalmsgcnt表示该用户累积发送的消息数
- Totaldaycnt表示该用户累积发送到消息天数
- D60msgcnt表示该用户近60天累积发送的消息数
- D60daycnt表示该用户近60天累积发送到消息天数

#3. 用rpart对用户进行分类

##3.1 rpart简介

CART（Classification And Regression Tree）算法采用了一种二分递归分割的技术，
将当时的样本集分为两个样本集，使得生成的决策树的每一个非叶子节点都有2个分支。
因此，CART算法生产的决策树是结构简洁的二叉树。

rpart是R语言对CART的实现。


##3.2 分析过程

###3.2.1 对所有数据进行整体分类

```
> users_rpart <- rpart(userid ~ ., data=users[,-2],minsplit = 1000,cp = 0.01)
> print(users_rpart)
n= 13585 

node), split, n, deviance, yval
      * denotes terminal node

  1) root 13585 208933300000  6793.973  
    2) D30msgcnt< 0.5 5365  42326750000  4236.477  
      4) D60daycnt< 0.5 2453  12701170000  2521.370 *
      5) D60daycnt>=0.5 2912  16331500000  5681.243  
       10) Totaldaycnt>=4.5 634   2263584000  3091.880 *
       11) Totaldaycnt< 4.5 2278   8633997000  6401.900 *
3) D30msgcnt>=0.5 8220 108611900000  8463.191  
      6) Totaldaycnt>=2.5 5746  69915790000  7299.372  
       12) D30daycnt< 2.5 1320   9608964000  4707.581  
         24) Totaldaycnt>=9.5 358   1178033000  2581.478 *
         25) Totaldaycnt< 9.5 962   6210432000  5498.792 *
       13) D30daycnt>=2.5 4426  48795440000  8072.341  
         26) Totaldaycnt>=14.5 1797  16087650000  5988.137  
           52) D30daycnt< 11.5 589   2823868000  3932.154 *
           53) D30daycnt>=11.5 1208   9560086000  6990.599  
            106) Totaldaycnt>=31.5 394   1864297000  4328.231 *
            107) Totaldaycnt< 31.5 814   3551264000  8279.263 *
         27) Totaldaycnt< 14.5 2629  19566150000  9496.957  
           54) D15msgcnt< 6.5 1693  11281650000  8771.944  
            108) Totaldaycnt>=4.5 1229   7509701000  8045.753 *
            109) Totaldaycnt< 4.5 464   1407156000 10695.410 *
           55) D15msgcnt>=6.5 936   5784943000 10808.330 *
      7) Totaldaycnt< 2.5 2474  12837310000 11166.220  
       14) D15daycnt< 0.5 1086   4046202000 10070.770 *
       15) D15daycnt>=0.5 1388   6468240000 12023.330 *

```

通过业务分析，与快报互动较少的用户明显不符合活跃用户的定义，
详细分析前先将部分用户剔除掉，选择子树13为下一步分析的根树.

###3.2.2 第二步：对部分数据进行细化分析


```
> users_rpart10 <- rpart(userid ~ ., data=users[which(users$D30daycnt >=3),-2],minsplit = 100,cp = 0.001)
> print(users_rpart10)
n= 4426 

node), split, n, deviance, yval
      * denotes terminal node

  1) root 4426 48795440000  8072.341  
    2) Totaldaycnt>=14.5 1797 16087650000  5988.137  
      4) D30daycnt< 11.5 589  2823868000  3932.154  
        8) D60daycnt< 14.5 166   241363100  1938.958  
         16) Totaldaycnt>=26.5 49    28076800  1032.510 *
         17) Totaldaycnt< 26.5 117   156164200  2318.581 *
        9) D60daycnt>=14.5 423  1664207000  4714.355  
         18) Totaldaycnt>=25.5 148   368010800  2852.696  
           36) Totaldaycnt>=38.5 70    80725150  1812.271 *
           37) Totaldaycnt< 38.5 78   143509700  3786.410 *
         19) Totaldaycnt< 25.5 275   507210000  5716.265  
           38) Totaldaycnt>=17.5 159   212667500  5214.208 *
           39) Totaldaycnt< 17.5 116   199530300  6404.431  
             78) D30daycnt< 9.5 80    87249480  5963.950 *
             79) D30daycnt>=9.5 36    62265880  7383.278 *
      5) D30daycnt>=11.5 1208  9560086000  6990.599  
       10) Totaldaycnt>=31.5 394  1864297000  4328.231  
         20) Totaldaycnt>=55.5 87   130711100  1729.517 *
         21) Totaldaycnt< 55.5 307   979547000  5064.674  
           42) D30daycnt< 22.5 156   287329700  4031.994  
             84) D60daycnt< 31.5 36    32893460  2354.333 *
             85) D60daycnt>=31.5 120   122715600  4535.292 *
           43) D30daycnt>=22.5 151   353982600  6131.550  
             86) Totaldaycnt>=39.5 70    78008730  5058.414 *
             87) Totaldaycnt< 39.5 81   125694600  7058.951 *
       11) Totaldaycnt< 31.5 814  3551264000  8279.263  
         22) D30msgcnt< 27.5 370  1673616000  7358.384  
           44) Totaldaycnt>=18.5 230   812642500  6487.430  
             88) D30daycnt< 18.5 184   586414300  6095.120  
              176) Totaldaycnt>=22.5 105   302534700  5421.800 *
              177) Totaldaycnt< 22.5 79   173007500  6990.038 *
             89) D30daycnt>=18.5 46    84632980  8056.674 *
           45) Totaldaycnt< 18.5 140   399877800  8789.236  
             90) D30daycnt< 14.5 78   171734400  7873.885 *
             91) D30daycnt>=14.5 62    80570550  9940.806 *
         23) D30msgcnt>=27.5 444  1302408000  9046.662  
           46) Totaldaycnt>=24.5 171   449974100  8229.515  
             92) D30daycnt< 22.5 61   139101800  6854.934 *
             93) D30daycnt>=22.5 110   131699000  8991.782 *
           47) Totaldaycnt< 24.5 273   666732000  9558.502  
             94) D15daycnt< 8.5 94   229057500  8822.596 *
             95) D15daycnt>=8.5 179   360035100  9944.955 *
    3) Totaldaycnt< 14.5 2629 19566150000  9496.957  
      6) D15msgcnt< 6.5 1693 11281650000  8771.944  
       12) Totaldaycnt>=4.5 1229  7509701000  8045.753  
         24) D30daycnt< 4.5 415  2198535000  6322.749  
           48) D60daycnt< 4.5 33    59667760  2477.333 *
           49) D60daycnt>=4.5 382  1608733000  6654.945  
             98) Totaldaycnt>=8.5 130   427363100  5230.785  
              196) D60msgcnt< 13.5 44   176018000  3793.432 *
              197) D60msgcnt>=13.5 86   113933200  5966.174 *
             99) Totaldaycnt< 8.5 252   781679700  7389.631  
              198) D15daycnt>=1.5 108   383018700  6788.509 *
              199) D15daycnt< 1.5 144   330366400  7840.472 *
         25) D30daycnt>=4.5 814  3451017000  8924.188  
           50) Totaldaycnt>=7.5 512  2042882000  8286.725  
            100) D30daycnt< 7.5 225   813366200  6968.764  
              200) Totaldaycnt>=11.5 55   172158200  5838.055 *
              201) Totaldaycnt< 11.5 170   548140400  7334.582  
                402) D15daycnt>=3.5 41   179284500  6087.805 *
                403) D15daycnt< 3.5 129   284867100  7730.845 *
            101) D30daycnt>=7.5 287   532287200  9319.969  
              202) Totaldaycnt>=10.5 172   333175500  8800.000  
                404) D30daycnt< 10.5 97   147595600  8070.979 *
                405) D30daycnt>=10.5 75    67352370  9742.867 *
              203) Totaldaycnt< 10.5 115    83055900 10097.660 *
           51) Totaldaycnt< 7.5 302   847347700 10004.920  
            102) Totaldaycnt>=5.5 194   652173500  9560.778  
              204) D30daycnt< 5.5 52   178040000  7908.115 *
              205) D30daycnt>=5.5 142   280096100 10165.980 *
            103) Totaldaycnt< 5.5 108    88163100 10802.730 *
       13) Totaldaycnt< 4.5 464  1407156000 10695.410  
         26) D15daycnt< 2.5 301   585105200 10051.620  
           52) Totaldaycnt>=3.5 144   374260200  9555.556  
            104) D30daycnt< 3.5 53   144598900  8010.283 *
            105) D30daycnt>=3.5 91    29395430 10455.550 *
           53) Totaldaycnt< 3.5 157   142908600 10506.610 *
         27) D15daycnt>=2.5 163   466917900 11884.260 *
      7) D15msgcnt>=6.5 936  5784943000 10808.330  
       14) Totaldaycnt>=6.5 626  4230292000 10115.640  
         28) D30daycnt< 6.5 67   296462300  6071.836 *
         29) D30daycnt>=6.5 559  2706904000 10600.320  
           58) D15msgcnt< 16.5 353  1916796000  9945.045  
            116) Totalmsgcnt>=16.5 196   886307400  9127.010  
              232) D30msgcnt< 16.5 62   224399500  7133.726 *
              233) D30msgcnt>=16.5 134   301593700 10049.280 *
            117) Totalmsgcnt< 16.5 157   735588300 10966.290  
              234) Totaldaycnt>=8.5 73   485362700 10214.120 *
              235) Totaldaycnt< 8.5 84   173034500 11619.950 *
           59) D15msgcnt>=16.5 206   378801100 11723.190  
            118) Totaldaycnt>=12.5 33   123419200 10308.940 *
            119) Totaldaycnt< 12.5 173   176787700 11992.970 *
       15) Totaldaycnt< 6.5 310   647736900 12207.120  
         30) D7msgcnt< 6.5 191   497379500 11818.930 *
         31) D7msgcnt>=6.5 119    75379860 12830.180 *

```

###3.2.3 第三步：剪枝

```
> printcp(users_rpart10)

Regression tree:
rpart(formula = userid ~ ., data = users[which(users$D30daycnt >= 
    3), -2], minsplit = 100, cp = 0.001)

Variables actually used in tree construction:
[1] D15daycnt   D15msgcnt   D30daycnt   D30msgcnt   D60daycnt   D60msgcnt  
[7] D7msgcnt    Totaldaycnt Totalmsgcnt

Root node error: 4.8795e+10/4426 = 11024726

n= 4426 

          CP nsplit rel error  xerror      xstd
1  0.2693210      0   1.00000 1.00063 0.0175424
2  0.0804196      1   0.73068 0.73783 0.0147714
3  0.0512253      3   0.56984 0.56794 0.0133738
4  0.0484634      4   0.51861 0.52428 0.0131876
5  0.0381214      5   0.47015 0.49981 0.0127593
6  0.0218652      6   0.43203 0.45064 0.0120848
7  0.0188193      8   0.38830 0.39820 0.0111025
8  0.0161693      9   0.36948 0.37212 0.0107748
9  0.0154531     10   0.35331 0.35581 0.0106034
10 0.0128907     11   0.33786 0.34516 0.0105521
11 0.0117888     13   0.31208 0.33978 0.0104993
12 0.0108644     14   0.30029 0.32584 0.0101689
13 0.0094496     15   0.28942 0.30393 0.0099588
14 0.0084292     16   0.27997 0.29779 0.0097335
15 0.0081911     17   0.27154 0.29301 0.0096091
16 0.0072780     18   0.26335 0.28581 0.0095636
17 0.0069317     19   0.25608 0.27882 0.0096386
18 0.0067139     20   0.24914 0.27553 0.0096171
19 0.0038057     22   0.23572 0.25843 0.0094601
20 0.0036719     23   0.23191 0.24524 0.0093254
21 0.0030848     24   0.22824 0.23478 0.0089009
22 0.0030798     26   0.22207 0.22842 0.0088180
23 0.0030243     27   0.21899 0.22842 0.0088180
24 0.0029465     28   0.21596 0.22754 0.0088135
25 0.0029018     29   0.21302 0.22627 0.0088070
26 0.0028161     30   0.21012 0.22506 0.0087932
27 0.0027482     31   0.20730 0.22455 0.0087901
28 0.0026994     33   0.20180 0.22300 0.0087842
29 0.0024007     34   0.19910 0.21789 0.0085739
30 0.0022722     36   0.19430 0.21769 0.0085736
31 0.0019472     37   0.19203 0.21458 0.0085641
32 0.0019073     38   0.19008 0.21181 0.0085130
33 0.0017212     39   0.18818 0.20984 0.0084235
34 0.0016107     40   0.18645 0.20838 0.0084648
35 0.0015911     41   0.18484 0.20910 0.0084995
36 0.0015819     42   0.18325 0.20908 0.0084994
37 0.0015366     43   0.18167 0.20881 0.0084793
38 0.0013996     44   0.18013 0.20788 0.0084729
39 0.0011706     45   0.17873 0.20564 0.0083296
40 0.0010250     46   0.17756 0.20391 0.0082961
41 0.0010000     47   0.17654 0.20348 0.0083245

> users_rpart101 <- prune(users_rpart10,cp =0.0002)
> users_rpart101
n= 4426 

node), split, n, deviance, yval
      * denotes terminal node

  1) root 4426 48795440000  8072.341  
    2) Totaldaycnt>=14.5 1797 16087650000  5988.137  
      4) D30daycnt< 11.5 589  2823868000  3932.154  
        8) D60daycnt< 14.5 166   241363100  1938.958  
         16) Totaldaycnt>=26.5 49    28076800  1032.510 *
         17) Totaldaycnt< 26.5 117   156164200  2318.581 *
        9) D60daycnt>=14.5 423  1664207000  4714.355  
         18) Totaldaycnt>=25.5 148   368010800  2852.696  
           36) Totaldaycnt>=38.5 70    80725150  1812.271 *
           37) Totaldaycnt< 38.5 78   143509700  3786.410 *
         19) Totaldaycnt< 25.5 275   507210000  5716.265  
           38) Totaldaycnt>=17.5 159   212667500  5214.208 *
           39) Totaldaycnt< 17.5 116   199530300  6404.431  
             78) D30daycnt< 9.5 80    87249480  5963.950 *
             79) D30daycnt>=9.5 36    62265880  7383.278 *
      5) D30daycnt>=11.5 1208  9560086000  6990.599  
       10) Totaldaycnt>=31.5 394  1864297000  4328.231  
         20) Totaldaycnt>=55.5 87   130711100  1729.517 *
         21) Totaldaycnt< 55.5 307   979547000  5064.674  
           42) D30daycnt< 22.5 156   287329700  4031.994  
             84) D60daycnt< 31.5 36    32893460  2354.333 *
             85) D60daycnt>=31.5 120   122715600  4535.292 *
           43) D30daycnt>=22.5 151   353982600  6131.550  
             86) Totaldaycnt>=39.5 70    78008730  5058.414 *
             87) Totaldaycnt< 39.5 81   125694600  7058.951 *
       11) Totaldaycnt< 31.5 814  3551264000  8279.263  
         22) D30msgcnt< 27.5 370  1673616000  7358.384  
           44) Totaldaycnt>=18.5 230   812642500  6487.430  
             88) D30daycnt< 18.5 184   586414300  6095.120  
              176) Totaldaycnt>=22.5 105   302534700  5421.800 *
              177) Totaldaycnt< 22.5 79   173007500  6990.038 *
             89) D30daycnt>=18.5 46    84632980  8056.674 *
           45) Totaldaycnt< 18.5 140   399877800  8789.236  
             90) D30daycnt< 14.5 78   171734400  7873.885 *
             91) D30daycnt>=14.5 62    80570550  9940.806 *
         23) D30msgcnt>=27.5 444  1302408000  9046.662  
           46) Totaldaycnt>=24.5 171   449974100  8229.515  
             92) D30daycnt< 22.5 61   139101800  6854.934 *
             93) D30daycnt>=22.5 110   131699000  8991.782 *
           47) Totaldaycnt< 24.5 273   666732000  9558.502  
             94) D15daycnt< 8.5 94   229057500  8822.596 *
             95) D15daycnt>=8.5 179   360035100  9944.955 *
    3) Totaldaycnt< 14.5 2629 19566150000  9496.957  
      6) D15msgcnt< 6.5 1693 11281650000  8771.944  
       12) Totaldaycnt>=4.5 1229  7509701000  8045.753  
         24) D30daycnt< 4.5 415  2198535000  6322.749  
           48) D60daycnt< 4.5 33    59667760  2477.333 *
           49) D60daycnt>=4.5 382  1608733000  6654.945  
             98) Totaldaycnt>=8.5 130   427363100  5230.785  
              196) D60msgcnt< 13.5 44   176018000  3793.432 *
              197) D60msgcnt>=13.5 86   113933200  5966.174 *
             99) Totaldaycnt< 8.5 252   781679700  7389.631  
              198) D15daycnt>=1.5 108   383018700  6788.509 *
              199) D15daycnt< 1.5 144   330366400  7840.472 *
         25) D30daycnt>=4.5 814  3451017000  8924.188  
           50) Totaldaycnt>=7.5 512  2042882000  8286.725  
            100) D30daycnt< 7.5 225   813366200  6968.764  
              200) Totaldaycnt>=11.5 55   172158200  5838.055 *
              201) Totaldaycnt< 11.5 170   548140400  7334.582  
                402) D15daycnt>=3.5 41   179284500  6087.805 *
                403) D15daycnt< 3.5 129   284867100  7730.845 *
            101) D30daycnt>=7.5 287   532287200  9319.969  
              202) Totaldaycnt>=10.5 172   333175500  8800.000  
                404) D30daycnt< 10.5 97   147595600  8070.979 *
                405) D30daycnt>=10.5 75    67352370  9742.867 *
              203) Totaldaycnt< 10.5 115    83055900 10097.660 *
           51) Totaldaycnt< 7.5 302   847347700 10004.920  
            102) Totaldaycnt>=5.5 194   652173500  9560.778  
              204) D30daycnt< 5.5 52   178040000  7908.115 *
              205) D30daycnt>=5.5 142   280096100 10165.980 *
            103) Totaldaycnt< 5.5 108    88163100 10802.730 *
       13) Totaldaycnt< 4.5 464  1407156000 10695.410  
         26) D15daycnt< 2.5 301   585105200 10051.620  
           52) Totaldaycnt>=3.5 144   374260200  9555.556  
            104) D30daycnt< 3.5 53   144598900  8010.283 *
            105) D30daycnt>=3.5 91    29395430 10455.550 *
           53) Totaldaycnt< 3.5 157   142908600 10506.610 *
         27) D15daycnt>=2.5 163   466917900 11884.260 *
      7) D15msgcnt>=6.5 936  5784943000 10808.330  
       14) Totaldaycnt>=6.5 626  4230292000 10115.640  
         28) D30daycnt< 6.5 67   296462300  6071.836 *
         29) D30daycnt>=6.5 559  2706904000 10600.320  
           58) D15msgcnt< 16.5 353  1916796000  9945.045  
            116) Totalmsgcnt>=16.5 196   886307400  9127.010  
              232) D30msgcnt< 16.5 62   224399500  7133.726 *
              233) D30msgcnt>=16.5 134   301593700 10049.280 *
            117) Totalmsgcnt< 16.5 157   735588300 10966.290  
              234) Totaldaycnt>=8.5 73   485362700 10214.120 *
              235) Totaldaycnt< 8.5 84   173034500 11619.950 *
           59) D15msgcnt>=16.5 206   378801100 11723.190  
            118) Totaldaycnt>=12.5 33   123419200 10308.940 *
            119) Totaldaycnt< 12.5 173   176787700 11992.970 *
       15) Totaldaycnt< 6.5 310   647736900 12207.120  
         30) D7msgcnt< 6.5 191   497379500 11818.930 *
         31) D7msgcnt>=6.5 119    75379860 12830.180 *
		 
```

###3.2.4 选择目标用户

第一大类：

```

20) Totaldaycnt>=55.5 87   130711100  1729.517 *
85) D60daycnt>=31.5 120   122715600  4535.292 *
86) Totaldaycnt>=39.5 70    78008730  5058.414 *
87) Totaldaycnt< 39.5 81   125694600  7058.951 *

```

共358名用户，表示和快报互动最多，对快报功能有相对比较明确认识的用户。

第二大类：

```

46) Totaldaycnt>=24.5 171   449974100  8229.515  
95) D15daycnt>=8.5 179   360035100  9944.955 *

```


共350名用户，表示和快报互动较多，且近期和快报有较多互动的用户。

第三大类：

```

233) D30msgcnt>=16.5 134   301593700 10049.280 *
59) D15msgcnt>=16.5 206   378801100 11723.190  
31) D7msgcnt>=6.5 119    75379860 12830.180 *

```

共459名用户，表示近期比较活跃的用户。


##3.3 存在问题：

用该模块会存在一些问题，当前我也没法解释。

- **模型的选择是否合适？**
  此类问题用决策树是否合适，是否有更适用的方法？
	
- **模型中因变量的选择？**

```
> users_rpart <- rpart(userid ~ ., data=users[,-2],minsplit = 1000,cp = 0.01)
```

  表达式变量选择userid，感觉不妥，但是否有更合适的变量？

- **用户选择？**
  用户的选择具有很大的随意性，如何证明一个子树比另一个子树更合适呢？
	
- **模型证明？**
  怎么证明模型选择的用户是合适的？我能想到的是后期做问卷，看用户的反馈，但是否有其他更好的方式证明呢？


#4 用RFM模型对用户进行分类

##4.1 RFM模型介绍
	
根据美国数据库营销研究所Arthur Hughes的研究，客户数据库中有三个神奇的要素，这三个要素构成了数据分析最好的指标：

- 最近一次消费(Recency)
- 消费频率(Frequency)
- 消费金额(Monetary)


## 4.2 分析过程

###4.2.1 初始化数据

```
> dim(users)
[1] 13586    13
> head(users)
  userid subscribeday    lastday Totalmsgcnt Totaldaycnt D60msgcnt D60daycnt
1      0         <NA> 2013-11-23          NA          NA        NA        NA
2      1   2013-07-21       <NA>          NA          NA        NA        NA
3      2         <NA> 2013-08-20         143          23        NA        NA
4      3   2013-07-21       <NA>          NA          NA        NA        NA
5      4         <NA> 2013-07-30          14           4        NA        NA
6      5         <NA> 2013-07-27          27           4        NA        NA
  D30msgcnt D30daycnt D15msgcnt D15daycnt D7msgcnt D7daycnt
1        NA        NA        NA        NA       NA       NA
2        NA        NA        NA        NA       NA       NA
3        NA        NA        NA        NA       NA       NA
4        NA        NA        NA        NA       NA       NA
5        NA        NA        NA        NA       NA       NA
6        NA        NA        NA        NA       NA       NA
```

当前只对近30天的数据做具体的分析

```
> > subusers <- users[!is.na(users$lastday),c('userid','subscribeday','D30msgcnt','D30daycnt')]
> > subusers <- subset(users,!(is.na(lastday)|is.na(subscribeday)),
+                            select=c('userid','subscribeday','D30msgcnt','D30daycnt'))
> > dim(subusers)
[1] 13360     4
> head(subusers)
   userid subscribeday D30msgcnt D30daycnt
7       6   2013-07-21        NA        NA
8       7   2013-07-21        NA        NA
9       8   2013-07-21         1         1
10      9   2013-07-21        30        21
11     10   2013-07-21         4         2
12     11   2013-07-21        31        11
```

增加一列，用户从关注起到2013-11-23的间隔天数

```

> > usersR <- round(as.numeric(difftime('2013-11-23',subusers[,2],units="days")) )
> usersF <- subusers$D30daycnt;
> usersM <- subusers$D30msgcnt;
> > ##Merging R,F,M
> usersRFM <- as.data.frame(cbind(subusers$userid,usersR,usersF,usersM))
> usersRFM[is.na(usersRFM)] <- 0
> colnames(usersRFM) <- c('Userid','Recency','Frequency','Monetization')
> > > dim(usersRFM)
[1] 13360     4
> head(usersRFM)
  Userid Recency Frequency Monetization
1      6     125         0            0
2      7     125         0            0
3      8     125         1            1
4      9     125        21           30
5     10     125         2            4
6     11     125        11           31

```

### 4.2.2 通过KM对各个维度进行分类

```

> ##Creating R,F,M levels 
> km1 <-  kmeans(scale(usersRFM$Recency),3)
> km2 <-  kmeans(scale(usersRFM$Frequency),3)
> km3 <-  kmeans(scale(usersRFM$Monetization),3)

```

### 4.2.3 查看各个分类的区间并赋值

以R值为例

```

> range(usersRFM[which(km1$cluster == 1),'Recency'])
[1]  0 31
> range(usersRFM[which(km1$cluster == 2),'Recency'])
[1]  71 125
> range(usersRFM[which(km1$cluster == 3),'Recency'])
[1] 32 70
> > #为各分区赋值
> usersRFM$rankR <-0
> usersRFM[which(km1$cluster == 1),]$rankR <- 'HIGH'
> usersRFM[which(km1$cluster == 2),]$rankR <- 'LOW'
> usersRFM[which(km1$cluster == 3),]$rankR <- 'MEDIUM'

```

### 4.2.4 对结果进行分析

```

> #抽取结果
> head(usersRFM)
  Userid Recency Frequency Monetization rankR  rankF  rankM
1      6     125         0            0   LOW    LOW    LOW
2      7     125         0            0   LOW    LOW    LOW
3      8     125         1            1   LOW    LOW    LOW
4      9     125        21           30   LOW   HIGH MEDIUM
5     10     125         2            4   LOW    LOW    LOW
6     11     125        11           31   LOW MEDIUM MEDIUM

> table(usersRFM[,5:7])
, , rankM = HIGH

        rankF
rankR    HIGH  LOW MEDIUM
  HIGH    100    0     28
  LOW      23    0      0
  MEDIUM  120    0      9

, , rankM = LOW

        rankF
rankR    HIGH  LOW MEDIUM
  HIGH      0 2853    449
  LOW       0 3192    164
  MEDIUM    0 3970    575

, , rankM = MEDIUM

        rankF
rankR    HIGH  LOW MEDIUM
  HIGH    189   83    561
  LOW     102    3     85
  MEDIUM  452   12    390
  
```

# 5参考

所以的代码均在

- [github库](https://github.com/sunnotes/Programming-R)
- [rpart](https://github.com/sunnotes/Programming-R/tree/master/kuaibao/useranalysis/useranalysis.R)
- [RFM](https://github.com/sunnotes/Programming-R/tree/master/kuaibao/useranalysis/rfmanalysis.R)





