---
layout: post
title: 快报手记4：微信添加location消息，回复天气信息
description: 
category: code
tags: [微信 , 余额宝 , 快报 ]
---
{% include JB/setup %}

今天花了一天的时间增加微信读取location消息，并回复天气的功能。


##读取location消息

通过微信API直接获取和地址相关的信息，wechat API有详细的讲解

##调用baidu map API 获取城市名
	/*
	* location_x location_y 是经纬度坐标
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


##调用sina weather API 获取天气信息
	
	/*
	*输入 $city  城市名
	*/
	public function get_weatherinfo_from_city($city){
		$content ='';
		if($city == ''){
			return $content;
		}
		//转码
		$cityencode = urlencode(iconv('UTF-8', 'GBK', $city));
		$day = '&day=0';
		//使用sina 天气API
		$sina_weather_api = 'http://php.weather.sina.com.cn/xml.php?city='.
				$cityencode.'&password=DJOYnieT8234jlsK&day=0';
		$saeFetchurl = new SaeFetchurl();
		$weather_return = $saeFetchurl->fetch($sina_weather_api);
		if($saeFetchurl->errno == 0){
			//读取成功
			preg_match_all("/\<\!-- published at (.*?)\--\>/", $weather_return, $publish_time);
			preg_match_all("/\<status1\>(.*?)\<\/status1\>/", $weather_return, $status1);
			preg_match_all("/\<status2\>(.*?)\<\/status2\>/", $weather_return, $status2);
			preg_match_all("/\<temperature1\>(.*?)\<\/temperature1\>/", $weather_return, $temperature1);
			preg_match_all("/\<temperature2\>(.*?)\<\/temperature2\>/", $weather_return, $temperature2);
			preg_match_all("/\<chy_shuoming\>(.*?)\<\/chy_shuoming\>/", $weather_return, $dress);
			if($status1 == $stutus2){
				$status = $status1[1][0];
			}else {
				$status = $status1[1][0].'转'.$status2[1][0];
			}
			$temperature = $temperature1[1][0].'℃ ~ '.$temperature2[1][0].'℃  ';
			$content =   '您所在的城市：'.$city."\n".
					'天气信息播报'."\n".
					'更新时间：'.$publish_time[1][0]."\n".
					'气候：'.$status."\n".
					'温度：'.$temperature."\n".
					'穿衣：'.$dress[1][0]."\n";
		}
		return $content;
	}



##Todo List
  1.  用mysql数据库保存用户提交的信息
  
  
##后记
本系列博文用于记录维护优化**余额宝快报**的点点滴滴，分享技术及经验。

