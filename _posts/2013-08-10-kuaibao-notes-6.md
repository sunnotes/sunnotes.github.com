---
layout: post
title: "快报手记6：增加提问自动回复功能"
description: ""
category: code
tags: [wechat , weibo , ali_bao ]
---
{% include JB/setup %}

[余额宝快报] ()微信公共平台现在粉丝已经1200+，且每天都有很多朋友向小宝提问，
且都是很基础的问题，于是昨天花时间开发了一个自动回复的功能。

##方案

在SAE提供的MYSQL数据库中增加一张表 question，通过程序自动查询回复答案。

##编码

###数据库设计
增加一张表：
question.sql

	//create table
	CREATE TABLE question (
	   id int(8) NOT NULL AUTO_INCREMENT COMMENT 'ID',
	   code varchar(16) DEFAULT NULL COMMENT '问题编号',
	   question varchar(128) DEFAULT NULL COMMENT '问题',
	   answer varchar(256) DEFAULT NULL COMMENT '回答',
	   keyword varchar(64) DEFAULT NULL COMMENT '关键词',
	   PRIMARY KEY (id)
	)

插入数据

	//insert 部分示例数据
	insert  into `question`(`id`,`code`,`question`,`answer`,`keyword`) values (1,'001','余额宝收益历史','输入 L 既可以查看历史','收益'),(2,'002','余额宝收益是怎么结算的？','余额宝的收益为“每日结转，按日支付”的收益结算方式，也就是说投资者可以享有“日日复利”的复利收益。','结算'),(3,'003','余额宝为什么会出现日每万份收益下降而七日年化收益率却上升的现象？','因为7日年化收益率是把过去7天的收益换算成一年后得出的一个值，而每万份收益是当天实际的投资收益，所以两者变化趋势会有所不同。当天收益已经回落，但由于前七日收益较高，所以7日年化收益还可能往上走。','万份'),(4,'004','余额宝的收益是怎么计算的？','余额宝每天都会公布日每万份的收益。余额宝1份=1元，如果今天余额宝的日每万份收益是1.1907元，那么就是说您余额宝中每10000元钱，就能得到1.1907元的收益。',NULL),(5,'005','余额宝的风险如何？会亏损吗？','余额宝是货币基金，主要投资于银行存款、有固定票息的债券等品种，风险级别较低，历史上国内市场同类产品尚未出现过年度亏损。',NULL);
		


###后台程序

	//增加类question.class.php
	<?php
	class Question
	{
		/*
		*$question  用户提交的问题
		*/
		public function getQuestionList($question)
		{
			//预定义的关键词
			static $keywordlist = array(
					'1' => '收益',
					'2' => '结算',
					'3' => '万份',
					'4' => '年化',
					'5' => '7日',
					'6' => '七日',
					'7' => '计算',
					'8' => '风险',
					'9' => '亏损',
					'10' => '投资额',
					'11' => '交易',
					'12' => '费用',
					'13' => '转出',
					'14' => '资金',
					'15' => '存款',
					'16' => '利息',
					'17' => '开始',
					'18' => '转入'
			);
			//抽取问题中的关键词
			$i = 0;
			foreach ($keywordlist as $keyword) {
				if(strpos($question, $keyword)!==false)
				{
					$keywords["$i"] = $keyword;
					$i++;
				}
			}
			//通过LIKE查询，获取和关键词关联的问题
			$sql = 'SELECT  code ,question  FROM question WHERE 1!=1 ' ;
			if (isset($keywords)) {
				foreach ($keywords as $keyword){
					$sql = $sql." OR question LIKE '%$keyword%' ";
				}
				$sql = $sql." order by id";
			}else {
				return false;
			}
			$mysql = new SaeMysql();
			$result = $mysql->getData( $sql );
			$content ="小宝猜您想知道:\n";
			foreach ($result as $record){
				$content = $content.$record['code'].' '.$record['question']."\n";
			}
			$content = $content."......\n 回复数字编码查看回答 \n 000  返回更多问题";
			return $content;
		}

		/*
		*返回所有的问题单
		*/
		public function getAllQuestionList()
		{		
			$sql = 'SELECT  code ,question  FROM question order by id ' ;		
			$mysql = new SaeMysql();
			$result = $mysql->getData( $sql );
			$content ="回复数字编码查看回答:\n";
			foreach ($result as $record){
				$content = $content.$record['code'].' '.$record['question']."\n";
			}
			$content = $content."......\n 000  返回更多问题";
			return $content;
		}
		
		/*
		*$questioncode  问题编码
		* 返回问题答案，其中 000 0001 需特殊处理
		*/
		public function getAnswer($questioncode) {
			$sql = "SELECT  question,answer  FROM question WHERE code = $questioncode " ;
			$mysql = new SaeMysql();
			$result = $mysql->getData( $sql );
			if ($result == '') {
				$content = "你输入的数字编码无效...\n 000  返回更多问题";
			}else {
				$content = $questioncode.' '.$result[0]['question']."\n\n".$result[0]['answer']."\n"."......\n 000  返回更多问题";
			}
			return $content;
		}	
	}

##结果
增加该功能，短短几个小时，就已经有1000+ 问题查询，受到用户的热烈欢迎！

   
##后记

  1.  本系列博文用于记录维护优化**余额宝快报**的点点滴滴，分享技术及经验。
	
  2.  文章中所有的代码，详见[github](https://github.com/sunnotes/kuaibao)

	
	