---
title: 'http请求响应,curl命令用法'
date: 2018-09-04 14:28:28
categories: [HTTP]
tags: [http,命令行]
---

> HTTP 请求包括哪些部分，如何用Chrome开发者工具查看 HTTP 请求内容?
HTTP 响应包括哪些部分，如何用Chrome开发者工具查看 HTTP 响应内容?
如何使用 curl 命令?

# 一、笔记
1、李爵士发明了URI（统一资源标识符）、HTTP（超文本传输协议）、HTML（超文本标记语言）；
2、URL（统一资源定位符）、URN（统一资源名称）；
3、URL组成：协议+域名+路径+查询参数+锚点+端口，http://baidu.com是二级域名，http://www.baidu.com是三级域名；
4、DNS（域名解析系统）：输入域名，输出IP；
5、server服务器、client客户端、http协议，一个端口只做1件事，80端口用来服务http；
6、浏览器负责发起请求，服务器在 80 端口接收请求，服务器负责返回内容（响应），浏览器负责下载响应内容，HTTP 的作用就是指导浏览器和服务器如何进行沟通；
7、状态码解释：[点这里](https://zh.wikipedia.org/wiki/HTTP%E7%8A%B6%E6%80%81%E7%A0%81)

# 二、http请求包含部分

## 1、格式

```
1动词 路径 协议/版本
2 Key1: value1
2 Key2: value2
2 Key3: value3
2 Content-Type: application/x-www-form-urlencoded
2 Host: www.baidu.com
2 User-Agent: curl/7.54.0
3
4 要上传的数据
```

**解释：**请求最多包含4部分，最少包含3部分；第3部分永远是一个回车；动词可以是GET（获取） POST（上传）PUT（整体更新）PATCH（局部更新）DELETE（删除）HEAD OPTIONS等；路径包含查询参数但不包括锚点；如果没有写路径，默认为/；Content-Type标注了第四部分的格式；

## 2、GET请求例子

```
1 GET / HTTP/1.1
2 Host: www.baidu.com
2 User-Agent: curl/7.54.0
2 Accept: */*
2 Frank: xxx
3
```

## 3、POST请求例子

```
1 POST /path HTTP/1.1
2 Host: baidu.com
2 Accept:application/json
2 Conetent-Type:application/x-www-form-urlencoded
2 Conetent-Length:1000
3
4 username=ff&password=123
```

# 三、用Chrome查看请求

1、打开 Network
2、地址栏输入网址
3、在 Network 点击查看 request，点击「view source」点击「view source」点击「view source」
4、可以看到请求的前三部分了
5、如果有请求的第四部分，那么在 FormData 或 Payload 里面可以看到

# 四、http响应包含部分

## 1、格式
```
1 协议/版本号 状态码 状态解释
2 Key1: value1
2 Key2: value2
2 Content-Length: 17931
2 Content-Type: text/html
3
4 要下载的内容
```

## 2、例子

```
HTTP/1.1 302 Found
Connection: Keep-Alive
Content-Length: 17931
Content-Type: text/html
Date: Tue, 10 Oct 2017 09:19:47 GMT
Etag: "54d9749e-460b"
Server: bfe/1.0.8.18
```

# 五、用Chrome查看响应

1、打开 Network
2、输入网址
3、选中第一个响应
4、查看 Response Headers，点击「view source」
5、你会看到响应的前两部分
6、查看 Response 或者 Preview，你会看到响应的第 4 部分

# 六、如何使用curl命令

## 1、GET请求访问百度的命令：

```
curl -s -v -- "https://www.baidu.com"
```

## 2、如果要获取网页时使用额外的标题，可以输入命令：

```
url -s -v -H "Lambert: xxx" -- "https://www.baidu.com"
```

## 3、POST请求使用额外标题访问QQ的命令：

```
curl -X POST -s -v -H "Lambert: xxx" -- "https://www.qq.com"
```

## 4、POST请求访问并指定数据发送HTTP服务器命令：
```
curl -X POST -d "111111111" -s -v -H "Lambert: xxx" -- "https://www.qq.com"
```

## 5、下载一个页面的内容
```
curl 网址>本地文件夹路径
```