---
title: Vue实现业务功能
date: 2018-10-15 15:32:53
categories: [Vue]
tags: Vue]
---

# 一、实现逻辑功能

## 1、导航显示二级菜单

- 鼠标进入显示
- 鼠标移开隐藏
- 多个菜单使用同一个函数

```
HTML
---------------
<div @mouseover="showSubmenu('show','Language')" 
     @mouseout="showSubmenu('hide','Language')" >
    <span>Language</span>
    <ul class="submenu" v-show="showLanguage">
        <li><a class="language" href="#">中文</a></li>
        <li><a class="language" href="#">English</a></li>
        <li><a class="language" href="#">Espanol</a></li>
    </ul>
</div>
```

- 使用鼠标onmouseover及mouseout来监听事件,传入两个参数x,y
- x表示show或hide,y表示触发状态的元素

```
JS
---------------
showSubmenu(status,item){
    if(item == 'Language'){
        status=='show'?this.showLanguage=true:this.showLanguage=false
    }
    if(item == 'Individual'){
        status=='show'?this.showIndividual=true:this.showIndividual=false
    }
    …………
}
```

## 2、搜索框

- 函数节流，输入过程中显示相关的搜索清单

```
if(this.timer){ 
    clearTimeout(this.timer)
}
this.timer = setTimeout((
    //发送请求
    //对请求后的数据处理
)=>{},1000)
```

- 输入不同的类型内容跳转不同页面，同时传id

```
输入过程中，结合函数节流从后端获取输入的内容的类型，根据类型判断跳转不同的页面
JS
---------------
gotoNewPage(list){
    if(list.type=="city"){
        //进入city页面  传参为list.cid
        this.$router.push({name:'ranking',params:{
            id:list.cid
            }
        })
    }else if(list.type=="scenic"){
        //进入维基百科页面
        let url = `${Url.wiki}${list.placeName}`
        window.open(url)
    }
    this.showList=false
},
```

- 鼠标点击外部，input显示的清单消失

```
在APP.vue文件监听click事件，点击判断是否为input相关的元素/元素class
然后触发$emit事件，在Search组件mounted()使用$on监听

APP.vue JS
---------------
changeLists(e){
    let targetName=e.target.className
        if(!(targetName === "input" ||  targetName === "list")){
            this.$bus.$emit('changeList')         
        }
}


Serarch.vue JS
---------------
this.$bus.$on('changeList',()=>{
    this.showList=false
})
```

- 搜索报错.catch处理页面逻辑

```
.catch((error)=>{
        console.log(error.response)
})
//注意，一定要.response才能取到报错的数据
```

## 3、焦点状态

- 当鼠标点击/浮过某个元素，显示某种状态

```
声明一个变量 topIndex
传入index,并在函数内赋值给topIndex
判断其是否相等,添加class激活状态

<li :class="{'active':topIndex==index}" 
v-for="(item,index) in provinceNames" v-bind:key="index"
@mouseover="updateCityList(index,item)"
>
{{xxx}}
</li>
```

## 4、页面状态保存

- 存的到localStorage、sessionStorage里
- 页面加载是从里面获取渲染页面

## 5、不准确路由跳转404

- 404页面当然是自己准备了
- 在APP.vue的created里判断路由的name是否存在

```
  if(this.$route.name===null){
    this.$router.push({name:'errorPage'})
  }
```

- 当然也可以把这个函数写在Vue.protoype里

## 6、路由守卫

- 引用场景，判断某个组件，某个页面用户是否登录来决定是否显示该页面或跳转到登录页面
- 根据后端传来的登录状态储存到sessionStorage/localStorage

```
登录成功：true
登录失败：false
储存到sessionStorage:window.sessionStorage.setItem('isLogin',true)
```

- 页面初始化或点击某个组件进行状态判断

```
Vue.prototype.$initPageLogin=function(){
  let isLogin=window.sessionStorage.getItem('isLogin');
  if(isLogin === false || isLogin === null){
      this.$router.push({name:'onepage'})
  }
}
```
  
## 7、打包-懒加载

- 对引入的组件更改为按需加载,会使得打包体积大大降低
- 原来是这样的：

```
import xxx from './yyy.vue'
```

- 懒加载引入

```
const xxx = ()=>import ('./yyy.vue')
```

## 8、window.open突破拦截

- 产生原因：非用户点击直接开启新窗口，在异步请求后打开新窗口被拦截
- 解决方案

1、先打开空白新窗口，异步请求后重定向新窗口
```
var xxx=window.open('',_blank)
xxx.location.href=url
```

2、把异步请求变成同步请求

-- 异步换成同步写法(本质还是异步，所以不行)

```
async function r(){
    try{
        let content1 = await read('1.txt','utf8');
        let content2 = await read(content1,'utf8');
        return content2;
    }catch(e){ 
        console.log('err',e)
    }
}
```

-- 用同步的方式发请求(这里使用了jquery ajax)

```
let data
$.ajax({
    url:xxx,
    async:false， //表示是否异步
    success(mes){
        data=mes
    }
})
//后面可以使用data
然后window.open打开新窗口
```

# 二、CSS样式

## 1、隐藏滚动条

```
.xxx{
    //限定最大高度如max-height:100px;    
    white-space: nowrap;
    -webkit-overflow-scrolling: touch;
    overflow-x: auto;
    overflow-y: scroll;       
    margin-bottom: -.2rem;
    overflow: -moz-scrollbars-none;
    overflow: -moz-scrollbars-none;
    &::-webkit-scrollbar{
        display: none;
    }   
}
```

## 2、显示固定行数

```
.xxx{
    //设置高度
  display: -webkit-box;    
  -webkit-box-orient: vertical;    
  -webkit-line-clamp: 5;    
  overflow: hidden;
}

```

## 3、宽

- 在布局的时候，最外的div设置固定的px
- 内部的div设置百分比
- 实现响应式的时候，用媒体查询修改最外div的宽度就可以了


## 4、解决滚动条抖屏

- 产生原因：不同页面的所用的高度不一样那么超出浏览器高度会显示滚动条，出现样式抖动
- 解决方案，全局样式加上以下代码(body absoulte)

```
html {
    overflow-y: scroll;
}
:root {
    overflow-y: auto;
    overflow-x: hidden;
}
:root body {
    position: absolute;
}
body {
    width: 100vw;
    overflow: hidden;
}
```

# 三、前端库的使用


## 1、Swiper

- 官网，选择样式：https://www.swiper.com.cn/
- 引入cdnjs

```
index.html
-------------------
<script src="https://cdnjs.cloudflare.com/ajax/libs/Swiper/4.4.1/js/swiper.min.js"></script>
```

- 引入css

```
@import "../assets/css/swiper.min.css";
```

- 引入html
- 引入js

注意，如果异步获取的数据赋值后不更新，那么在swiper的配置里加上：  

```      
observer:true,
observeParents:true,
```



