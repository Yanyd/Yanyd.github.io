---
author: Yanyd
comments: true
date: 01/12/2018 09:53:00 PM 
layout: post
slug: nginx-image-server
title: "使用Nginx搭建图片服务器"
thread: 13
categories: 
- Nginx

tags:
- Nginx
---

## 前言

每次解决一个难题，都经过很多折腾，各种查找资料、做试验，过程曲折。问题解决了而没有及时纪录整理，过阵子又忘了，下次遇到还是懵逼。

这次想用Nginx搭建一个图片服务器，利用http_image_filter_module进行动态裁剪和缩放，由于Nginx是实时进行处理的，比较耗cpu，
所以使用proxy_cache将处理结果缓存下来，下次相同请求直接读取缓存，Nginx擅于处理IO密集型的任务。

## 1、Nginx相关说明

版本信息：

1. 服务器：Ubuntu 16.04.5 LTS(cat /etc/issue)
2. Nginx：1.10.3(nginx -V)

如果之前编译安装Nginx的时候，没有http_image_filter_module，需要重新手动编译进去。

## 2、Nginx配置

http块的设置(只写最主要的)：
	
	http {
		#下面的代码将会使用一块最大100M的共享内存，用于硬盘上的文件索引，包括文件名和请求次数，每个文件在1天内若不活跃（无请求）则从硬盘上淘汰，硬盘缓存最大10G，满了则根据LRU算法自动清除缓存。
		#这种缓存方式为了更快的索引，采用hash分级来存储图片，图片目录结构和名称都变得面目全非，因此网页热点图片，必须通过其他途径统计，比如日志。
		#有个奇怪的问题：use_temp_path=off时，会多创建一个temp目录，然后每次url缓存时，也会在里面生成一些无用的二级目录，不知道干嘛的，看了源码(ngx_http_file_cache.c)才知道有这个问题
		#1.10.3版本有这个问题，1.15.7版本没有这个问题
		#不知道是不是bug，use_temp_path默认为on
		proxy_cache_path  /var/cache/nginx/proxy_cache_path levels=1:2 keys_zone=img_cache:100m inactive=7d max_size=10g;
		
		#在日志格式中加入$upstream_cache_status
    		log_format static_img '$remote_addr - $remote_user [$time_local] ' '"$request" $status $body_bytes_sent ' '"$http_referer" "$http_user_agent" $upstream_cache_status';
		
		#配置文件分开写
		include ./static.conf;
	}

static.conf文件里相关server块的设置(只写最主要的)：
	
	#image_filter模块无法跟proxy_cache同时处理，要实现裁剪并缓存必须要将请求拆成两个Host来达成

	server {
	    listen 80;
	    server_name example.com;
	    #用来显示是否命中缓存(HIT、MISS等)
	    add_header Nginx-Cache $upstream_cache_status;
	    root /home/static/;

	    #禁止访问执行php脚本
	    location ~* \.(php|php5)$ {
	        deny all;
	    }
	    
	    #图片请求格式：http://example.com/a.jpg!200!200
	    location ~* (.*\.(jpg|gif|png))!(.*)!(.*)$ {
	        proxy_pass http://127.0.0.1:10299/$3/$4/$1;
	        proxy_cache img_cache;
	        proxy_cache_valid  200 304 302 7d;
	        #proxy_cache_key "$host$document_uri";
	
	        #当多个客户端请求一个缓存中不存在的文件（或称之为一个MISS），只有这些请求中的第一个被允许发送至服务器。其他请求在第一个请求得到满意结果之后在缓存中得到文件。
	        #如果不启用proxy_cache_lock，则所有在缓存中找不到文件的请求都会直接与服务器通信。
	        proxy_cache_lock on;
	
	        #当NGINX收到服务器返回的error，timeout或者其他指定的5xx错误，并且在其缓存中有请求文件的陈旧版本，则会将这些陈旧版本的文件而不是错误信息发送给客户端
	        proxy_cache_use_stale error timeout http_500 http_502 http_503 http_504;
	        expires 7d;
	
	        #到时可以用来统计命中情况，分析日志进行调整
	        #日志切割用系统自带的logrotate
	        access_log /var/log/nginx/example.com/access.log static_img;
	  	}
	}

	server {
	    #图片裁剪服务
	    listen 10299;
	    server_name 127.0.0.1;
	    access_log off;
	
	    location ~ ^/([0-9]+)/([0-9]+)/(.*)$ {
	        alias /home/static/$3;
	        set $width   $1;
	        set $height  $2;
	        image_filter resize $width $height;
	        image_filter_buffer 100M;
	        image_filter_interlace on;
	        image_filter_jpeg_quality 95;
	        image_filter_sharpen 100;
	        image_filter_transparency on;
	    }
	}

## 3、遇到的问题

由于对Nginx不熟悉，只能Google瞎搜索找解决方案，事倍功半：

1. image_filter和proxy_cache不能同时处理，要写在两个server块里。当时不懂整个原理，照搬网上的配置，写在同一个server块里，试了半天都没效果。
2. proxy_cache_path里的use_temp_path配置，官方文档是说这个用来决定是否使用临时目录，默认是on，当时使用下来并不是那么一回事。当时配置的是off，结果生成缓存时，会多一个temp目录，里面都是空的二级目录，除了占空间没什么用，想要搞清楚为什么，搜索很久都没答案，只能看源码。看了源码发现，当use_temp_path=off时会生成一个temp目录，感觉是判断写错了，是一个bug，后面找新版本的源码看下，已经没有那个判断了，也没再追究下去是不是bug了。

整个流程大概如下：一个请求过来，先是经过缓存服务，缓存里没有的话，就请求后端服务（回源），拿到结果后，返回给前端，再缓存下来，下次直接用缓存。

## 4、总结

每次都是瞎折腾一通，没有从本质上看待问题，解决问题，同样的坑踩多次。两点感悟：

1. 要知道一个工具如何使用，一定首先想到去看官方文档，对工具先有个大概的了解，而不是使用搜索引擎找别人的案例。
2. 使用过程中遇到问题了，最省事的做法就是直接分析源码，没那么难。

偷懒的话，迟早都得还回去。如果不从本质去解决问题，一定会一次次的去做这个功课，直到学会为止。

突然想到，实习的时候，当时的导师就说过，学东西要首先看官方文档，当时不以为意，可能因为都是英文，有点恐惧吧。还是得养成好习惯。