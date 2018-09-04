---
title: Vue简历编辑器-预览、编辑功能
date: 2018-09-04 13:29:31
categories: [编程作品集]
tags: [Vue,Vue-Router,LeanCloud]
---

今天一个bug花了我一半天才搞定。所以打代码前应该先把逻辑理顺了再动手，逻辑没有理顺，打出来的代码就像走进死胡同一样。。。出都出不来。

首先要明确一点：每一个注册的用户，LeanCloud都会给一个唯一的id。

<img src="https://pic4.zhimg.com/80/v2-700a6408a1ec08e95d4b1a8571a8dd95_hd.jpg" width="70%">

**关键词**：vue.js、vue-router.js、keep-alive、组件通信、LeanCloud

**描述**：Vue简历编辑器，通过Vue-router实现路由切换功能，使用兄弟组件通信及keep-alive进行数据的传递和渲染，使用leanCloud完成用户的登录、注册、登出的功能，使用v-model实现了数据的编辑和保存双向绑定，通过查寻url的id来区分用户登录和分享的状态，通过媒体查询切换print的CSS样式。

**源码链接**：[点这里](https://github.com/yuyunzhi/Vue-resume-2018-06)
**预览链接**：[点这里](https://yuyunzhi.github.io/Vue-resume-2018-06/src/index.html)

# 一、预览、编辑切换逻辑

## 1、区分预览和编辑页面的url

- 预览页面的url带有该用户唯一的id查询参数
- 编辑页面的url不带用户id查询参数

## 2、当用户打开一个url进行判断（注意顺序）

- 该页面是否是预览页面(url是否包含id查询参数)
- 存在则为预览状态mode==="preview"，并根据该id从数据库获取resume的信息，保存到data里的previewResume
- 不存在则为编辑状态，再判断用户是否登录了。如果登录那么根据登录的用户id从数据库获取resume的信息，保存到data里的resume；如果没有登录就不获取信息，渲染原始数据

## 3、当为预览状态（不能编辑）

- 把不相关的button、div……隐藏
- 增加'退出预览'按钮
- 点击该按钮切换编辑状态，显示之前隐藏的button、div……以及隐藏退出按钮。同时退出登录状态

## 4、用computed计算值来切换渲染的页面

- 当mode==="preview"，使用previewResume的数据
- 当mode==="edit"，使用resume的数据

# 二、编辑状态


<img src="https://pic3.zhimg.com/80/v2-3d1c3b6591c0f4f170b6e8fef575dcb1_hd.jpg" width="70%">

## 1、使用组件，设置可编辑，并双向绑定

html

```
<editable-span v-bind:disabled="mode==='preview'" 
   v-bind:value="displayResume.name" v-on:edit="onEdit('name',$event)">
</editable-span>
```

vue.js
```
Vue.component('editable-span', {
    props: ['value','disabled'],
    template: `
        <span class="editableSpan">
            <span v-show="editing">{{value}}</span>
            <input v-show="!editing" type="text" v-bind:value="value" v-on:input="x">
            <button v-if="!disabled"v-on:click="editing=!editing">edit</button>
        </span>
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

## 2、新增项目

html

```
 <li v-if="mode==='edit'">
    <span @click="addSkill">添加skill</span>
 </li>
```

vue.js
```
addSkill(){
   this.resume.skills.push({name:'xxx',description:'yyy'})
}
```

## 3、删除项目

html
```
<span class="removeSkill" v-if="mode==='edit'" 
    @click="removeSkill(index)" v-cloak >
删除</span>
```

vue.js
```
removeSkill(index){
 this.resume.skills.splice(index,1)
}
```

# 三、预览状态

<img src="https://pic2.zhimg.com/80/v2-6acc07c76871cd5f45367e3fbf0fea29_hd.jpg" width="70%">

## 1、获取预览（分享）的链接：

```
app.shareLink=this.getShareLink(currentUser)
getShareLink(){
   let currentUser = AV.User.current()
   if(currentUser){
      return location.origin+location.pathname+'?user_id='+currentUser.id      
   }else{
      return '' 
}  
```

## 2、当用户打开链接，判断是否是预览链接:

- 获取查询参数
- 用正则表达式获取id
- 如果可以获取说明是预览链接……
- 如果不可以获取，那么是编辑状态……

```
app.initSignOutButton()
initSignOutButton(){

   //获取预览用户的 id
   let search = location.search
   let regex = /user_id=([^&]+)/
   let matches = search.match(regex)
   let userId
   if (matches){
   //预览页面
      userId = matches[1]
      app.mode = 'preview'
      this.getResume({id: userId}).then(user => {
          this.previewResume = user.attributes.resume      
        })
    }else{
   //用户登录页面
          let currentUser = AV.User.current()
          if(currentUser==null||currentUser==undefined){
              this.signOutVisible=false
              this.boundary.userName='未登录'
              return
          }else{
              this.signOutVisible=true
              this.boundary.userName=currentUser.attributes.username
              app.shareLink=this.getShareLink(currentUser)
              this.getResume(currentUser).then((user)=>{
                  this.resume = user.attributes.resume  
            })
        }
    }

}
```

## 3、编辑预览模式切换

把之前双向绑定的resume.xxx改为displayRresume.xxx

```
displayResume(){
     return this.mode === 'preview' ? this.previewResume : this.resume
}
```

## 4、把预览链接不需要显示的元素内容隐藏

```
v-if="mode==='edit'"
```