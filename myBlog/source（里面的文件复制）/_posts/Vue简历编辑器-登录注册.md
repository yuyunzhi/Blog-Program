---
title: Vue简历编辑器-登录注册功能
date: 2018-09-04 13:06:08
categories: [编程作品集]
tags: [登录,注册,Vue,LeanCloud]
---

开始学习Vue了，先看了慕课网的入门视频，又看了官网的文档。然后开始做在Vue版本的在线简历编辑器。

<img src="https://pic1.zhimg.com/80/v2-662431eed397d8f24a7048a690328dd9_hd.jpg" width="70%"/>

# 一、注册、登录逻辑

## 1、用户进入网站界面，判断是否已经登录

- 已登录：有登出按钮
- 未登录：无登出按钮

## 2、用户无账号，进入注册界面

- 输入用户名、密码
- 点击提交，保存到数据库

## 3、用户注册成功，进入登录界面

- 输入用户名、密码
- 发送请求判断是否存在该用户

## 4、用户登录成功，可以进行操作

- 编辑简历内容
- 保存到数据库

# 二、用到哪些Vue的知识？

## v-on

- 缩写：@
- 用法：监听原生DOM事件。用在自定义元素组件上时，也可以监听子组件触发的自定义事件
- 参数：在监听原生 DOM 事件时，方法以事件为唯一的参数。如果使用内联语句，语句可以访问一个$event属性：
```
v-on:click="handle('ok', $event)"
```
- 此次用到监听的元素：
```
v-on:click="xxx"
v-on:input="xxx"
v-on:submit="xxx"
```
- 自定义事件：v-on:edit="xxx"

## v-show

```
v-show="xxx"
```

- xxx的值为true，该元素显示
- xxx的值为false，该元素不显示
- 与v-if的区别：v-show 改变的是display，而v-if是销毁或重建元素

## v-bind

```
v-bind:yyy="xxx"
```

- 缩写： ：
- 用法：绑定事件监听器。动态地绑定一个或多个特性，或一个组件 prop 到表达式。
- 如：:src、:class、:style、:yyy自定义事件

## v-cloak

- 在使用vue绑定数据的时候，渲染页面时会出现变量闪烁
- 在相关元素添加v-cloak，并更改css样式[v-cloak]{display:none}
- 原理：加载css隐藏该元素，加载 js删除v-cloak

## 发布订阅模式this.$emit('xxx',data)

## 组件，模板

```
Vue.component('editable-span', {
    props: ['value'],
    template: `
            <span v-show="editing">{{value}}</span>
        `,
    data() {
       return {
            editing: 'false'
        };
    },
    methods: {
        x(e) {
         this.$emit('edit', e.target.value);
        }
    }
});
```

# 二、一步一步实现

<img src="https://pic2.zhimg.com/80/v2-5302b3d530646a83514c87c9e21dc477_hd.jpg" width="70%"/>

## 1、用户注册账号

- 这里使用LeanCloud邮箱和密码注册
- 监听注册提交按钮触发提交事件:v-on:submit="onSignUp"
- LeanCloud提供注册方式

```
onSignUp(e){//注册提交触发事件
   // 新建 AVUser 对象实例
   var user = new AV.User();
   // 设置用户名
   user.setUsername(this.signUp.email);
   // 设置密码
   user.setPassword(this.signUp.password);
    // 设置邮箱
   user.setEmail(this.signUp.email);
   user.signUp().then(function (user) 
         console.log(user)
   }, function (error) {
         console.log(error)
    });
}
```

## 2、用户登录账号

- 监听登陆提交按钮触发提交事件:v-on:submit="onLogin"
- LeanCloud提供登陆验证

```
onLogin(e){ //登录提交触发事件
    AV.User.logIn(this.signOn.email, this.signOn.password).then((user)=>{
       this.loginVisible=false
       this.signOutVisible=true
    }, function (error) {
        if(error.code==211){
            alert('抱歉，邮箱不存在')                 
            }else if(error.code==210){
            alert('邮箱和密码不匹配')
            }else if(error.code==203){
            alert('邮箱已被注册')
            }else if(error.code==219){
            alert('登录失败次数超过限制，请稍候再试')
            }
        });
}
```

## 3、用户退出登陆

- 监听登出按钮，触发登出事件：v-on:click="signOut"
- LeanCloud提供用户登入信息清除方式

```
signOut(){//登出
    AV.User.logOut();
 // 现在的 currentUser 是 null 了
    if(currentUser!==undefined||currentUser!==null){
        alert('注销成功')
     this.signOutVisible=false
    }
     var currentUser = AV.User.current();
}
```

## 4、用户进入网站，检测是否已经登陆

- 已登录：有登出按钮
- 未登录：无登出按钮

```
app.initSignOutButton()
initSignOutButton(){
   let currentUserId = AV.User.current()
   if(currentUserId==null||currentUserId==undefined){
      this.signOutVisible=false
      return
   }else{
      this.signOutVisible=true
   }
}
```