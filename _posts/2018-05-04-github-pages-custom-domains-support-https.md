---
author: Yanyd
comments: true
date: 4/5/2018 17:54:00 AM 
layout: post
slug: github-pages-custom-domains-support-https
title: "Github Pages 自定义域名支持https"
thread: 10
categories: 
- github

tags:
- 阿里云DNS解析
---

## 添加DNS解析

国内推荐使用阿里云DNS解析，DNSPod免费版本添加解析记录有上限，
每种类型最多添加2条记录，阿里云DNS解析目前免费且没有添加上限。
在域名下添加4条A记录：
	
	185.199.108.153
	185.199.109.153
	185.199.110.153
	185.199.111.153

配置完后，DNS解析一般都有十分钟缓存的，十分钟过后使用如下命令查看记录是否解析生效: `dig +noall +answer yanyd.tk(your domain)`

## 重新添加自定义域名

将原有的域名删掉后保存，然后再重新添加一次，就会触发Github为你的域名签发证书。
注意：去掉自定义域名保存后必须等它 publish 到 xxx.github.io 之后才能再次添加自定义域名。

## 等待证书生效

上面两步都做好了，就慢慢等待吧。可能刚开始请求量大，生效慢，我等了一天终于好了，中途还发了两封邮件寻求支持，也是一天后收到回复。
等生效后，就可以在项目的设置里勾选`Enforce HTTPS`，接下来所有的访问请求都会使用https了。

## 将页面中的HTTP请求改为HTTPS

像一些css、js、img请求，如果使用的是HTTP协议，那就改为相对协议或者HTTPS协议，这样就保证了全站都是真正的HTTPS协议。
