---
title: 如何用jQuery做了一个2048游戏？
date: 2018-09-04 14:17:58
categories: [编程作品集]
tags: [JavaScript,jQuery]
---

<img src="https://i.loli.net/2018/06/25/5b30e5148d971.jpg" width="70%" />


**关键词：**JavaScript、jQuery

**描述：**使用jQuery动态获取元素，动态更改CSS样式，动态更改分值，伪协议初始化游戏。

**源码链接：**[点这里](https://github.com/yuyunzhi/2048-game)

**预览链接：**[点这里](https://yuyunzhi.github.io/2048-game/index.html)

# 一、如何创建二位数组

## 1、直接定义并初始化


```
var array=[['0-0','0-1'],['1-0','1-1']]
```

## 2、未知长度的二位数组

```
var board = new Array();
for(var i=0;i<4;i++){
   board[i]=new Array();
   for(var j=0;j<4;j++){
       board[i][j]=0;
   }
}
```

# 二、初始化如何随机生成2个随机数？

## 1、判断是否有空间可以生成
遍历所有数组，判断board[i][j]的值是为0，如果有为0，那么就存在

## 2、随机生成一个位置

```
var randx=parseInt(Math.floor(Math.random()*4));
var randy=parseInt(Math.floor(Math.random()*4));
```


- Math.floor()表示返回一个小于或等于给定数的最大整数，比如Math.floor(3.9) 得到3
- parseInt()需要传两个参数，第一个参数表示需要解析的内容，第二个表示进制(不写默认为10进制)


判断这个位置是否可以用，如果不可以用就继续生成

```
while(true){
    if(board[randx][randy]==0){
        break;
   }else{
       var randx=parseInt(Math.floor(Math.random()*4));
       var randy=parseInt(Math.floor(Math.random()*4));
   }
 }
 ```

 ## 3、随机生成两个数字2或4，将生成的数字与位置绑定，并调用动画函数（颜色，位置，背景）

 ```
 var randNumber=Math.random()<0.5?2:4;
 board[randx][randy]=randNumber;
 ```

 # 三、如何与用户交互

 ## 1、对document进行键盘的监听，然后用switch，分别调用上下左右移动的函数

 ```
 $(document).keydown(function(event){}
 ```

 ## 2、举例左←移动的函数:判断是否能够左右,满足以下两点则值为true

 ```
 遍历16个格子，board[i][j]!=0
 board[i][j-1]==0||board[i][j-1]==board[i][j]
 ```

 ## 3、当能够左移动时，有两种情况

 其一：自身值board[i][j]!=0，左边的格子数字值为0且不存在其他有数字的格子
    
 其二：自身值board[i][j]!=0，左边的格子数字与自己相等且不存在其他有数字的格子

 **注意break 和 continue的 区别**
 
 break：跳出循环，继续执行循环外的代码
    
 continue：停止当前循环，进行下一个循环

 
- 对于其一，两个位置的数值交换，同时颜色状态改变
- 对于其二，两个位置的数值相加，同时颜色状态改变，更新score分数
 

 ## 4、移动后的状态

 随机生成1个数，调用生成随机数函数

 ```
 function generateOneNumber(){
    //判断是否有空间生成位置数字
   if(hasspace(board)){
   //随机生成一个位置,并判断这个位置是否可以存在
   var randx=parseInt(Math.floor(Math.random()*4));
   var randy=parseInt(Math.floor(Math.random()*4));
  
   while(true){
     if(board[randx][randy]==0){
        break;
     }else{
        var randx=parseInt(Math.floor(Math.random()*4));
        var randy=parseInt(Math.floor(Math.random()*4));
      }
    }
  
   //随机生成2或4
   var randNumber=Math.random()<0.5?2:4;
  
   //将生成的数字附在生成的位置上，并产生动画；
      board[randx][randy]=randNumber;
      showNumberWithAnimation(randx,randy,randNumber);
  
  }else{
   return false;
   }
  }
```

并判断是否游戏结束（没有位置，也不能移动）

```
function nospace(board){
    for(var i=0;i<4;i++){
       for(var j=0;j<4;j++){
          if(board[i][j]==0){
              return false;
           }
       }
    }
    return true;
   }
function nomove(board){
    if(canMoveDown(board)||canMoveLeft(board)||canMoveLeft(board)||canMoveRight(board)){
        return false;
    }else{
        return true;
    }
}
```