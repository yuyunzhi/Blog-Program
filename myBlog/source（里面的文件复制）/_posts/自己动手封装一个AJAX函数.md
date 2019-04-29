---
title: 自己动手封装一个AJAX函数
date: 2018-09-04 15:37:15
categories: [JavaScript]
tags: [JavaScript,AJAX]
---

> 这是一件很有意思的事，封装一个抽象性函数，就相当于可以把这个函数用在类似相同的环境中。
1、知道AJAX的功能
2、把AJAX的功能封装成函数
3、用Promise优化封装的函数

# 一、AJAX有哪些功能？

- 客户端的JS发起请求（浏览器上的）
- 服务端的JS发送响应（Node.js上的）

## 1、JS 可以设置任意请求 header 吗？

- 第一部分 request.open('get', '/xxx')
- 第二部分 request.setHeader('content-type','x-www-form-urlencoded')
- 第四部分 request.send('a=1&b=2')

## 2、JS 可以获取任意响应 header 吗？

- 第一部分 request.status / request.statusText
- 第二部分 request.getResponseHeader() / request.getAllResponseHeaders()
- 第四部分 request.responseText

# 二、window.jQuery.ajax

之前写过window.jQuery，而这次写window.jQuery.ajax。

封装AJAX函数，JS代码如下：

```
window.jQuery.ajax = function(obj){
    let url=obj.url
    let method=obj.method
    let body=obj.body
    let successFn=obj.successFn
    let headers=obj.heaaders
    let failFn=obj.failFn
    let request = new XMLHttpRequest()
    request.open(method, url) // 配置request第一部分
    for(let key in headers){
        let value = headers[key];
        request.setRequestHeader(key,value)
    }
    request.onreadystatechange = ()=>{
        if(request.readyState===4){
            if(request.status >= 200 && request.status < 300){
                successFn.call(undefined,x)
                //假如同时调用f1和f2可以这样写这里的x===request.responseText
                //f1.call(undefined,x)
                //f2.call(undefined,x)           
            }else if(request.status >= 400){
                failFn.call(undefined,request)
            }
        }
    }
    request.send(body)//设置请求的第四部分
}
```
补充一个ES 6的知识点：**解构赋值**。所以上面声明的前6个let可以用解构赋值来优化代码，到文末的时候我再修改。

```
var a=1
var b=2;
[a,b]=[2,1]    //a与b的值交换
```

函数调用方式：
```
window.jQuery.ajax({
    url:'/xxx',
    method:'post',
    body:'a=1&b=2',
    headers:{
        'content-type':'application/x-www-form-urlencoded',
        'yukaka':'25'
    },
    successFn:function sss(x){console.log(x)},
    failFn:function fff(resquest){console.log('失败了')}  
})
```

然而successFn 和 failFn 是我定义的。**不同的程序员可能回调函数名是不一样，那么在用一个封装过的函数就需要查看手册了传什么样的参数。这不是很方便，那怎么办呢?**

# 三、用Promise来优化函数

用法如下：
```
const promise = new Promise(function(resolve, reject) {
  // ... some code

  if (/* 异步操作成功 */){
    resolve(value);
  } else {
    reject(error);
  }
});
```

用Promise优化AJAX函数：
```
window.jQuery.ajax = function({url,method,body,headers}){ //这里使用ES6 解构赋值
    return new Promise(function(resolve,reject){   //resolve表示成功，reject表示失败
        let request = new XMLHttpRequest()
        request.open(method, url) // 配置request第一部分
        for(let key in headers){  //遍历，设置请求第二部分
            let value = headers[key];
            request.setRequestHeader(key,value)
        }
        request.onreadystatechange = ()=>{  //获取readyState码
            if(request.readyState===4){
            if(request.status >= 200 && request.status < 300){   //异步操作成功
                  resolve.call(undefined,request.responseText)        
             }else if(request.status >= 400){
                  rejcet.call(undefined,request)
              }
            }
        }
        request.send(body)//设置请求的第四部分
    })
}
```
函数调用方式：
```
myButton.addEventListener('click', (e)=>{
    window.jQuery.ajax({
        url:'/xxx',
        method:'post',
        body:'a=1&b=2',
        headers:{
           'content-type':'application/x-www-form-urlencoded',
           'yukaka':'25'
        }    
    }).then(
        (text)=>{console.log("响应成功，调用第一个函数");return 1},//成功调用这个函数       
    ).then(
        (text)=>{console.log(text)},//成功调用这个函数，这里的参数是上一个then返回的值
        (request)=>{console.log('error')} //失败调用这个函数     
    ) 
})
```
关于then，第一个函数表示的是异步操作成功时调用的函数，第二个函数表示的是失败时调用的函数。同时then接受多个函数的调用，方法就是链式操作。需要注意的是成功调用这个函数，它的参数是上一个then返回的值。
```
function xxx(){
    return new Promise((f1, f2) => {
        doSomething()
        setTimeout(()=>{
            // 成功就调用 f1，失败就调用 f2
        },3000)
    })
}
xxx().then(success, fail)
xxx().then(success, fail).then(success, fail)  // 链式操作
```