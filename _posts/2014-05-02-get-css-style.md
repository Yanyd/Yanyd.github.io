---
author: Yanyd
comments: true
date: 5/2/2014 10:19:16 PM 
layout: post
slub: get-css-style
title: "获取元素的样式"
thread: 4
categories:
- js

tags:
- style

---

三种样式

1. 内嵌样式 `<div style='width:100px;'>test</div>`
2. 内部样式 `<head><style> div{width:100px;} </style></head>`
3. 外部样式 `<link src='/css/style.css' rel='stylesheet' type='text/css'></link>`

rel 属性规定当前文档与被链接文档之间的关系,在上例中，rel属性指示被链接的文档是一个样式表。

###获取内嵌样式

document.getElementById('nav').style.width

###获取非内嵌样式

IE:objDiv.currentStyle.width

FF:getComputedStyle(objDiv,false).width

通用方法

	//取得不在行间的样式 和 解决 offset 的bug(加边框时会出现问题)
	function getStyle(obj,attr){
		if(obj.currentStyle){
			return obj.crrentStyle[attr];
		}else if(window.getComputedStyle){
			return getComputedStyle(obj,false)[attr];
		}
	}