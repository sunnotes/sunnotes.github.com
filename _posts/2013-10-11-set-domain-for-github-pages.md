---
layout: post
title: Github博客使用自定义域名访问
description: 
category: lessons
tags: [jekyll , github , pages  ]
---
{% include JB/setup %}

首先去域名服务商域名解析记录列表中添加一条A记录，设置IP为103.245.222.133

可以使用dig命令获取github提供的ip，如下所示


```
eason@EASON:~$ dig sunnotes.github.com

; <<>> DiG 9.7.3 <<>> sunnotes.github.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60119
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;sunnotes.github.com.		IN	A

;; ANSWER SECTION:
sunnotes.github.com.	3600	IN	CNAME	github.map.fastly.net.
github.map.fastly.net.	22	IN	A	103.245.222.133

;; Query time: 78 msec
;; SERVER: 192.168.1.1#53(192.168.1.1)
;; WHEN: Mon Oct  7 22:38:54 2013
;; MSG SIZE  rcvd: 88
```


可以看到sunnotes.github.com的IP为103.245.222.133

然后在Pages的Root目录里面添加一个名为CNAME的文件，文件内容就是你的域名，例如这里的：


```
sunnotes.com
```


等域名更改生效，就可以用 sunnotes.me 访问你的Github博客了


另外，自定义域名后，也可以通过添加右上角```Fork me on Github```链接图标forward到自己的github站点。

设置方式详见[GitHub Ribbons](https://github.com/blog/273-github-ribbons)

