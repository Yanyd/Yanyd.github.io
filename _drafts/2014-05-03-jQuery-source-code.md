

匿名函数自执行的优点：

所有东西(变量，函数等)都是局部的，避免与其他代码混淆或被干扰。

那么在匿名函数外边是访问不到里面的内容的，所以得对外提供访问的接口，就是将变量、函数挂载到window下，那么在外边就可以通过window对象来访问。

(function(){
	jQuery=function(){}
})();

window.jQuery=window.$=jQuery

extend 有利于扩展，按需求挂载到对象，而不是一下子就把很多方法，变量加给对象。

从面向对象的角度来思考:

静态方法(工具方法)，最底层的
$.trim();$.proxy();

实例方法
$().css();$().html();

很多实例方法里面调用的都是一些静态方法

回调/延迟： 先挂载再触发

定时器 setInterval()/倒计时setTimeout()

传参window的用意

1. 加快查找速度，因为window是js的最顶端，查找相对较慢，而变量的作用域查找是就近原则的。
2. 压缩时有用，window-->w,如果没有传参，在匿名函数里使用w是没有意义的，浏览器不知道这是什么。(function(w){w.$=jQuery;})(window);

undefined是window下的一个属性，也不是一个关键字，在一下浏览器下可以被修改。jQuery之所以要传入undefined,是为了防止被别人修改，匿名函数里要是使用到，也是使用自己的。

以面向对象的思维来思考：

我们的做法
function Aaa(){}

Aaa.prototype.init=function(){}
Aaa.prototype.css=function(){}

var a=new Aaa();
a.init();
a.css();


jQ的做法

function jQuery(){return new jQuery.prototype.init();} //构造函数，用来创建对象
jQuery.prototype    //原型对象，它就是一个对象
jQuery.prototype.init=function(){}//为对象添加一个初始化方法
jQuery.prototype.css=function(){} //为对象添加其他方法

//这句很很重要
jQuery.prototype.init.prototype=jQuery.prototype //对象引用

jQuery().css();

css方法是挂载在jQuery原型下的，但是是使用init创建对象的，说明其是构造函数，但是它并没有css方法，所以必须把它的原型对象指向jQuery的原型对象，这样new出来的对象就可以操作jQuery的方法了。



constructor:创建出来的对象的一个属性，值为该对象所属的构造函数。

修正指向问题

function Aaa(){}
Aaa.prototype.constructor=Aaa; //原生JS写法
Aaa.prototype.name="hello";
Aaa.prototype.age=20;
var a=new Aaa();
alert(a.constructor);//Aaa()

如果使用简写方式:

Aaa.prototype={
	name:'hello',
	age:20
};
alert(a.constructor); //变成Object()
因为简写方式并不是往原型对象添加属性，而是整个覆盖，所以constructor就被该对象覆盖了。
修正：

Aaa.prototype={
	constructor:Aaa,
	name:'hello',
	age:20
};







