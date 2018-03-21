---
author: Yanyd
comments: true
date: 2/5/2014 1:17:50 PM 
layout: post
slug: scroll-pic
title: "图片无缝滚动"
thread: 2
categories:
- CSS
- jQuery

tags:
- CSS
- XHTML
- jQuery

---

##扯几句
没工作了，心里拔凉拔凉的，一想到毕业后该何去何从，心中无数匹草泥马奔腾而过。每一个哀叹今天的人，都有一个不曾努力的过去。人，在自己还不是很厉害的时候，千万别把自己太当回事。

##原理
将两组相同的图片并排在一行(想像一下，如果首尾相连，是不是就构成一个圆环了，那么不管怎么转都不会出现问题了),所有图片用一个大容器包住，实际移动的就是整个容器。(好吧，这不算原理，表达是硬伤!!!)

##布局
	<div class="wrapper clearfix">
    	<a href="javascript:void(0);" title="" class="prev">
        	<img src="prev.jpg" alt="" title=""/>
        </a>
        <a href="javascript:void(0);" title="" class="next">
        	<img src="next.jpg" alt="" title=""/>
        </a>
        <div class="scroll">
	        <div class="scroll_pic">
	        	<ul class="count">
	            	<li>
	                	<a href="#" title="">
	                    	<img src="1.jpg" alt="' title=""/>
	                    </a>
	                </li>
	                <li>
	                	<a href="#" title="">
	                    	<img src="2.jpg" alt="' title=""/>
	                    </a>
	                </li>
	                <li>
	                	<a href="#" title="">
	                    	<img src="3.jpg" alt="' title=""/>
	                    </a>
	                </li>
	                <li>
	                	<a href="#" title="">
	                    	<img src="4.jpg" alt="' title=""/>
	                    </a>
	                </li>
	            </ul><!--第一组图片 end-->
	        	<ul>
	            	<li>
	                	<a href="#" title="">
	                    	<img src="1.jpg" alt="' title=""/>
	                    </a>
	                </li>
	                <li>
	                	<a href="#" title="">
	                    	<img src="2.jpg" alt="' title=""/>
	                    </a>
	                </li>
	                <li>
	                	<a href="#" title="">
	                    	<img src="3.jpg" alt="' title=""/>
	                    </a>
	                </li>
	                <li>
	                	<a href="#" title="">
	                    	<img src="4.jpg" alt="' title=""/>
	                    </a>
	                </li>
	            </ul><!--第二组图片 end-->
	        </div><!--图片包裹器(实际滚动元素) end-->
        </div><!--图片可视区域 end-->
    </div><!--整体包裹器 end-->

##样式
	<style type="text/css">
	
	/*样式重置*/
	body,div,ul,li,a,img{margin:0;padding:0;}
	ul{list-style:none;vertical-align:bottom;}
	img{border:none;}
	
	/*清除浮动*/
	.clearfix:before,.clearfix:after{
		content:"";
		display: table;
	}
	.clearfix:after{
		clear:both;
	}
	.clearfix{*zoom:1;/*For IE6/7 , trigger hasLayout*/}
	
	.wrapper{width:980px;height:100px;overflow:hidden;margin:100px auto 0;}
	
	.wrapper ul img{width:200px;height:100px;}
	
	.wrapper ul li{float:left;padding:0 10px;}
	
	/*按钮样式设置*/
	.wrapper .prev,.wrapper .next{width:50px;height:100px;}
	
	.wrapper .prev{float:left;}
	
	.wrapper .prev img,.next img{margin-top:-50px;}
	
	.wrapper .next{float:right;}
	
	.wrapper .next img{margin-left:40px;}
	
	.wrapper .scroll{width:880px;height:100px;overflow:hidden;position:relative;float:left;}
	
	.wrapper .scroll_pic{width:3000px;position:absolute;left:0;top:0;}
	
	</style>

##jQuery代码
	<!--版本太高 IE6 不支持-->
	<script src="http://libs.baidu.com/jquery/1.7.0/jquery.min.js"></script>
	<script type="text/javascript">
	$(function(){
		var 
			len=$('.count li').length,            //图片个数
			left='',				              //滚动区域相对父容器的左边位置
			animateTime=800,                      //动画时间
			liWidth=$('.count li').innerWidth(),  //包含一张图片的容器的宽度
												  //innerWidth包含padding值，outerWidth包含border和margin，根据自己情况取值。
			ticker='',			                  //滚动循环器
			duration=3000;                        //滚动周期(就是每隔多长时间滚动一次),不能比动画时间小
	
		//向前滚动
		$('.prev').on('click',scrollPrev);
		function scrollPrev(){		
				$('.prev').off('click');          //解除事件绑定(为了避免快速点击，程序来不及反应)
				left=$('.scroll_pic').position().left;
				if(0==left){
					$('.scroll_pic').css('left',-liWidth*len+'px');
				}
				$('.scroll_pic').animate({'left':'+='+liWidth+'px'},animateTime,function(){
					$('.prev').on('click',scrollPrev);  //动画完成后恢复事件绑定
				});
				
		 }
		 //向后滚动
		 $('.next').on('click',scrollNext);
		 function scrollNext(){
			 $('.next').off('click');
			left=$('.scroll_pic').position().left;
			if(-liWidth*len==left){
				$('.scroll_pic').css('left','0px');	
			}
			$('.scroll_pic').animate({'left':'-='+liWidth+'px'},animateTime,function(){
				$('.next').on('click',scrollNext);
			});
			
		 }
		 //自动滚动
		 ticker=autoRun();
		 function autoRun(){
		 	return setInterval(scrollNext,duration);
		 }
		 //停止/恢复自动滚动
		 $('.wrapper').hover(
		    function(){ clearInterval(ticker);},
			function(){ticker=autoRun();}
		 );
	});
	</script>

##总结
重点是这两句:

	//点击向前按钮，下一张是一组图片中最后一张图片
	if(0==left){
        $('.scroll_pic').css('left',-liWidth*len+'px');
    }
	//点击向后按钮，下一张是一组图片中第一张图片
	if(-liWidth*len==left){
        $('.scroll_pic').css('left','0px'); 
    }

在执行动画之前，把大容器移动到合适的位置，然后正常执行动画，由于计算机执行速度非常快，你看不到变化，看上去像一气呵成。附上[DEMO](/scroll-pic-demo/ "图片无缝滚动")
