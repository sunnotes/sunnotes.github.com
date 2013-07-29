---
layout: post
title: "PHP中使用JSON解析百度地图结果"
description: ""
category: code 
tags: [php , json, kuaibao]
---
{% include JB/setup %}

在做快报的应用时，需要用微信地址消息中的经纬度调用Baidu Map API 获取当前的城市名，
由于对JSON不熟悉，所以当时采用的是用XML读取数据，而后用正则表达式（相信
PHP也提供方法直接解析XML，正则匹配是最笨的办法）过滤出结果中城市的城市名。
考虑到JSON是现在最流行的数据交换格式，后来也对程序进行了改进，
本文针对这一块功能，就PHP使用JSON做一个介绍。


##PHP原生函数介绍

从5.2版本开始，PHP原生提供json_encode()和json_decode()函数，前者用于编码，后者用于解码。

###json_encode()

该函数主要用来将数组和对象，转换为json格式。先看一个数组转换的例子：

	$arr = array ('a'=>1,'b'=>2,'c'=>3,'d'=>4,'e'=>5);
    echo json_encode($arr);
	
结果为：

	{"a":1,"b":2,"c":3,"d":4,"e":5}
	
再看一个对象转换的例子：

        $obj->body = 'another post';
        $obj->id = 21;
        $obj->approved = true;
        $obj->favorite_count = 1;
        $obj->status = NULL;
        echo json_encode($obj);
　
结果为：

    {
	 	"body":"another post",
		"id":21,
		"approved":true,
		"favorite_count":1,
		"status":null
    }

由于json只接受utf-8编码的字符，所以json_encode()的参数必须是utf-8编码，
否则会得到空字符或者null。当中文使用GB2312编码，或者外文使用ISO-8859-1编码的时候，这一点要特别注意。

###json_encode()

该函数用于将json文本转换为相应的PHP数据结构。下面是一个例子：

    $json = '{"foo": 12345}';
    $obj = json_decode($json);
    print $obj->{'foo'}; // 12345

通常情况下，json_decode()总是返回一个PHP对象，而不是数组。比如：

    $json = '{"a":1,"b":2,"c":3,"d":4,"e":5}';
    var_dump(json_decode($json));

输出结果为:

    object(stdClass)#1 (5) {
		["a"] => int(1)
		["b"] => int(2)
		["c"] => int(3)
		["d"] => int(4)
		["e"] => int(5)
    }

如果想要强制生成PHP关联数组，json_decode()需要加一个参数true：

    $json = '{"a":1,"b":2,"c":3,"d":4,"e":5}';
    var_dump(json_decode($json),true);

结果就生成了一个关联数组：

    array(5) {
		["a"] => int(1)
		["b"] => int(2)
		["c"] => int(3)
		["d"] => int(4)
		["e"] => int(5)
    }

##百度地图API

关于如何使用baidu map API 不再赘述，详见[官方文档](http://developer.baidu.com/map/webservice-geocoding.htm#)

使用XML格式的代码

	/**
	*$location_x,$location_y 为经纬度值
	*baidu_map_key 为作者向百度地图申请，读者可自行申请
	*SaeFetchurl 为SAE提供的函数
	*/
	public function get_cityinfo_from_location($location_x,$location_y){
		$city = '';		
		//准备回复数据
		//使用百度API
		$baidu_map_api_url = 'http://api.map.baidu.com/geocoder?output=xml&';
		$baidu_map_key = 'ebad1e950d1f95a2656ffe633662659f';
		//调用SAE的类
		$saeFetchurl = new SaeFetchurl();
		//取百度的地址解析结果
		$baidu_return = $saeFetchurl->fetch($baidu_map_api_url.'location='.
				$location_x.','.$location_y.'&key='.$baidu_map_key);
		if($saeFetchurl->errno == 0){
			//匹配成功
			preg_match_all("/\<city\>(.*?)\<\/city\>/", $baidu_return, $city);
			$city = str_replace(array('市','县','区'), array('','',''), $city[1][0]);
		}
		return $city;
	}
	
其中调用的语句为：

	$url='http://api.map.baidu.com/geocoder?output=json&location=39.983424,116.322987&key=ebad1e950d1f95a2656ffe633662659f';
	$location_json=file_get_contents($url);
	echo $location_json;

返回的结果是：

	{
		"status":"OK",
		"result":{
			"location":{
				"lng":116.322987,
				"lat":39.983424
			},
			"formatted_address":"北京市海淀区中关村大街27号1101-08室",
			"business":"人民大学,中关村,苏州街",
			"addressComponent":{
				"city":"北京市",
				"district":"海淀区",
				"province":"北京市",
				"street":"中关村大街",
				"street_number":"27号1101-08室"
			},
			"cityCode":131
		}
	}

	
通过decode()函数解析成数组

	$object = json_decode($location_json,true);
	print_r($object);
	
输出的结果为：

	Array ( [status] => OK [result] => Array ( [location] => Array ( [lng] => 116.322987 [lat] => 39.983424 ) [formatted_address] => 北京市海淀区中关村大街27号1101-08室 [business] => 人民大学,中关村,苏州街 [addressComponent] => Array ( [city] => 北京市 [district] => 海淀区 [province] => 北京市 [street] => 中关村大街 [street_number] => 27号1101-08室 ) [cityCode] => 131 ) )
	
获取数组中有效值

	echo $object['status'];
	echo $object['result']['addressComponent']['city'];
	
输出结果：

	OK
	北京市

##总结

  1.  使用JSON可以高效的解析出所需要的信息
  
  2.  文章中所有的代码，详见[github](https://github.com/sunnotes/beta/)   json 目录下 json.php
	
##参考材料
  1.  [在PHP语言中使用JSON](http://www.ruanyifeng.com/blog/2011/01/json_in_php.html)
  
  2.  [数据类型和Json格式](http://www.ruanyifeng.com/blog/2009/05/data_types_and_json.html)
  
  3.  [PHP Manual](http://php.net/manual/en/book.json.php)
	
	

	
	
	
	

