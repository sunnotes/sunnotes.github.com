---
layout: post
title: 快报手记3：用mysql本地存储数据成功
description: 
category: code
tags: [wechat , 余额宝 , 快报 ]
---
{% include JB/setup %}

[余额宝快报] ()微信公共平台推出之后，关注人数和每天的消息增加到100+ ，
出现来不及回复的状况，尤其在推送每日收益后情况更严重。
可见之前的方案及代码效率有很大的问题，需要调整。

##方案

考虑用SAE提供的MYSQL数据库存储每天的收益数据，这样来自微信平台的每次查询就可以读取本地的
数据库，大大提高效率。

##编码

###数据库设计

	CREATE TABLE `fund` (
	   `id` int(11) NOT NULL AUTO_INCREMENT,
	   `code` varchar(10) NOT NULL DEFAULT '000198',
	   `name` varchar(32) NOT NULL DEFAULT '增利宝',
	   `date` date NOT NULL,
	   `updatetime` datetime NOT NULL,
	   `profit` decimal(10,4) NOT NULL,
	   `rate` decimal(10,4) NOT NULL,
	   PRIMARY KEY (`id`)
	 ) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8


###用户连接数据库的辅助类
ADO.php

	<?php 
	class ADO
	{
		private $date = ' ';
		private $profit = ' ';
		private $rate = ' ';
		private $mysql;
		//获取最新的记录
		public function getlast()
		{
			$mysql = new SaeMysql();
			$sql = "SELECT date  , profit ,rate  FROM fund ORDER BY DATE DESC LIMIT 1;";
			$data = $mysql->getData( $sql );
			$record['date'] = $data[0]['date'];
			$record['profit'] = $data[0]['profit'];
			$record['rate'] = $data[0]['rate'];
			return $record;
		}
		//将记录写入数据库
		public function insert( $date, $profit, $rate)
		{
			$mysql = new SaeMysql();
			$sql = "INSERT  INTO `fund` ( `date` , `profit` , `rate` ,`updatetime` ) VALUES ( ' $date ' , ' $profit ' , ' $rate '   , NOW() ) ";
			$mysql->runSql( $sql );

		}
	}
	?>	


###其他
其他模块也做了相应的调整。
增加query.php 并由CRONTAB定时调用抓取页面，调用ADO插入数据库
weixin模块通过ADO读取最新数据，回复用户。

##结果
调整数据存储后，目前微信平台一直运行正常，效率有很大的提高


##Todo List
见识到了微信公共平台的强大，考虑新增如下功能:

   1.  增加机器人聊天功能
   
   2.  后台开发记录所有的消息，用于数据挖掘
   
##后记
本系列博文用于记录维护优化**余额宝快报**的点点滴滴，分享技术及经验。