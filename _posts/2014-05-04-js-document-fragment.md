---
author: Yanyd
comments: true
date: 4/5/2014 9:58:06 AM  
layout: post
slug: js-document-fragment
title: "使用文档碎片提高JS执行效率"
thread: 6
categories:
- js

tags:
- 文档碎片

---

	<title>文档碎片</title>
	<script>
		//往body添加10个p标签
		window.onload=function(){
			//文档碎片，相当于一个透明的div(容器)，暂时存放数据的地方
			var fragment=document.createDocumentFragment(),objP;
			for(var i=1;i<11;i++){
				objP=document.createElement('p');
				objP.appendChild(document.createTextNode(i));
				fragment.appendChild(objP);
			}
			//尽量减少DOM的操作，一次可以完成的就不要分多次完成
			document.body.appendChild(fragment);	
		}
	</script>