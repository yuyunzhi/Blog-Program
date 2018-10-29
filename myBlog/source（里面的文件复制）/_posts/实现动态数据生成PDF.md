---
title: 实现动态数据生成PDF
date: 2018-10-22 18:59:03
categories: [Vue]
tags: [Vue]
---

最近在做一个业务功能，根据后端传来的数据给每个不同的用户动态生成不同的pdf并下载。

- 方案一、使用window.print()  
pass,打印出来的东西跟页面结构关联度太高，且不是pdf

- 方案二、使用pdfmake
当然还有其他的jsPDF，这里我选择了pdfmake

## 一、先搞定能够下载PDF

- github ：[官方说->pdfmake](https://github.com/bpampuch/pdfmake)

- Get Start

```
<!doctype html>
<html lang='en'>
<head>
  <meta charset='utf-8'>
  <title>my first pdfmake example</title>
  <script src='build/pdfmake.min.js'></script>
  <script src='build/vfs_fonts.js'></script>
</head>
<body>
```
引入 pdfmake.min.js
引入 vfs_fonts.js
可到cdnjs里获取链接：[cdnjs->](https://cdnjs.com/libraries/pdfmake)

- 创建内容

```
var docDefinition = { content: 'This is an sample PDF printed with pdfMake' };
```

- 下载、打开、打印内容

```
pdfMake.createPdf(docDefinition).download();
pdfMake.createPdf(docDefinition).open();
pdfMake.createPdf(docDefinition).print();
```

**然后这虽然可以下载，但是内容为中文时会乱码，以及需要设置样式，下面一一道来**

## 二、解决中文乱码(使用自定义样式)

### 方案一 

```
npm install pdfmake
```

- 用一个现成含有中文的字体替换原来的node_modules/pdfmake/dist/vfs_font.js
这里有现成的方正黑体简体:[进去下载vfs_font.js](https://github.com/aushion/pdfmake-chinese)

- js配置

```angular2html
/*设置默认字体,放入变量docDefinition里，与content同作用域*/
defaultStyle: {
    font: 'xxx'
}

/*设置字体 与变量docDefinition同作用域*/
pdfMake.fonts = {
     Roboto: {
         normal: 'Roboto-Regular.ttf',
         bold: 'Roboto-Medium.ttf',
         italics: 'Roboto-Italic.ttf',
         bolditalics: 'Roboto-Italic.ttf'
     },
     /*这里是加入的微软雅黑字体*/
     xxx: {
         normal: '方正黑体简体.ttf',
         bold: '方正黑体简体.ttf',
         italics: '方正黑体简体.ttf',
         bolditalics: '方正黑体简体.ttf',
     }
 };
```

### 方案二

- 创建一个vfs_fonts.js包含字体文件的新文件
- pdfMake.fonts在你的JavaScript中分配
- 指定doc-definition中的字体

**主要就这三个步骤,现在拆分多个小步骤**

1、git clone:[官方官网的代码到本地](https://github.com/bpampuch/pdfmake)
2、在clone下来的文件加中文字体文件：xx.ttf到 pdfmake\examples\fonts 目录下
3、npm install确保安装所有先决条件模块
4、安装gulp,我也是第一次用，其实没有那么复杂的。

```
npm install gulp -g
```
5、检查是否安装成功  

```
gulp -v
```

6、打开gulpfile.js 看下require了哪些模块，依次(一次)安装
7、安装 gulp-cli,没有安装会报错

```
npm install gulp-cli -g
```
8、运行gulp buildFonts以创建新的build/vfs_fonts.js

```angular2html
gulp buildFonts
```

9、这个时候就获得了方案一现成的vfs_fonts.js 并替换包里的vfs_fonts.js
10、引入至JS,我这里使用的是Vue

```angular2html
import xxx from '../../node_modules/pdfmake/build/pdfmake.min.js';
import yyy from '../../node_modules/pdfmake/build/vfs_fonts';
```
11、配置使用，同方案一


## 三、样式排版

- 参考这个网站，直接按照这个逻辑写就行了:[戳这里->](http://pdfmake.org/playground.html)
- 注意，既可以对所有类型设置统一的样式，也可以对每个类型单独设置权限更高的样式
- 把获取的数据存入变量，直接放入pdfmake模板，就动态更新pdf内容了。