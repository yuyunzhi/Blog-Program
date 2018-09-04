---
title: 如何用原生JS做一个画板？
date: 2018-09-04 13:43:47
categories: [编程作品集]
tags: [JavaScript,Canvas,移动端,特性检测,SVG]
---

<img src="https://www.yuyunzhi.xyz/img/6.jpg" width="70%">

这是我做的第一个项目画板，故记录知识点方便以后回顾。有两种方式：div和canvas，选择了用canvas做。

**关键词:**原生JavaScript、Canvas、移动端、SVG、特性检测

**描述：**该项目使用原生JS实现，主要调用 Canvas API，实现了线粗、调色、橡皮擦、保存等功能。用 context.clearRect()实现了 橡皮檫和清屏的功能，用 className切换实现了笔的线粗、颜色切换的功能，用meta:vp、特性检测、ontouch事件实现了触屏设备与web端兼容。

**源码链接**：[点这里](https://github.com/yuyunzhi/drawingBoard)
**预览链接**：[点这里](https://yuyunzhi.github.io/drawingBoard/drawing-canvas/canvas.html)

# 一、div VS canvas 选哪个？

如果用div做画板，那么思路是这样的：

- document.onmousedown：鼠标点击画一个圆，获取点击的坐标；创建div；设置div样式；
- document.onmousemove：移动创建圆：把1复制到2里，然后使用标记，未点击是false，点击是true，松开是false；
- doucment.onmouseup：鼠标松开，完成绘绘画；


然后缺点是**快速移动无法形成连续的圆**，so不能用 div来做画板。而canvas解决了这个不足的地方。

让我们看下如何一步步用canvas实现画板。

# 二、cavans提供的画图API

## 1、使用canvas

```
var canvas=document.getElementById('canvas');
var context=canvas.getContext('2d');
```

## 2、封装一个画实心圆的函数

```
function drawCir(x,y){
    context.beginPath()
    context.arc(x,y,0.1,0,Math.PI*2)
    context.fill();
}
```

## 3、封装一条画直线的函数

```
function drawLine(x1,y1,x2,y2){
    context.beginPath();
    context.lineWidth=2;
    context.moveTo(x1,y1);
    context.lineTo(x2,y2);
    context.stroke();
    context.closePath();  
}
```

**注意**context.cloasePath()，表示关闭路径，线宽在开始和结束只能用一次，如果有多个颜色就要进行多次的开始和结束。

## 4、删除内容，橡皮檫

```
context.clearRect(x坐标,y坐标,长,宽);
//x和y的坐标都可以动态获取
```

更多canvas API可以查看MDN:

[Canvas基本用法](https://developer.mozilla.org/zh-CN/docs/Web/API/Canvas_API/Tutorial/Basic_usage)


# 三、然后怎么做？

## 1、首先我们要给画板设置视口宽高，并封装成函数

```
function setCanvasSize(){
    var pageWidth=document.documentElement.clientWidth;
    var pageHeight=document.documentElement.clientHeight;
    canvas.width = pageWidth;
    canvas.height = pageHeight;
 }
```

如果我们窗口变化了，可以调用这个函数重新设置canvas的宽高。

## 2、实现能画图

```
var using=false;
var lastPoint={
    x:undefined,y:undefined
}
//鼠标点击监听
canvas.onmousedown=function(aaa){
 var x=aaa.clientX;
 var y=aaa.clientY;
    using=true;
    lastPoint={x:x,y:y};
 if(eraserEnabled){
        context.clearRect(x-10,y-10,20,20);
    }else{
        drawCir(x,y);
    }
}

//鼠标移动监听
canvas.onmousemove=function(aaa){
 var x=aaa.clientX;
 var y=aaa.clientY;
 var newPoint={x:x,y:y}
 if(using){
 if(eraserEnabled){
            context.clearRect(x-10,y-10,20,20);
      }else{
            drawCir(x,y);
            drawLine(lastPoint.x,lastPoint.y,newPoint.x,newPoint.y)
            lastPoint=newPoint;      
        }
    }
}       

//鼠标松开监听
canvas.onmouseup=function(aaa){
    using=false;
}
```

**注意**做一个“锁”来切换点击状态 var using=false。

## 3、引入SVG：铅笔、橡皮、清屏、下载

在iconfont里找到自己想要的icon，按照说明帮助使用smybol引用：

[iconfont-阿里巴巴矢量图库](http://iconfont.cn/)


## 4、通过点击事件用js切换className

```
xxx.onclick=function(){
    xxx.classList.add('active');
    yyy.classList.remove('active');
}
```
解释：给一个元素的状态激活增加active，给另一个元素的取消激活状态移除active。xxx代表点击事件监听的元素，比如pen的粗细切换、颜色切换，pen和eraser切换进行红色高亮。

## 5、增加清屏功能

```
clear.onclick=function(){
    context.clearRect(0,0,canvas.clientWidth,canvas.clientHeight);
}
```

## 6、增加下载图片功能

```
download.onclick = function(){
    var url = canvas.toDataURL('image/png');
    var a = document.createElement('a');
    document.body.appendChild(a);
    a.href=url;
    a.download='my drawing';
    a.click();
}
```

## 7、解决移动页面宽度（在手机能看）

在head加一个meta:vp:

```
<meta name="viewport" content="width=device-width,initial-scale=1,user-scalable=no,maximum-scale=1.0,minimum-scale=1.0"/>

页面宽度=移动宽度 ：width=device-width
用户不可以缩放：user-scalable=no
缩放比例：initial-scale=1
最大缩放比例：maximum-scale=1.0
最小缩放比例：minimum-scale=1.0
```

具体可看我之前写的文章：

[移动端是怎么做适配的？](https://zhuanlan.zhihu.com/p/36851344)


## 8、特性检测没有onmouse事件，用ontouch事件

```
document.ontouchstart =function(){}
document.ontouchmove = function(){}
document.ontouchend = function(){}
```

## 9、特性检测

```
if(document.body.ontouchstart!==undefined){
    //是触屏设备
 }else{
    //不是触屏设备
 }
 ```

 # 四、解决一些问题

 ## 1、当窗口变大变小，能够保证画板自适应的放缩

这里需要注意的是，onresize响应事件处理中，获取到的页面尺寸参数是变更后的参数。

```
window.onresize =function(){
    setCanvasSize()
}
```

## 2、用http协议预览

使用了svg，那么file协议的预览就无法看到图片，所以打开node.js输入命令行安装一个http协议预览的功能，输入命令：

```
npm i -g http-server
```

运行

```
http-server -c-1 //清除缓存预览
hs -c-1 //与上面等价
```
