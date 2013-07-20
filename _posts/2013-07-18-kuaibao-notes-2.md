---
layout: post
title: 快报手记2：SAE本地读写XML失败的尝试
description: 
category: code
tags: [weixin , weibo , ali_bao ]
---
{% include JB/setup %}


[余额宝快报] (http://weibo.com/aliyuebao) 微博推出之后，便考虑写一个自动发微博的程序，
通过CRONTAB周期性抓取，本地XML保存定时发博，结果尝试失败。 


##方案

原计划采用的XML作为本地数据缓存，调用SAE的CRONTAB去定时抓取天弘官网的数据，CHECK数据是否更新，
若最新的收益数据为当天的数据且本地XML无此数据，则认为是第一次更新，发送微博并将最新的记录写入XML。


##编码

由于之前没有用过PHP，一个人琢磨，用了一个周末编码，主要过程有：

###预定义的XML文件格式
<pre>
	< ?xml version="1.0" encoding="utf-8"? >
	< root >
	< info date="2013-07-07" >< code >000198< code >< profit >1.3671< /profit >< rate >5.4250< /rate >< /info>
	< info date="2013-07-08" >< code>000198< /code>< profit >1.3129< /profit >< rate>5.2790< /rate >< /info>
	< /root >
</pre>

###php读取XML

<pre>
<!--lang=php-->
/**
 * 读取XML文件，判断是否是第一次发微博
 * @param string $filename 文件名
 */
function is_first($filename) {
    //读取XML
    $doc = new DOMDocument("1.0","utf-8");
	file_get_contents(  'saemc://'.'../data'.'/info.xml',$doc);
	$infos = $doc->getElementsByTagName("info");
	$today = date ("Y-m-d");
	echo $today;
	$array  = array();
	//判断是否存在
	foreach ($infos as $info){
		$date = $info->getAttribute("date");
		$array[] =$date;
	}
	if(in_array($today,$array))
	{
	    echo '当天记录已经更新';
		return 0;  
	}else
	{
	    echo '当天记录未更新';
		return 1;
	}
}
</pre>


###php写XML文件
<pre>
/**
 * 将记录写入XML文件
 * @param string $filename 文件名
 * @param array $record 需要写入的记录
 * @return string
 */
function add_record($filename,$record) {
    echo '添加新的节点';
	$doc = new DOMDocument("1.0","utf-8");
	file_get_contents(  $filename , $doc);
	$root = $doc->documentElement;
	$info=$doc->createElement("info");  //创建节点对象实体
	$info=$root->appendChild($info);    //把节点添加到root节点的子节点
	$date=$doc->createAttribute("date");  //创建节点属性对象实体
	$date=$info->appendChild($date);  //把属性添加到节点info中	
	$code=$doc->createElement("code");    //创建节点对象实体
	$code=$info->appendChild($code);
	$profit=$doc->createElement("profit");
	$profit=$info->appendChild($profit);
	$rate=$doc->createElement("rate");
	$rate=$info->appendChild($rate);
	$date->appendChild($doc->createTextNode($record['date']));  //createTextNode创建内容的子节点，然后把内容添加到节点中来
	$code->appendChild($doc->createTextNode($record['code']));
	$profit->appendChild($doc->createTextNode($record['profit'])); //注意要转码对于中文，因为XML默认为UTF-8格式
	$rate->appendChild($doc->createTextNode($record['rate']));
	file_put_contents(  $filename, $doc->saveXML() );
}
</pre>

本地多次测试完全OK后发布SAE。

之前并不知道SAE平台不支持直接读取file，第二天下午天弘更新数据后，程序自动发微博，但是没法将记录写入本地文件，
导致CRONTAB每次调用都会发微博，且频率为2分钟一次。当时我还在参加公司组织的羽毛球比赛，女友在家删微博删到手软。

尝试失败！


##结果

由于没法本地文件标记，所以之后便用CRONTAB在每天下午7点半定时抓取发布微博。


##后记

本系列博文用于记录维护优化 _余额宝快报_ 的点点滴滴，分享技术及经验。