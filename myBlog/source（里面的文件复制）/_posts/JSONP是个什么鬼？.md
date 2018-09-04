---
title: JSONP是个什么鬼？
date: 2018-09-04 15:27:55
categories: [JavaScript]
tags: [JSONP，跨域]
---

<img src="https://pic1.zhimg.com/v2-d95c4c3b461e89c4eadc90bf9b699a6e_1200x500.jpg" width="70%">

花了一天的时间学习JSONP，总结一下以后方便回顾。在了解什么是JSONP前，需要先了解什么是数据库，如何向服务器进行请求得到响应，可以用那些方式发送请求？如何向不同的服务器发送请求并获得响应？然后推出啥是JSOPN？内容有些长~~

# 一、数据库是什么？

- 文件系统就是一种数据库：能长久存数据，能够对内容进行增、删、改、储存

- MySQL是一种数据库

# 二、向数据库做加法

> 要做的流程如下：
点击“提交1元钱”发送请求给服务器，服务器修改数据库内容，响应返回所需数据。
用两种方式来做：图片请求；script请求；

<img src="https://pic3.zhimg.com/80/v2-f17585903314412fbd2ce0e106c80ff5_hd.jpg" width="70%">

**方案一：用图片的src向服务器发送get请求**

html代码如下：

```
<body>
    <p>账户金额：<span id="amount">!!!amount!!!</span></p>
    <button id="button">付款1元钱</button>
    <script>
        button.addEventListener('click', (e)=>{
            let image = document.createElement('img')
            image.src = '/pay'
            image.onload = function(){ // 状态码是 200~299 则表示图片请求成功
                alert('成功')
            }
            amount.innerText = amount.innerText -1
            image.onerror = function(){ // 状态码大于等于 400 则表示图片请求失败
                alert('失败')
            }
        })
    </script>
</body>
```

其中!!!amount!!!表示一个占位符，后端通过获取这个占位符把数据库里的内容赋值给它。

**解释：**当用户点击button按钮的时候，用图片发送了路径为/pay的GET请求给服务器。服务器收到后执行下面的代码，依次是：
1、判断路径
2、读取数据库的内容，假如是9966。原始数据设置的是10000
3、在最新的内容减1，并保存给变量newAmount
4、把newAmount保存到数据库，那么这个时候的值应该为9965
5、确定返回给浏览器的内容类型为 image/jpg
6、返回一张图片
7、结束

服务器代码如下：
```
if(path === '/'){
    let string = fs.readFileSync('./index.html','utf8')
    let amount = fs.readFileSync('./db.xxx','utf8') //db.xxx文件储存字符串10000
    string = string.replace('!!!amount!!!',amount)
    response.statusCode = 200
    response.setHeader('Content-Type', 'text/html;charset=utf-8')
    response.write(string)
    response.end()
  }else if(path === '/style.css'){
    let string = fs.readFileSync('./style.css','utf8')    
    response.statusCode = 200
    response.setHeader('Content-Type', 'text/css;charset=utf-8')
    response.write(string)
    response.end()
  }else if(path ==='/pay'){
    let amount = fs.readFileSync('./db.xxx','utf8')
    var newAmount = amount -1
    fs.writeFileSync('./db.xxx',newAmount)    
    response.setHeader('Content-Type', 'image/jpg')    
    response.write(fs.readFileSync('./1.jpg'))
    response.end()
  }else{
    response.statusCode = 404
    response.setHeader('Content-Type', 'text/html;charset=utf-8')
    response.write('呜呜呜')
    response.end()
  }
```

**image发送get请求的缺点：**得返回一张图片，同时无法获取其他的数据内容。

<img src="https://pic3.zhimg.com/80/v2-a172c74cca7f6800e8fe663f60cb870f_hd.jpg" width="70%">

**方案二：用script的src向服务器发送get请求**

html代码如下：

```
<body>
    <p>账户金额：<span id="amount">!!!amount!!!</span></p>
    <button id="button">付款1元钱</button>
    <script>      
    button.addEventListener('click', (e)=>{
        let script = document.createElement('script')
        script.src = '/pay'
        document.body.appendChild(script)  //image是不需要插入，但script要插入，记住
        script.onload = function(e){   // 状态码是 200~299 则表示图片请求成功
            e.currentTarget.remove()   //删除script标签 
            alert('成功')
        }
        script.onerror = function(){ // 状态码大于等于 400 则表示图片请求失败
            e.currentTarget.remove()   //删除script标签 
            alert('失败')
    }
        })
    </script>
</body>
```

**解释：**当用户点击button按钮的时候，用scrip发送了路径为/pay的GET请求给服务器。服务器收到后执行下面的代码，依次是：
1、判断路径
2、读取数据库的内容，假如是9966。原始数据设置的是10000
3、在最新的内容减1，并保存给变量newAmount
4、把newAmount保存到数据库，那么这个时候的值应该为9965
5、确定返回给浏览器的内容类型为 JavaScript
6、返回一段JS代码，用来更改浏览器用户看到的数字
7、结束

服务器代码如下（只有path ==='/pay'变动）：
```
else if(path ==='/pay'){
    let amount = fs.readFileSync('./db.xxx','utf8')
    var newAmount = amount -1
    fs.writeFileSync('./db.xxx',newAmount)   
    response.setHeader('Content-Type', 'application/javascript')  //返回内容类型为 js
    response.write(`
    amount.innerText=amount.innerText-1      
    `)   //返回JS代码执行，账户余额减1
    response.end()
}
```

**script请求与image请求相比优劣：**不用返回一个图片，速度回更快一些,同时可以返回其他的内容。但动态创建的script会被执行，响应结束立刻删除e.currentTarget.remove()

<img src="https://pic4.zhimg.com/80/v2-3f8a298fb504134627dc872498419b87_hd.jpg" width="70%">

这种技术就叫做 SRJ - Server Rendered JavaScript
这种技术就叫做 SRJ - Server Rendered JavaScript
这种技术就叫做 SRJ - Server Rendered JavaScript

# 三、跨域SRJ - JSONP

## 1、什么是JSONP（文字解释）

<img src="https://pic2.zhimg.com/80/v2-ff3397d3f0dd125dcfeafa417665a247_hd.jpg" width="70%">

## 2、代码解释

html代码如下：
```
<body>
    <p>账户金额：<span id="amount">!!!amount!!!</span></p>
    <button id="button">付款1元钱</button>
    <script>
    button.addEventListener('click', (e)=>{
        /***以下内容为服务器获取参数返回相关值并调用的函数***/
        let functionName = 'frank'+parseInt(Math.random()*10000,10)
        //frank12312423423
        window[functionName] = function(result){
        if(result === 'success'){
            amount.innerText = amount.innerText -1
        }else{
        }
    }
        /******以下内容为动态创建script发送请求,提供参数*********/
        let script = document.createElement('script')
        script.src = 'http://jack.com:8002/pay?callbackName='+functionName
        document.body.appendChild(script)  //image是不需要插入，但script要插入，记住
        script.onload = function(e){   // 状态码是 200~299 则表示图片请求成功
            e.currentTarget.remove()   //删除script标签 
            delete window[functionName]
        }
        script.onerror = function(){ // 状态码大于等于 400 则表示图片请求失败
            e.currentTarget.remove()   //删除script标签 
            delete window[functionName]
    }
        })
    </script>
</body>
```
**解释：**请求方为http://frank.com，请求http://jack.com的服务器（响应方），通过动态script标签来请求的。服务器收到请求后执行：

1、判断路径
2、读取数据库的内容，假如是9966。原始数据设置的是10000
3、在最新的内容减1，并保存给变量newAmount
4、把newAmount保存到数据库，那么这个时候的值应该为9965
5、确定返回给浏览器的内容类型为 JavaScript
6、返回内容为：用${query.callbackName}获取浏览器提供的参数yyy，返回请求方所需要的数据，可以是JSONP
7、结束

服务器代码如下：
```
else if(path ==='/pay'){
      let amount = fs.readFileSync('./db.xxx','utf8')//100
      var newAmount = amount -1
      fs.writeFileSync('./db.xxx',newAmount)   
      response.setHeader('Content-Type', 'application/javascript')
      response.write(`
        ${query.callbackName}.call(undefined,{
            "success":true,
            "left":${newAmount}
        `})     
      )
      response.end()
  }
```
**约定：**
1、callbackName 必须叫做 callback / jQuery callback
2、funcitonName 为 随机数 frank12312312312321325()

## 3、用jquery来实现JSONP，代码更加的简洁

服务器代码不变,html代码如下：
```
<body>
   <p>账户金额：<span id="amount">!!!amount!!!</span></p>
   <button id="button">付款1元钱</button
   <script>
   button.addEventListener('click', (e)=>{
  
      //这个方法不是ajax，只是一个动态script
       $.ajax({
           url: "http://jack.com:8002/pay",
           dataType: "jsonp",
           success: function( response ) {
               if(response === 'success'){
                    amount.innerText = amount.innerText - 1
                }
            }
         })
         $.jsonp()
     })
 </script>
     <script src='https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/jquery.min.js'></script>
</body>
```

**解释：**url写明响应方的路径；dataType写明回应的内容类型；当响应成功执行什么样的函数；其他复杂的删减内容jQuery已经做完了。注意这个方法不是ajax，只是一个动态script进行跨域请求，可以叫做JSONP。

# 四、两个问题

## 1、什么是JSONP

<img src="https://pic2.zhimg.com/80/v2-ff3397d3f0dd125dcfeafa417665a247_hd.jpg" width="70%">

## 2、JSONP为什么不支持POST请求？

因为JSONP是通过动态创建script来实现的跨域请求的，而script只能发送GET请求。