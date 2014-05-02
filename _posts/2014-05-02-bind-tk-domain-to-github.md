---
author: Yanyd
comments: true
layout: post
date: 5/2/2014 4:28:46 PM 
slug: bind-tk-domain-to-github
title: "绑定TK域名到github pages上"
thread: 3
categories: 
- github

tags:
- DNSPod

---

捣鼓了好久才配置成功，之前直接使用跳转的，但那样设置太弱了，而且后面出现了问题。


###1、注册TK域名

[地址](http://www.dot.tk)，有时会抽风，注册不了，要耐心一点。

###2、添加CNAME文件

在仓库根目录下添加一个CNAM文件，没有后缀名，里面内容为你的域名(如:test.tk),
不需要添加http/www等前缀。

###3、添加DNSPod记录

去[DNSPod](http://www.dnspod.cn)注册个账号，添加域名，设置CNAME记录。
(如:* CNAME 默认 username.github.io. )然后保存即可。

###4、设置TK域名的DNS

在Csutom DNS里，设置DNS service,添加两条记录f1g1ns1.dnspod.net和f1g1ns2.dnspod.net
有时会抽风，提示你设置不成功，耐心一点，多试几次。

###5、漫长的等待

要全球解析生效，得等上一会了(72h)。
