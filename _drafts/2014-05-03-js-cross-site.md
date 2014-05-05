---
---

Js跨域通讯

###主域与子域之间

a.test.com/b.test.com

设置document.domain="test.com"

###不同域名之间

a.com/b.com

1、使用jsonp方式

jsonp是为了解决ajax的跨域获取数据问题。  
jsonp（json padding 包装的json数据），利用回调的方式来获取数据。利用`<script>`标签没有跨域限制的特点，来达到与第三方通讯的目的。当需要通讯时，本站脚本创建一个`<script>`元素（一定要放在声明回调函数所在域的后面，才能调用到该回调函数。），地址指向第三方的API网址，形如：
<script src="http://www.example.net/api?param1=1&param2=2"></script>。

并提供一个回调函数来接收数据（函数名可约定，或通过地址参数传递）。
第三方产生的响应为json数据的包装（故称之为jsonp，即json padding），形如：
callback({"name":"hax","gender":"Male"})
这样浏览器会调用callback函数，并传递解析后json对象作为参数。本站脚本可在callback函数里处理所传入的数据。 

2、使用window.location

3、使用window.name
