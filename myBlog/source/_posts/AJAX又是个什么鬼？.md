---
title: AJAX又是个什么鬼？
date: 2018-09-04 15:53:38
categories: [JavaScript]
tags: [JavaScript,AJAX]
---

# 一、我们如何向服务器发送请求？

- 用 form 可以发请求，但是会刷新页面或新开页面
- 用 a 可以发 get 请求，但是也会刷新页面或新开页面
- 用 img 可以发 get 请求，但是只能以图片的形式展示
- 用 link 可以发 get 请求，但是只能以 CSS、favicon 的形式展示
- 用 script 可以发 get 请求，但是只能以脚本的形式运行

那么有没有什么方式，可以使用get、post、put、delete 请求都行，并且用任意的方式返回给浏览器展示？

当然有，**这个就是AJAX。**

# 二、什么是AJAX？

Jesse James Garrett 讲同时满足以下技术取名叫做AJAX:异步的 **JavaScript 和 XML**。

- 使用 XMLHttpRequest 发请求
- 服务器返回 XML 格式的字符串
- JS 解析 XML，并更新局部页面

**如何使用XMLHttpRequest？**

JS文件代码如下：

```
myButton.addEventListener('click', (e)=>{
    let request = new XMLHttpRequest()
    request.open('get', '/xxx') // 配置request，请求方式和路径
    request.onreadystatechange = ()=>{
        if(request.readyState===4){  //当请求相应完毕，执行以下代码
            if(request.status >= 200 && request.status < 300){  //判断状态码
                let string = request.responseText    
                // 把符合 JSON 语法的字符串,转换成 JS 对应的值
                let object = window.JSON.parse(string)  
                // JSON.parse 是浏览器提供的
                console.log(typeof object)//得到的值为object类型
                console.log(object)   // 得到后端返回字符串转成符合JSON语法的对象
                console.log(object.note.from) //得到余咖咖
             }else if(request.status >= 400){
                console.log('说明请求失败') 
            }
        }
    }
    request.send()
})
```

**解释：**
- 当点击按钮的时候发送请求，请求过程从2开始
- 初始化new XMLHttpRequest()
- 配置request的请求方式和路径
- 获取readyState的值，并判断是否完成
- 判断状态码是否是请求成功
- 把返回的JSON语法的字符串转化成JS对应的值

其中，4里的readyState参考下图，或搜MDN查询
<img src="https://pic1.zhimg.com/80/v2-a0c060f011b32bc1eff4a55ec800c716_hd.jpg" width="70%">

其中，6里的JSON语法：
<img src="https://pic1.zhimg.com/80/v2-2e54545e5b49087ba16e0a9a9ef1e416_hd.jpg" width="70%">

JSON是一门语言，可在该网站学习：[JSON官网](http://json.org/)

后端代码如下：
```
else if(path === '/xxx'){
    response.statusCode = 200
    response.setHeader('Content-Type', 'text/xml;charset=utf-8')
 //response.setHeader('Access-Control-Allow-Origin', '请求方地址')
    response.write(`
    {
      "note":{
        "to": "xxx",
        "from": "余咖咖",
        "heading": "很高兴认识你",
        "content": "hi"
      }
    }
    `)
    response.end()
```

# 三、什么是同源策略？

同源策略要求 **协议+端口+域名** 一模一样才允许发 AJAX 请求。
举例：
```
http://xxx.com 可以向 http://www.xxx.com 发 AJAX 请求吗 no
http://xxx.com:80 可以向 http://xxx.com:81 发 AJAX 请求吗 no
```

**为什么要有同源策略？**

如果没有同源策略，就可以直接用POST、GET等向其他网站发送请求得到响应。任何一个网站都可以读取你的余额，直接进行支付转账，获取你写的私密信息，那么在互联网上就没有隐私安全了。

所以对浏览器要保证：

只有 **协议+端口+域名** 一模一样才允许发 AJAX 请求。

# 四、突破同源策略===跨域

假如A网站用ajax想给B网站发送请求并得到响应，那怎么办？即不同的域名要互相访问（请求方frank，访问响应方jack）。

用 **CORS，全称是"跨域资源共享"**（Cross-origin resource sharing）。它允许浏览器向跨源服务器，发出XMLHttpRequest请求，从而克服了AJAX只能同源使用的限制。

Jack在服务器加上这一段话：

```
response.setHeader('Access-Control-Allow-Origin', 'frank的地址')  
```

告诉服务器，frank是我朋友，不用阻止了。

**那CORS与JSONP的区别在哪？**

CORS与JSONP的使用目的相同，但是比JSONP更强大。

JSONP只支持GET请求，CORS支持所有类型的HTTP请求。JSONP的优势在于支持老式浏览器，以及可以向不支持CORS的网站请求数据。

# 五、一个问题

- 请用原生JS手写一个AJAX

```
let request = new XMLHttpRequest()
    request.open('get', '/xxx') 
    request.onreadystatechange = ()=>{
        if(request.readyState===4){ 
            if(request.status >= 200 && request.status < 300){ 
                console.log('说明请求成功了') 
                let string = request.responseText    
                let object = window.JSON.parse(string)  
             }  }else if(request.status >= 400){
                console.log('说明请求失败') 
            }
        }
    }
    request.send()
```

over~