---
title: SASS常用方法
date: 2018-09-20 07:29:31
categories: [SASS]
tags: [SASS,CSS]
---

# 一、基于Vue的SASS安装

- 安装Vue-cli 这里不介绍了
- 安装SASS
```
npm install node-sass --save-dev
npm install sass-loader --save-dev
```
- 在webpack.base.conf.js文件添加scss
```
{
    test: /\.scss$/,
    loaders: ["style", "css", "sass"]
},
```
- 在Vue单文件里使用SASS
```
<style scoped lang="scss" type="text/css">
```
- 启动 **npm run dev** 会热更新

# 二、SASS与SCSS的区别

- 主要是写法不一样，语法一样
- SASS格式
```
#sidebar
  width: 30%
  background-color: #faa
```
- SCSS格式
```
#sidebar {
  width: 30%;
  background-color: #faa;
}
```

# 三、SASS语法

## 1、变量

- SASS允许使用变量，所有变量以$开头
```
$blue : #1875e7;　
div{
　　color : $blue;
}
```
如果变量需要镶嵌在字符串之中，就必须需要写在#{}之中
```
$side : left;

.rounded {
　　border-#{$side}-radius: 5px;
}
```

## 2、计算功能
- SASS允许在代码中使用算式：
```
body {
　　　　margin: (14px/2);
　　　　top: 50px + 100px;
　　　　right: $var * 10%;
}
```

## 3、嵌套
- SASS允许选择器嵌套。比如，下面的CSS代码：
```
div h1 {
　　　　color : red;
}
```
可以写成：
```
div {
　　hi {
　　　　color:red;
　　}
}
```
- 在嵌套的代码块内，可以使用&引用父元素。比如a:hover伪类，可以写成：
```
a {
　　&:hover { color: #ffb3ff; }
}
```

## 4、注释

- 标准的CSS注释 /* comment */ ，会保留到编译后的文件。

- 单行注释 // comment，只保留在SASS源文件中，编译后被省略。

## 5、继承

- SASS允许一个选择器，继承另一个选择器
```
.class1 {
　　border: 1px solid #ddd;
}
```
class2要继承class1，就要使用@extend命令
```
.class2 {
　　@extend .class1;
　　font-size:120%;
}
```
## 6、Mixin

- 使用@mixin命令，定义一个代码块,是可以重用的代码块
```
@mixin left {
　　float: left;
　　margin-left: 10px;
}
```
使用@include命令，调用这个mixin
```
div {
　　@include left;
}
```
- mixin的强大之处，在于可以指定参数和缺省值
```
@mixin left($value: 10px) {
　　　float: left;
　　　margin-right: $value;
}
```
使用的时候，根据需要加入参数
```
div {
　　@include left(20px);
}
```

## 7、插入文件

- @import命令，用来插入外部文件
```
@import "path/filename.scss";
```
- 如果插入的是.css文件，则等同于css的import命令

```
@import "foo.css";
```

## 8、条件语句

- @if可以用来判断：
```
p {
　　@if 1 + 1 == 2 { border: 1px solid; }
　　@if 5 < 3 { border: 2px dotted; }
}
```
- 配套的还有@else命令：
```
@if lightness($color) > 30% {
　　background-color: #000;
} @else {
　　background-color: #fff;
}
```

## 9、循环语句

- SASS支持for循环：
```
@for $i from 1 to 10 {
　　.border-#{$i} {
　　　　border: #{$i}px solid blue;
　　}
}
```
- 也支持while循环：
```
$i: 6;
@while $i > 0 {
　　.item-#{$i} { width: 2em * $i; }
　　$i: $i - 2;
}
```

## 10、自定义函数

- SASS允许用户编写自己的函数
```
@function double($n) {
　　　@return $n * 2;
}

#sidebar {
　　　width: double(5px);
}
```