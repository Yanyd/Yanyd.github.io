---
author: Yanyd
comments: true
date: 5/5/2014 8:45:34 AM
layout: post
slug: chrome-extension
title: "编写简单的chrome扩展程序-选中文字分享到微博"
thread: 8
categories:
- js

tags:
- chrome extension

---

##功能描述

在chrome浏览器的页面中，当鼠标选中一些文字之后，在鼠标旁边会显示一个微博分享按钮，用户点击该按钮，页面会自动跳转到·分享到微博·界面，并且相关内容已经自动填好，用户只需点击分享按钮即可。

##步骤一:实现获取文字功能

使用JS实现当用户选择一些文字之后，能够获取选中的内容的功能。对于获取选中文字，IE和其它遵循标准的浏览器有不同的做法，所以我们首先要封装一个通用的方法。

    function selectText(){
	    if(document.selection){//For ie
		    return document.selection.createRange().text;
	    }else{
		    return window.getSelection().toString();
	    }
    }

接着是监听document的鼠标抬起事件(onmouseup),判断选中文字的长度(比如要符合一定的字数才有意义)，决定是否显示分享图标；若满足条件，就获取鼠标当前位置，然后把分享图标显示在鼠标旁边。

	//创建图标
 	var objImg=new Image();
    objImg.src='share.gif';
    objImg.alt='微博分享图标';
    objImg.title='将选中的文字分享到微博';
    objImg.style.display='none';
    objImg.style.position='absolute';
    objImg.style.cursor='pointer';
	document.body.appendChild(objImg);
	
	//绑定鼠标抬起事件
    document.onmouseup=function(ev){
	    var 
	    	ev   = ev || window.event ,
	    	left = ev.clientX,
	    	top  = ev.clientY;
	    	
	    if(selectText().length>0){
			//解决IE下图标与鼠标对不齐的问题
		    setTimeout(function(){
			    objImg.style.display='block';
			    objImg.style.left = left+'px';
			    objImg.style.top  = top +'px';
			}, 100);
	    }
    }

然后给图标添加点击事件，跳转到分享页面地址，手动拼接url链接。由于鼠标松开后，onmouseup事件会冒泡到document,导致图标位置再次改变，所以要给图标添加onmouseup事件，阻止事件冒泡。

	objImg.onclick=function(ev){
		//新浪分享API
		window.location.href='http://v.t.sina.com.cn/share/share.php?searchPic=false&title='+selectText()+ '&url='+window.location.href;
	}

	//鼠标松开会触发document的mouseup事件/冒泡
	objImg.onmouseup=function(ev){
		var ev=ev|| window.event;
		ev.cancelBubble=true;
	}

最后就是点击页面任意位置，隐藏图标。

    document.onclick=function(ev){
	    objImg.style.display='none';
    }

总的代码，存为`select.js`，**若是含有中文，要使用UTF-8编码。**
	
	window.onload=function(){

	    var objImg=new Image();
	    objImg.src='share.gif';
	    objImg.alt='微博分享图标';
	    objImg.title='将选中的文字分享到微博';
	    objImg.style.display='none';
	    objImg.style.position='absolute';
	    objImg.style.cursor='pointer';
	    document.body.appendChild(objImg);	

	    function selectText(){
		    if(document.selection){//For ie
			    return document.selection.createRange().text;
		    }else{
			    return window.getSelection().toString();
		    }
	    }		

	    document.onmouseup=function(ev){
		    var 
		    	ev   = ev || window.event ,
		    	left = ev.clientX,
		    	top  = ev.clientY;
		    	
		    setTimeout(function(){
			    if(selectText().length>0){
				    setTimeout(function(){
					    objImg.style.display='block';
					    objImg.style.left = left+'px'; 
					    objImg.style.top  = top +'px';
					}, 100);
			    }
		    },200);
	    }

		objImg.onclick=function(ev){
			
			window.location.href='http://v.t.sina.com.cn/share/share.php?searchPic=false&title='+selectText()+ '&url='+window.location.href;
			
		}

		//鼠标松开会触发document的mouseup事件/冒泡
		objImg.onmouseup=function(ev){
			var ev=ev|| window.event;
			ev.cancelBubble=true;
		}

	    document.onclick=function(ev){
		    objImg.style.display='none';
	    }
	}


##步骤二：配置chrome扩展程序开发所必需的的文件

最主要的就是一个清单文件**manifest.json(不可缺少)**，**如果包含中文，要使用UTF-8编码**。其中内容如下，标`*`的都要填，查看更详细资料就Google下：

	{
	  "name": "分享文字", //插件名字 *
	  "version": "1.0",  //插件版本 *
	  "manifest_version":2, //清单文件格式的版本,在Chrome18之后,应该都是2 *
	  "description": "将选中文字分享到微博", //插件功能描述
	  "browser_action": { //插件标识
	    "default_icon": "favicon.ico" //显示的插件图标 *
	  },
	  "content_scripts": [ //在浏览器运行时，需要加入的js或css
	    {
	      "matches": ["http://*/*"], //哪些http协议支持该插件
	      "js": ["select.js"]        //运行插件需要哪些js文件 *
	    }
	  ]
	}


最后把js、css、图片、manifest.json等所需文件统一放入一个文件夹内。


##步骤三：安装扩展程序

打开chrome浏览器，点击右上角的设置面板，选择`设置`，再点击`扩展程序`，勾选`开发者模式`，再点击`加载正在开发的扩展程序`，选择定义好的扩展程序文件夹即可，如果程序有错误，会提示，按提示改正即可。安装成功后，在浏览器的右上角会看到自己的插件图标。

PS:网上有一种做法是，点击`扩展程序后`,直接把整个文件夹往里拖放即可，我没做成，不知哪里出现问题。

##问题:

1、在js里引用自己的图片，会自动加上网页的网址，导致找不到。

2、使用window.onload体验不好，若一个网页只加载一部分，此时想分享已加载的内容就不行。

3、图标位置显示不对，本地测试可以，打包后就不行。


##mousedown、mouseup、click事件的冲突问题

一般可以把onclick认为是onmousedown和onmouseup的集合体。onclick一般用于处理瞬间的点击效果，而onmousedown和onmouseup主要用于处理一些之间有时间间隔的效果，比如说鼠标拖拽停放的效果。

冲突场景：

如果一个对象身上既有onclick，又需要有onmousedown和onmouseup。如果点击这个对象，这三个事件同时触发。如果只是想要单机效果，但不巧的是，他把onmousedown和onmouseup也给触发了，而我们根本就不想触发onmousedown和onmouseup。

解决方案：

用setTimeout异步的调用OnMouseDown中的代码，间隔时间为200ms。

如果down和up之间时间

1)  >200ms,调用onmousedown和onmouseup

2)  <=200ms,调用onclick事件