---
author:Yanyd
date: 5/3/2014 9:00:57 AM 
layout: post
comments: true
slug: cookie-operate
title: Cookie相关操作
thread: 5
categories:
- Javascript

tags:
- cookie

---

##Cookie是什么

Cookie是User-Agent与服务器交互的凭据，是存储在用户本地终端上的数据(通常会加密)。由服务器产生，发送给浏览器，浏览器会将cookie存储在某个目录下的文本文件内，下一次请求同一网站时，就会把cookie包含在请求头里，一同发给服务器。（前提是浏览器没有禁止Cookie）

##为什么会有Cookie

http是无状态协议，上一秒还跟你聊得火热，下一秒就不认识你了，得了严重“失忆症”。那么如何让服务器端对你印象深刻呢？Cookie就是用来解决此问题的，可以把它当做是一种凭据，当服务器看到这个凭据，就知道你是谁了。Cookie最早是网景公司的前雇员LouMontulli在1993年3月发明的。

##Cookie有何用处

. 用户身份识别（记录用户登录信息，简化登录过程，自动识别用户）   

. 跟踪用户操作(访问时间、访问页面，停留时间等，然后数据统计分析，推测用户习惯，提供个性化服务，发送针对性广告，推送用户感兴趣的内容等)  

. 电商网站的购物车  

. 视频网站的观看记录和进度  

. 在线考试系统/做题系统的错题记录、做题进度等  

. 网站个性化(自定义网站外观，只关注网站某部分的内容等)

##Cookie格式/大小/个数

###格式

`key=value;expire=date;path=path;domain=domain`

其中value的格式为 `name1:value1&name2:value2&name3:value3...`

###大小

FF和Safari:4097字节  
Opera:4096字节 
IE：4095字节  
名、值、等号都算在内，所以说Coookie的容量一般不要超过4KB。

###个数(单位:个)

IE6：20/域名,IE7:30/域名,IE8:50/域名，Opera:30/域名  
WebKit内核的浏览器貌似没有限制，所以如果为了兼容IE6，每个域名最好不要超过20个。  
如果超过最大限制个数，会按照队列先进先出的方式处理，后面进，前面出。  
如果一个Cookie的容量还绰绰有余，那么可以将多个Cookie合并成一个。

##生存周期

Cookie在生成时会指定一个expires值，就是Cookie的生存周期，在这个周期内Cookie有效，超出这个周期Cookie就会被UA清除。一般会将这个周期设置为“0”或负值，这样在关闭浏览器时，Cookie就会被自动清除，不会记录用户信息，更加安全。

##如何操作Cookie

	<script type="text/javascript">
	    var str='',
	    	date='',
	    	ms='',
	    	cookieArr='';
	    var cookie={
		    set:function(key,value,expire){ //按小时计算
			    //escape() 函数可对字符串进行编码,这样就可以在所有的计算机上读取该字符串。
			    str=key+"="+escape(value);
			    if(expire>0){//设置过期时间
				    date=new Date();
				    ms= expire * 3600 * 1000;//存储时是按毫秒计算的
				    date.setTime(date.getTime()+ms)//设置当前Date对象所表示的日期/时间值
				    str+="; expires="+date.toGMTString();//根据格林威治时间(GTM)把Date对象转换为字符串
			    }
			    document.cookie=str;
		    },
		    get:function(key){
			    cookieArr=document.cookie.split("; ");
			    for(var i=0,len=cookieArr.length;i<len;i++){
				    var temp=cookieArr[i].split("=");
				    if(key===temp[0]){
					    return unescape(temp[1]); //解码
				    }
			    }
		    },
		    del:function(key){
			    date=new Date();
			    date.setTime(-1000);
			    document.cookie=key+"=; expires="+date.toGMTString();
		    },
		    getAll:function(){
			    return document.cookie;
		    }
	    };
	    //cookie.set('name','ada',1);
	    //cookie.get('name');
	    //cookie.del('name');
	</script>

##Cookie安全

可以手动清除Cookie也可以禁止Cookie.  
跨站点脚本攻击(Cross Site Scripting),利用网站漏洞在网站页面中或第三方脚本文件中植入脚本代码，获取用户Cookie信息，然后通过某种方式(Ajax等)将Cookie发送到指定服务器，攻击者将会重现其价值。

建议开发人员在向客户端 Cookie 输出敏感的内容时（譬如：该内容能识别用户身份）：

   1）设置该 Cookie 不能被脚本读取，这样在一定程度上解决上述问题。
   
   2）对 Cookie 内容进行加密，在加密前嵌入时间戳，保证每次加密后的密文都不一样（并且可以防止消息重放）。

   3）客户端请求时，每次或定时更新 Cookie 内容（即：基于第2小条，重新加密）

   4）每次向 Cookie 写入时间戳，数据库需要记录最后一次时间戳（防止 Cookie 篡改，或重放攻击）。
  
   5）客户端提交 Cookie 时，先解密然后校验时间戳，时间戳若小于数据数据库中记录，即意味发生攻击。


基于上述建议，即使 Cookie 被窃取，却因 Cookie 被随机更新，且内容无规律性，攻击者无法加以利用。另外利用了时间戳另一大好处就是防止 Cookie 篡改或重放。

注意事项：

images.sohu.com和man.sohu.com是同一域（sohu.com）下的不同主机名。默认情况下，一个主机中创建的cookie在另一个主机下是不能被访问的，但可以通过设置domain参数来实现对其控制，其语法格式为：document.cookie="name=value；domain=domain";
所以，这里可以通过设置document.cookie="name=value；domain=sohu.com";实现不同主机共享同一cookie 。