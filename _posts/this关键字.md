---
layout:     post
title:      this关键字
subtitle:   this关键字
date:       2020-4-20
author:     nylpupu
header-img: img/post-bg-alibaba.jpg
catalog: false
tags:
    - 设计点滴
---

## this关键字
#### 补充
>我们在js中主要研究的都是函数中的this

>js中的this代表的是当前行为执行的主体，js中的context（上下文）代表的是当前行为执行的坏境（区域）

```javascript
补充1.0
小白在沙县小吃吃馄饨
this>>小白，context>>沙县小吃
function 吃饭(){
this>>小白
}
小白.吃饭();
```
this是谁和函数在哪里定义的和在哪里执行的都没有任何关系

#### 如何的区分this？
###### 1、函数执行，首先看函数名前面有没有“.”,有的话，“.”前面是谁this就是谁；没有的话this就是window

```javascript
案例一
function fn(){
console.log(this);
}
var obj={fn:fn};
fn();
>>this>>window
obj.fn();
>>this>>obj
```

```javascript
案例一拓展1.0
function sum(){
>>this>>window
fn();
>>this>>window
}
sum();
```

```javascript
案例一拓展2.0
var oo={
sum:function(){
>>this>>oo
fn();
>>this>>window
}
};
oo.sum();
```
>“你以为你以为的就是你以为的”

###### 2、自执行函数中的this永远是window

```javascript
案例二
<div id="div1">点我</div>
—————————————
document.getElementById("div1").onclick=fn;
>>fn中的this是#div1
document.getElementById("div1").onclick=function(){
>>this>>#div1
fn();
>>this>>window
};
```

```javascript
补充2.0
自执行函数的区域
>>window:
~function fn(){
>>私有作用域
}();
```
###### 3、给元素的某一个事件绑定方法，当事件触发的时候，执行对应的方法，方法中的this是当前的元素

```javascript
案例三（看不懂）
var num=20;
var obj={
>>推内存空间
num:30,
fn(function (num){
this.num*=3;
num+=15;
var num=45;
return function(){
this.num*=4;
num+=20;
console.log(num);
}
})(num)
>>num是全局作用域下的，把全局变量num的20赋值给了自执行函数的形参，而不是obj下的30，如果想是obj下的30，我们需要写obj.num
};
var fn=obj.fn;
fn();
>>65
obj.fn();
>>85
console.log(window.num,obj.num);
>>240,120
```
![案例三，得好好琢磨琢磨](https://img-blog.csdnimg.cn/2020041202203979.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3lpbGluZ3B1cHU=,size_16,color_FFFFFF,t_70)

## this练习

```javascript
<div class="box">
<h2>是小仙女的就点击</h2>
<div id="btn">我非常赞同<span id="spanNum">0</span></div>
</div>
```
![运行结果](https://img-blog.csdnimg.cn/20200412022208839.jpg)

```javascript
错误方法
var obtn=document.getElementById("btn");
var spanNum=document.getElementById("spanNum");

obtn.onclick=function(){
var count=0;
count++;
spanNum.innerHTML=count;
};
>>从0-1，之后不会变成2
```
#### 方法一
1、利用全局作用域不销毁的原理，把需要累加的数字定义为全局的变量

```javascript
方法一
var count=0;
obtn.onclick=function(){
count++;
spanNum.innerHTML=count;
};
```
弊端：在项目中为了防止全局变量之间的冲突，我们一般禁止或者减少使用全局变量的

####  方法二

```javascript
方法二1.0
~function(){
var count=0;
obtn.onclick=function(){
count++;
spanNum.innerHTML=count;
};
}();
```

```javascript
方法二2.0
obtn.onclick=(function(){
var count=0;
return function(){
count++;
spanNum.innerHTML=count;
}
})();
```
弊端：有一个不销毁的私有作用域，所以占那么一丢丢的内存

#### 方法三
3、利用innerHTML的方式处理：每一次点击的时候都先到页面中获取最新的值，任何累加，最后把累加的结果重新放回去

```javascript
方法三
obtn.onclick=function(){
// spanNum.innerHTML获取页面的内容返回的是一个字符串
//spanNum.innerHTML=spanNum.innerHTML+1;
spanNum.innerHTML++;
};
```
弊端：每一次都需要把页面中的内容转化为字符串，任何再累加，累加完再重新添加回去，重新添加的时候，浏览器都要重新渲染

#### 方法四
4、利用自定义属性存储（推荐）

```javascript
方法四
obtn.count=0;
obtn.onclick=function(){
spanNum.innerHTML=++this.count;
};
```
![运行结果](https://img-blog.csdnimg.cn/20200412022550774.png)
