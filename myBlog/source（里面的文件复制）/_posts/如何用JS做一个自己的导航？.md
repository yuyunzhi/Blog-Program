---
title: 如何用JS做一个自己的导航？
date: 2018-09-04 14:11:22
categories: [编程作品集]
tags: [JavaScript,locationStorage,JSON,favicon,onerror,图床,CSS Gradient Generator]
---

<img src="https://i.loli.net/2018/05/27/5b098570a7ce3.png" width="70%" />


**关键词：**原生JavaScript、locationStorage、JSON、favicon、onerror、图床、CSS Gradient Generator

**描述：**该项目使用原生JS实现一个键盘导航，该导航支持百度、谷歌搜索，键盘字母绑定自定义的网站。用favicon实现了获取网站logo的url功能，用CSS Gradient Generator 实现了键盘颜色的渐变，用locationStorage实现了用户输入url保存到本地的功能。

**源码链接：**[点这里](https://github.com/yuyunzhi/navigation-demo)

**预览链接：**[点这里](https://yuyunzhi.github.io/navigation-demo/index.html)


# 一、页面分析

## 1、数据的储存


- 一个二维数组来表示n行键盘，这里的n为4
- 一个hash储存键（键盘的上的字母）值（网址）


## 2、视觉部分

css表示：定位、色彩、动画、hover

## 3、事件绑定


- 键盘点击事件：onkeypress
- 鼠标点击事件：onclick
- 图片加载失败：onerror


# 二、用到的知识点

## 1、关于locationStorage，注意保存的是字符串

```
localStorage.getItem(key)  //获取指定key本地存储的值
localStorage.setItem(key,value)  //将value存储到key字段
localStorage.removeItem(key)  //删除指定key本地存储的值
```

什么是locationStorage，可以看我的这篇文章：
[关于Cookie、Session、LocalStorage、Cache-Control](https://zhuanlan.zhihu.com/p/37171920)


## 2、关于JSON

```
JSON.stringfy()  //将对象、数组转换成字符串
JSON.parse()  //将字符串转成json对象
```

将locationStorage取出或存进去，都可以用JSON的API来进行转化。

## 3、favicon

在键盘里的button输入网站地址后确认，会在键盘左下角获取该网站的logo图，这里用到的是favicon，约定一个网站后面接/favicon.ico就会跳转到该网站logo的url，比如百度：
[百度logo，看url](https://www.baidu.com/favicon.ico)


## 4、onerror

onerror事件会在文档或图像加载过程中发生错误时被触发。因为我们要获取logo，假如没有获取成功，会使得页面很难看。所以可以……呈现出一个已经准备好的图片。

```
image.onerror=function(aaa){
    aaa.target.src='https://i.loli.net/2018/05/02/5ae95aee4a6c2.png';
}
```

## 5、图床

做好的图片转化为url使用，这里推荐一个网站：
[试试看](https://sm.ms/)


## 6、CSS Gradient Generator

这里推荐一个网站自动生成代码：
[试试看](http://www.colorzilla.com/gradient-editor/)


## 7、JS

获取元素

```
var xxx = document.getElementsByClassName('xxx')
var xxx = document.getElementById('xxx')
var xxx = document.querySelector('xxx')
```

找元素

```
var yyy=xxx.previousSibling  //找xxx的前面一个兄弟元素
var yyy=xxx.nextSibling  //找xxx的后面一个兄弟元素
var yyy=xxx.parentElement //找xxx的父元素
var yyy=xxx.firstChild //找xxx的第一个子元素，也可以是last子元素
```

创建元素

```
var xxx = document.createElement(tagName)
```

插入元素

```
yyy.appendChild(xxx)
```

所以会发现，一直在找元素，创建元素，插入元素。如果加一个for循环就可以批量创建元素了。

# 三、怎么做？

## 1、数据储存

```
var keys = {
    0:['1','2','3','4','5','6','7','8','9','0'],
    1:['q','w','e','r','t','y','u','i','o','p'],
    2:['a','s','d','f','g','h','j','k','l'],
    3:['z','x','c','v','b','n','m'],
    length:4,
}
 
 var hash={
    q:'www.qq.com',
    w:'www.weibo.com',
    e:'www.ele.me',
    z:'www.zhihu.com',
    m:'www.mcdonalds.com.cn'
    //可以不写全，也可以不写，让hash是一个空对象
}
```

## 2、遍历二维数组keys，创建所需要的元素

```
var index=0
while(index < keys['length']){
   var div1 =  creativeDiv('div');
   var row=keys[index];
   for(var number=0;number < row.length;number++){  
      var kbd = createKbd(row[number]);
      var button = createButton(row[number]);   
      var image = createImage(hash[row[number]]); 
      kbd.classList.add(row[number]) 
   }
   index=index+1;
}
```
**解释：**


- 创建4个div表示行，creativeDiv()
- 每个div创建对应数量的kbd表示键盘字母，createKbd()
- 每个kbd创建一个button用来点击输入网址，createButton()
- 每个kbd再创建一个img用来展示网站的logo，createImage()


**以上四个函数均已封装过，这里说一下createButton()**

```
function createButton(id){
    //插入button按钮，用户输入更改hash网址
    var  button = c('button');//创建button元素，封装过的函数
    button.id = id;
    kbd.appendChild(button);
   //绑定鼠标点击事件
    button.onclick = function(xxx){
       var button2=xxx.target;
       var image2=button2.previousSibling;
       var key = button2.id;
       x = prompt('请输入键位'+key+'对应的网站地址')
       hash[key] = x; //变更网址
       image2.src='http://'+hash[row[number]]+'/favicon.ico';
       image.onerror=function(aaa){
            aaa.target.src='https://i.loli.net/2018/05/02/5ae95aee4a6c2.png';
        }
        localStorage.setItem('zzz',JSON.stringify(hash)); 
        window.location.reload()
   }
    button.textContent = "E";
 return button;
}
```

**再说一下createImage()**

```
function createImage(domain){
    //插入image icon 图标
     var image=c('img');
     kbd.appendChild(image);
    //domain === hash[row[number]] 表示地址存在
     if(domain){
         image.src='http://'+hash[row[number]]+'/favicon.ico';
     } else{
         image.src='https://i.loli.net/2018/05/02/5ae95aee4a6c2.png';
     }
     image.onerror=function(aaa){
         aaa.target.src='https://i.loli.net/2018/05/02/5ae95aee4a6c2.png';
     }
  return image;
 }
 ```

 ## 3、事件监听

 ```
 body.onclick = function (evt){
    if(evt.target.localName == "input"){
       document.onkeypress=''; 
    }else if(evt.target.localName == "kbd"){
        var classNumber = evt.target.className[4];//key w ,w是在第5位
        website=hash[classNumber];
        isWebsite(website)
     }else{
        document.onkeypress=function(xxx){
        key=xxx['key'];  //将监听的键盘字母保存到变量key
        website=hash[key];  //获取hash数组的值（网站地址）保存变量website;
        //在当前窗口打开网站
        //location.href = "http://"+website
        //在新窗口打开网站
        isWebsite(website)         
         }
     }   
 }
 ```

 **解释：**

 
    - 当鼠标点击body的时候，判断点击的元素是input，还是kbd，还是其他
    - 当为input时，那么取消onkeypress的监听
    - 当为kbd时，获取kbd元素的className所对应的website，并打开跳转该网址
    - 其余情况对键盘进行监听，获取键盘所对应的website，并打开跳转该网址
 

 这里的 isWebsite()是为了解决一个bug,如果用户打开button什么也不输入直接点确定，那么hash存的值为undefined或null就均不跳转。

```
function isWebsite(website){
    if(website === undefined || website === null){
        return
    }else{
        window.open('http://'+website,'_blank');
    }
}
```

## 4、百度搜索、谷歌搜索跳转

```
<form class="formButton"name="s" action="http://www.baidu.com/s"> 
    <input class="text"type="text" id="q" name="wd" autocomplete="on" value="">
    <input type="button" class="button" onclick="window.open('http://www.baidu.com/s?wd='+s.wd.value,'')" value="百度一下">
    <input type="button" class="button" onclick="window.open('http://www.google.com/search?q='+s.wd.value,'')" value="谷歌搜索">
 </form>
```

 放上这段代码就可以了。。。

 ## 5、对代码优化，对函数进行封装
