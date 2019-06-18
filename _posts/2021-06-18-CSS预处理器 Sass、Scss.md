---
layout: post
title:  rem+媒体查询+vw 实现移动端自适应
date:   2019-06-18 00:00:00 +0800
categories: 前端工程化之自适应
tag: 前端工程化
---

* content
{:toc}


#### <font color="#65A5EA" size="3">1. rem 原理</font>			{#say-goodbyte}

&emsp;&emsp;首先，应该很清楚 rem 和 em的区别了：em 是相对于父元素字体而言；rem 是相对于根字体而言的。   
&emsp;&emsp;下载地址：https://www.charlesproxy.com/download/  

#### <font color="#65A5EA" size="3">2. 媒体查询</font>			{#say-goodbyte} 
&emsp;&emsp;移动端的屏幕大小差别很大，为了避免因为屏幕过宽或过窄造成字体过大或过小，需要结合没提查询，根据不同的屏幕宽度，人为规定字体大小。  
>&emsp;&emsp;<font color="green">//当屏幕宽度小于 320px 的时候，规定字体大小 64px 。</font>  
>&emsp;&emsp;@media screen and (max-width: 320px) {  
>&emsp;&emsp;&emsp;&emsp;font-size: 64px;  
>&emsp;&emsp;}  

>&emsp;&emsp;<font color="green">>//当屏幕宽度大于 540px 的时候，规定字体大小 108px 。</font>  
>&emsp;&emsp;@media screen and (min-width: 540px) {  
>&emsp;&emsp;&emsp;&emsp;font-size: 108px;  
>&emsp;&emsp;}



#### <font color="#65A5EA" size="3">3. css 预处理器语言 scss</font>			{#say-goodbyte} 
&emsp;&emsp;**scss 是 css 预处理器语言，语法规则和 css 类似。**  
**（1）**    
变量使用 $ 符表示，如 $vm_fontsize:75，
模版插值使用 #{ }，如font-size: #{ $vm_fontsize }。

具体规则可查看更多scss语法规则。

**（2）instanceOf 返回boolean，用于判断一个实例是否属于某种类型，会追溯原型链**  
除了Object 和 Function 之外，instanceof 自己等于false：    
>Object instanceof Object       //true  
>Function instanceof Function  //true  
>Function instanceof Object   //true  
>Number instanceof Number    //false  
>String instanceof String   //false  
>var num1 = new Number();  num1 instanceOf Number  //true   
>var num2 = '123';  num2 instanceOf Number  //false 

**(3)Object.prototype.toString.call(xx) 用于获取XX正确的数据类型，返回[Object Type] 的字符串**  
&emsp;&emsp;因为所有类型都是 object 的实例，toString 方法可以读取对象的内部属性[[class]]。  
&emsp;&emsp;为什么不直接使用 toString 呢？因为 toString 为 Object 的原型方法，而 Array、function、Date 等类型作为Object的实例，都重写了 toString 方法。  

**（4）constructor 是 object 对象的一个属性，用于判断 object 类型的数据**
>var arr = [1,2,3];  
>arr.constructor == Array    //true   

**（5）isArray es6提供的新方法，返回boolen，用于判断数组类型**  
>var arr = [1,2,3];  
>Array.isArray(arr)    //true  

#### <font color="#65A5EA" size="3">4. 数据类型转换</font>			{#say-goodbyte}  
* **在条件判断时，除了 undefined， null， false， NaN， ''， 0， -0，其他所有值都转为 true，包括所有对象。**  
* 另规定，console.log(null==null && undefined==undefined && null==undefined)  
* **对象在转换基本类型时，会优先将值转换为原始值（toPrimitive优先级最高），再转换为数字（valueOf），最后转换为字符串（toString）。**
* **在 + 号两侧，如果一方不是字符串或数字，则会将它转换为字符串或数字：**  
>'1'+'-2'  //'1-2' 仅仅是字符串拼接  
>[1,2]+[1,2]  // '1,21,2'  

<font color="red" size="2">解析：数组调用 valueof 得到基本类型的值，所以调用 toString 把数组转成字符串 </font>    
* **在 == 号两侧，会隐式地把布尔值转换成 number：**  
>if({}){console.log(4)}  //true -- 4  
>if({}==false){console.log(5)}  //false  
>if({}==!{}){console.log(6)}  //false  
>if({}=={}){console.log(7)}  //false    

>if([ ]){console.log(1)}   //true -- 1    
>if([ ]==false){console.log(2)}   //true -- 2 
>if([ ]==[ ]){console.log('')}   //false
>if([ ]==![ ]){console.log(3)}  //true -- 3  
><font color="red" size="2">解析：[ ]==![ ]  ---->   [ ]==false   ----->   [ ] == 0   ----->   0 == 0  --->//true </font>  
* 熟悉常见的优先级，如！非 的优先级最高。

#### <font color="#65A5EA" size="3">5. 深拷贝和浅拷贝</font>			{#say-goodbyte}
**基本类型和引用类型的区别是：**    
基本数据类型存储在栈中，引用类型存储在栈中的是一个地址，这个地址指向堆中的存储的对象；  
基本类型是传值，引用类型是传址，引用类型存在深浅拷贝。  
>*传值* 
>var a = 1;  
>var b = a;   
>b = 3;  
>console.log(b)   //3   
>console.log(a)   //1     

>*传址* 
>var a = {x:1,y:2};  
>var b = a;  
>b.x = 3;  
>console.log(b)   //{x:3,y:2}  
>console.log(a)   //{x:3,y2}  

&emsp;&emsp;当一个对象直接拷贝给另一个对象的时候，改变一个对象，另一个对象的数据也会相应改变，因为是拷贝的是地址，指向的是堆中的同一个对象。 

##### <font color="#65A5EA" size="2">浅拷贝(拷贝深度为一的引用类型)</font>  
（1）Object.assign()    
>Object.assign(target, ...sources)    
*//用于将所有可枚举属性的值从一个或多个源对象复制到目标对象。返回目标对象。*

>var a = {x:1,y:2};  
>var b = Object.assign({},a);  
>b.x = 3;  
>console.log(b)   //{x:3,y:2}  
>console.log(a)   //{x:1,y2}

（2）扩展符 ...  
>var a = {x:1,y:2};  
>var b = {...a};  
>b.x = 3;  
>console.log(b)   //{x:3,y:2}  
>console.log(a)   //{x:1,y2}

##### <font color="#65A5EA" size="2">深拷贝</font>    
（1）JSON.parse(JSON.stringify(obj1))
&emsp;&emsp;obj2 = JSON.parse(JSON.stringify(obj1)); 用JSON.stringify把对象转成字符串，再用JSON.parse把字符串转成新的对象。  
&emsp;&emsp;缺点是：这种方法转换后会抛弃对象的constructor，只能转换Number, String, Boolean，Array，像undefined，RegExp对象和function不能被转换。  
>var a = {x:{m:1,n:0},y:2};  
>var b = JSON.parse(JSON.stringify(a));  
>b.x.m = 3;  
>console.log(b)   //{x:{m:3,n:0},y:2}  
>console.log(a)   //{x:{m:1,n:0},y:2}  

（2）递归 (只拷贝自身的属性)  
>function deepClone(obj){  
>>if(obj == null || typeof obj !== 'object') { return obj }  
>>const newObj = new obj.constructor();  
>>for(let key in Object.getOwnPropertyDescriptors(obj)){  
>>>newObj[key] = clone(obj[key])  
>>}  
>>return newObj;  
>}  

>var a = {x:{m:1,n:0},y:2};  
>var b = deepClone(a)  
>b.x.m = 4;  
>console.log(b)  //{x:{m:4,n:0},y:2}  
>console.log(a)  //{x:{m:1,n:0},y:2}