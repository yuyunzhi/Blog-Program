---
title: 什么是MVC？
date: 2018-09-05 21:41:55
categories: [JavaScript]
tags: [JavaScript,MVC]
---

> 这段时间写代码，总是会忘记之前写的代码是什么意思？
那怎么办呢？
有什么好的办法能够清晰的看明白自己之前写的代码或让别人也更好的一目了然？
当然有，那就是……
用MVC。


# 一、什么是MVC

**MVC是Model View Controller**

- View：是这个js模块对应在html中的部分，就是展示给用户看的那一部分
- Model：可以从服务器获得数据，把数据传给Controller。还要将Controller监听到的用户提交的数据上传到服务器
- Controller：调用model的数据，用来更新view。还要监听用户在view上的操作，获取用户提交的数据，传给model

![图图](https://pic3.zhimg.com/80/v2-eaa6f710f48a952b04be3733fd35963e_hd.jpg)

# 二、举个例子来理解

**今天做了个留言板**[如何使用LeanCloud加个数据库？](https://zhuanlan.zhihu.com/p/37058493)

但是第一次用MVC重新改自己的代码花了我三个小时.....来说说步骤：

## 1、模块化

不同类型功能的JS代码放在不同的JS文件。同时一个监听事件包含了多个功能，要用队列来分开：xxx.addEventListener('yyy',function(){})
![xxx](https://pic3.zhimg.com/80/v2-1dbf51ffb6b5b2b7f0efd765cdda1bda_hd.jpg)

## 2、立即执行函数

- 有些时候我们不想要全局变量，所以我们要使用局部变量
- 在ES 5 里面只有函数有局部变量，于是我们声明一个 function xxx，然后 xxx.call()
- 然而 xxx 是全局变量（全局函数），所以我们得用匿名函数：function(){}.call()
- 但是 Chrome 会语法错误，试出来一种方法可以不报错:！function(){}.call()

## 3、使用闭包

如果要两个模块变量能够访问……

```
!function(){
    var people={
    name:'yukaka',
    age:25
}
window.yukakaGrouUp = function(){
    people.age += 1
    return people.age
}
}.call()
```
**解释：**

- 立即执行函数使得people 无法被外部访问
- 闭包使得匿名函数可以操作 people
- window.yukakaGrowUp 保存了匿名函数的地址
- 任何地方都可以调用 window.yukakaGrowUp（）
- => 任何地方都可以使用 window.yukakaGrowUp 操作 people，但是不能直接访问 people

**什么是闭包？**函数内部使用了函数外面的变量，那么这个函数和这个变量加起来叫做闭包。

## 4、开始用MVC改代码

格式如下：
```
!function(){
    var view =document.querySelector('#xxx')
    var model ={
        init:function(){},
        fetch:function(){},
        save:function(){},
    }
    var controller ={
        view:null,
        init:function(view){
             this.view=view
             //声明其他变量如下
             this.aaa=this.view.querySelector('#aaa')
             this.bindEvents()
            //初始化调用其他的函数如下
             this.ccc()
        },
    }
    bindEvents:function(){}
    ccc:function(){}

    controller.init.call(controller,view)
}.call()
```
**注意：**

- 大致格式是这样的，但并不是所有的都要用，也并不是所有的都不用
- controller.init.call(controller,view)，括号里的第一个参数是this，第二个及第二个以后的参数是arguments。上述调用可以写为controller.init(view)
- 箭头函数内外的this是一样的，或者不用箭头函数直接绑定this

```
 let aTags=this.view.querySelectorAll('nav.menu>ul>li>a');
 for(let i=0;i<aTags.length;i++){
     aTags[i].onclick=function(xxx){
        xxx.preventDefault();
        let a =xxx.currentTarget;
        let href=a.getAttribute('href')//"#siteAbout"
        let element = document.querySelector(href);
        this.scrollToElement(element)
     }.bind(this)//或把函数改为箭头函数，替换bind(this)
}
```

两段代码等价~~~~

```
let aTags=this.view.querySelectorAll('nav.menu>ul>li>a');
 for(let i=0;i<aTags.length;i++){
     aTags[i].onclick=（xxx)=>{
        xxx.preventDefault();
        let a =xxx.currentTarget;
        let href=a.getAttribute('href')//"#siteAbout"
        let element = document.querySelector(href);
        this.scrollToElement(element)
     }
}
```

将MVC封装函数：
```
window.Model = function(options){
    let resourceName = options.resourceName;
    return {
        init : function(){},
        fetch: function(){},
        save: function(){}
    }
}

window.View = function(selector){
    return document.querySelector(selector);
}

window.Controller = function(options){
    let init = options.init;

    let object = {
        view: null,
        model: null,
        init: function(view,model){
            this.view = view;
            this.model = model;
            this.model.init();
            init.call(this.view);
            this.bindEvents.call(this)
        }
    };
    for(let key in options){
        if(key !== 'init'){
            object[key] = options[key]
        }
    }
    return object;
}
```