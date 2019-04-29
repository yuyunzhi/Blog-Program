---
title: '初学jQuery,从封装函数开始'
date: 2018-09-04 15:00:07
tags: [jQuery]
categories: [jQuery]
---

# 一、封装两个函数getSiblings()、addClass()

## 1、getSiblings()根据一个元素的id，获取该元素的兄弟元素id的集合

**逻辑：**
1、找到node的父元素的所有子元素id集合allChildren;
2、初始化一个空对象用于存放查询的集合，即伪数组(没有push等功能);
3、遍历allChildren;
4、判断allChildren[i]!==node是否为真;
5、若为真，array[array.length]=allChildren[i],注意这是伪数组没有push的方法;
6、调用方式:getSiblings(item3)
7、代码如下：
```
function getSiblings(node){  //API
  var allChildren=node.parentNode.children
  var array = {
  length:0
}
for(var i=0; i<allChildren.length;i++){
  if(allChildren[i]!==node){
    array[array.length]=allChildren[i];
    array.length+=1
  }
}
return array
}
```
## 2、addClass()根据一个元素的id，给改元素增加一个class

node是一个元素的id，classes是一个数组如：var classes = {'a':true,'b':false,'c':true}

**逻辑：**

1、对该classes遍历，因为该classes不是以数字为key，所以用key in classes;
2、判断value是否为真;
3、如果为真，那么给该node增加一个class;
4、调用方式如：addClass(item2,{book:true})
5、代码如下：

```
function addClass(node,classes){
   //对一个哈希进行遍历
   for(var key in classes){
   var value = classes[key]
   if(value){
      node.classList.add(key)
    }
   }  
}
```

# 二、命名空间、Node.prototype、Node2

## 1、命名空间

```
window.yyzdom = {}
yyzdom.getSiblings = function (node){} //把花括号代码块写进来
yyzdom.addClass = function (node，classes){} //把花括号代码块写进来
```

调用方式：
```
yyzdom.getSiblings(item3)
yyzdom.addClass(item3,{book:true,fruit:false})
```

缺点：调用方式不习惯，最好能item3.getSiblings()这种

## 2、Node.prototype 在共有属性里增加函数

```
Node.prototype.getSiblings = function (){} //把花括号代码块写进来,注意用this来代替node
Node.prototype.addClass = function (node，classes){} //把花括号代码块写进来
```

调用方式：

```
item3.getSiblings()
//上述等价于item3.getSiblings.call(item3)
item3.addClass(classes)
//上述等价于item3.addClass.call(item3,classes)
```

## 3、Node2改为jQuery,jQuery改为$

**版本一：接受传node**

```
window.Node2 = function(node){
    return{
        getSiblings:function(){},//用“一”中的代码
        addClass:function(){}，//用“一”中的代码
    }
}

var node2 = Node2(item3)
node2.getSiblings()
node2.addClass(xxx)
```

注意：这就是jQuery的模式

**版本二：接受传多node和字符串**

```
window.jQuery = function(nodeOrSelector){
    //判断多个参数还是一个参数
    let nodes = {}
    if (typeof nodeOrSelector === "string"){
        let temp = document.querySelectorAll(nodeOrSelector);//伪数组
        for(let i =0;i<temp.length;i++){
            nodes[i]=temp[i]
        }
        nodes.length=temp.length
    }else if(nodeOrSelector instanceof Node){
        nodes = {
            0:nodeOrSelector,
            length:1
        }
    }

    nodes.getSiblings:function(){},//用“一”中的代码，要有return
    nodes.addClass:function(node){}，//用“一”中的代码,要有return
    nodes.setText = function(text){
    var texts = []
    for(let i=0;i<nodes.length;i++ ){
        nodes[i].textContent = text
    }       
    }
    return nodes
}

window.$=jQuery
var $node2 = $('ul>li:nth-child(3)')
$node2.getSiblings()
$node2.addClass(['blue'])
$node2.setText('hi')
```

注意：这里使用了闭包