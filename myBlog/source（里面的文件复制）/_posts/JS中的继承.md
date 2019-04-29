---
title: JS中的继承
date: 2018-11-21 13:54:08
categories: [JavaScript]
tags: [JavaScript]
---

- 什么是继承？继承就是子类拥有父类的各种属性和方法
 
- 什么是类？这里的JS的类即是函数，能产生对象的东西就是类
 
- 继承需要两个类：父类和子类，在JS中的继承需要有2次对原型的搜索。
 
- 如何让子类拥有父类的属性和方法？

```
//原理
子类('参数'){
    父类.call(this,'参数')
}
子类.prototype.__proto__=父类.prototype
```

好了，现在开始说明举例。

分为ES5和ES6的写法以及两者的区别。

# 一、ES5中如何完成JS的继承？

## 1、声明一个父类函数，父类函数有一个name的属性和run()的方法

```
function Human(name){
     this.name = name
 }
Human.prototype.run = function(){
     console.log("我叫"+this.name+"，我在跑")
     return undefined
 }
```

## 2、声明一个子类函数，并从父类函数中继承属性

```
function Man(name){
     Human.call(this, name) //是Human的属性
     this.gender = '男' //是自己的属性
 }
Man.prototype.fight = function(){
     console.log('xxx')
}
```
 
注意，Human.call(this, name)就是从父类继承属性，这句代码很重要

## 3、从父类函数继承方法

```
//添加一句这样的代码就可以了
Man.prototype.__proto__=Human.prototype
```

然而IE不兼容这个代码所以要换另一种方式

```
var f = function(){}
f.prototype = Human.prototype
Man.prototype = new f()
```

- 原因是什么？因为当 var a =new f()的时候

会发生5个步骤：
产生一个空对象
this=空对象
this._proto__=f.prototype
执行f.call(this,其他参数)
return 上一行述结果
所以需要声明一个空的函数f。

# 二、ES6中如何完成JS的继承？

```
class Human{
     constructor(name){
         this.name = name
     }
     run(){
         console.log("我叫"+this.name+"，我在跑")
         return undefined
     }
 }
 class Man extends Human{
     constructor(name){
         super(name)
         this.gender = '男'
     }
     fight(){
         console.log('xxx')
     }
 }
```
从ES5改成ES6逻辑其实是一样的，只是写法换了一下。其中用关键字extends来继承父类，用super来call一下父类获得父类的属性。

# 三、ES5与ES6写法的优劣？

- ES5的写法可以更直观的看清楚内存的变化，同时可以在方法中增加属性。而ES6的写法不能在方法中增加属性，暂时不支持。