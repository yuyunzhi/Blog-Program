---
title: 标签用法：iframe a form input table
date: 2018-09-04 14:42:46
categories: [Html]
tags: [Html]
---

# iframe与a标签嵌套：
```
<iframe name="xxx" src="http://baidu.com" frameborder="1"></iframe>
<a target="xxx" href="http://qq.com">qq</a>
```
****解释：****
1、默认打开情况，iframe打开的是百度页面；
2、iframe标签的name为xxx，与a标签target为xxx向对应结合使用，那么点击a标签，打开的是QQ页面；
3、frameborder不写的话会有一个默认border，往往不写的话比较难看；


# 一、a标签（get请求）

## 1、可以写一个跳转链接：
```
<a target="xxx" href="http://qq.com"target="_blank">qq</a>
<a target="xxx" href="http://qq.com"target="_self">qq</a>
<a target="xxx" href="http://qq.com"target="_parent">qq</a>
<a target="xxx" href="http://qq.com"target="_top">qq</a>
```

在HTML中target目标的四个参数的用法：
```
target="_blank"表示：将链接的画面内容，在新的浏览视窗中打开。打开新窗口；
target="_self"表示：在自己的窗口打开。如果被iframe 嵌套，这在当前这个iframe窗口打开，而不是当前浏览器窗口；
target="_parent"表示：将链接的画面内容，当成文件的上一个画面。即：当父窗口打开。
target="_top"表示：将框架中链接的画面内容，显示在没有框架的视窗中（即除去了框架)。即：顶端打开窗口。
```

## 2、点击a标签下载文件
```
 <a  href="http://qq.com" download>下载</a>
```

## 3、a标签的href到底可以写什么？

```
href="xxx.html"    //就会跳到: 当前地址/xxx.html
href="？name=qqq"  //就会跳到: 当前地址/？name=qqq
href="javascript:alert(1);"  //点击执行javascript内容
href="javascript: ;"     //点击后什么也不做的a标签-伪协议
href="#"     //页面会回到头部
```


# 二、form标签（post请求）

```
<form action="users" method="post">
    <label for="yyy">用户名</label>
    <input type="text" name="账号" id="yyy">
    <label>密码<input type="password" name="密码" ></label>
    <input type="submit" value="提交">
</form>
```

**解释：**
1、form表单里面的input的type没有submit属性，就无法提交；
2、label标签的for=yyy 与 input标签的id=yyy 同时出现，点击文字就能聚焦到输入框，或用label套input也行；

get默认会把参数出现在查询参数上；
post会把参数放入请求的第四部分；
我们不能让get请求出现在第四部分；
post可以把参数出现在查询参数，在action="users/use?name=1";

# 三、input标签type属性

## 1、能否提交button和submit?

```
<input type="submit" value="提交">
<button>按钮</button>
```
**解释：**两者都可以提交

```
<button type="button">按钮</button>
```
**解释：**不能提交

```
<button type="submit">按钮</button>
```
**解释：**可以提交

总结，button没有写type会自动升级为type="submit"使得可以提交。

## 2、多选框 type=“checkbox” name分组

喜欢的水果
```
<label><input type="checkbox" name="fruit" value="1"> 苹果 </label>
<label><input type="checkbox" name="fruit" value="2"> 香蕉 </label>
<label><input type="checkbox" name="fruit" value="3"> 哈密瓜 </label>
```

## 3、单选框 type=“radio” 及 name分组

```
<label><input type="radio" name="cool" value="yes"> yes </label>
<label><input type="radio" name="cool" value="no"> no </label>
<label><input type="radio" name="cool" value="noyes"> noyes </label>
```

## 4、选择框select

```
<select name="group" multiple >
   <option value="">-</option>
   <option value="1">第一组</option>
   <option value="2">第二组</option>
   <option value="3" disabled>第三组</option>
   <option value="4" selected>第四组</option>
</select>
<button type="submit"> button</button>
```

**解释：**
默认单选，加了multiple可以多选
disabled默认不能选
selected默认已经选择了

## 5、textarea多行文本

```
<textarea  name="hobby" cols="30" rows="10"></textarea>

```
可以增加css属性来调整textarea固定宽高或用cols和rows来定宽高

```
style="resize:none;width:100px;heigh:100px"

```

## 6、table标签

```
<table>
   <colgroup>
      <col width=100>
      <col width=200>
   </colgroup>
   <thead>
      <tr>
         <td>姓名</td><td>年龄</td>
      </tr>
   </thead>
   <tbody>
      <tr>
         <td>小红</td><td>12</td>
      </tr>
      <tr>
         <td>小明</td><td>15</td>
      </tr>
   </tbody>
   <tfoot>
      <tr>
         <td>XXX</td><td>yyy</td>
      </tr>
   </tfoot>
</table>
```

**解释：**
1、table标签有三个子元素：thead、tbody、tfoot，顾名思义；即使换了顺序浏览器也一定会按照顺序展示的；
2、table标签另一个元素：colgroup来设置每一列的宽度
3、tr表示列，td表示数据