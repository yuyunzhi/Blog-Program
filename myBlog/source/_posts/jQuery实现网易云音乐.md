---
title: jQuery实现网易云音乐
date: 2018-09-04 12:00:24
categories: [编程作品集]
tags: [JavaScript,jQuery,响应式布局 ,MVC,eventHub（发布/订阅模式）,七牛,LeanCloud,Swiper,移动端]
---

一直都想做一个音乐播放器，这次总算做了一个。

<img src="https://i.loli.net/2018/06/30/5b3795e25ca3f.jpg" width="70%">

**关键词**：JavaScript、jQuery、响应式布局 、MVC、eventHub（发布/订阅模式）、七牛&LeanCloud数据库、Swiper、移动端

**描述**：移动端播放歌曲、切换、暂停、搜索等功能，PC 端歌曲上传、删除、修改等功能。使用 jQuery、MVC，以及 LeanCloud、七牛等作为数据库实现。使用vConsole进行调试。

**源码链接**：[点这里](https://www.yuyunzhi.com/music-2018-06/)
**预览链接**：[点这里](https://yuyunzhi.github.io/music-2018-06/src/index.html)

这里整理一下页面分析和解决bug的通用方法:

# 一、页面分析

## 1、网易云音乐logo+下载APP

logo用SVG做，a标签做跳转

## 2、推荐音乐、热歌榜、搜索三个Tab键跳转

- 用ol>li>span标签包裹：推荐音乐、热歌榜、搜索
- 通过onclick事件点击任意一个Tab，将其余两个Tab页面隐藏，并用::after来显示红色下划线
- 对span使用inline-block，让红色下划线能够动态获取父元素的宽度


## 3、推荐音乐页面-轮播

使用Swiper官网组件，引入min.css和min.js，自定修改样式

## 4、推荐音乐页面-推荐歌单


- 使用ol>li*6>a>img+p标签插入跳转、图片、文字
- 确定每个li元素的宽度和间隙
- 使用flex布局:flex-wrap:wrap、flex-direction:row、justify-content:space-between
- a标签的href=“跳转页面的地址+查询参数（？id=xxxxxx）”


## 5、推荐音乐页面-最新音乐

音乐歌曲、图片上传到七牛，地址、歌名、歌描述、歌词、歌的id保存到LeanCloud，然后从数据库批量获取所存音乐，并把获取的数据通过evenHub通知其他所需要数据的页面。

```
find(){
    let query = new AV.Query('Song');
    return query.find().then((songs)=>{
       this.data.songs=songs.map((song)=>{
           let {background,cover,descript,hotSong,lyric,name,url}=song.attributes
           let id=song.id                  
           return {background,cover,descript,hotSong,lyric,name,url,id}
        })
       window.eventHub.emit("songs",this.data.songs)
       return songs
      });
 }
 ```

 对获取音乐的数组进行map()动态生成音乐清单，播放图标使用iconfont，smybol引用:

 ```
 renderSongList(data){
    let songs=data
    songs.map((song)=>{
       let name = song.attributes.name
       let descript =song.attributes.descript
       let url =song.attributes.url
       let id = song.id
       let $li = $(`
        - 
               ## ${name}
               <div class='sq'></div>
               ${descript}
               <a href="./song.html?id=${id}">
                   <svg class="icon" aria-hidden="true">
                      <use xlink:href="#icon-play1"></use>
                    </svg>
                </a>
                       
        `)
       $(this.el).find('#lastestMusic').append($li)
     })
   },
```

<img src="https://i.loli.net/2018/06/30/5b37970042ba9.jpg" width="70%">

## 6、推荐音乐页面-底部logo

logo用SVG做，a标签做跳转

## 7、热歌榜-云音乐背景

<img src="https://i.loli.net/2018/06/30/5b37973145ec8.jpg" width="70%">


- 其中白色的字的图片是一个雪碧图
- 外面再套一个特定宽高的div使用overflow:hidden
- 对雪碧图进行移动


## 8、热歌榜-音乐清单

<img src="https://i.loli.net/2018/06/30/5b37977ae6976.jpg" width="70%">

同5从数据库动态获取所有的音乐，然后把含有hotSong的音乐filter()出来，动态插入页面

```
window.eventHub.on("songs",(songs)=>{
    let newSongs=songs.filter((song)=>{
    return song.hotSong
})
```

1、2、3数字高亮

```
activeNumber(){
    let $songsNumber=$('.hotMusicList').find('.songNumber')
    for(let i=0;i<=2;i++){
       $songsNumber.eq(i).addClass('active')
    }
 }
 ```

 1、2、3变成01、02、03

 ```
 makeNumber(n){
    if(n<10){
         n=`0${n}`
      }
    return n
}
```

## 9、搜索-搜索框

<img src="https://i.loli.net/2018/06/30/5b3797c16d2e5.jpg" width="70%">

搜索框html代码如下

```
<div class="searchContainer">
    <img class="searchIcon"src="xxx" alt="">
    <input type="search" placeholder="搜索歌曲、歌手" >
    <img class="deleteIcon"src="xxx" alt="">
</div>
```

对输入框的value.length进行监听，searchIcon默认显示，当用户输入内容时deleteIcon显示。

```
changeDelete(value){
    if(value.length===0){
         $('.deleteIcon').removeClass('active')
    }else{
         $('.deleteIcon').addClass('active')
     }
 }
 ```

 当用户点击删除按钮，则令value=“”，并显示热门搜索
 <img src="https://i.loli.net/2018/06/30/5b37981767631.jpg" width="70%">

 ## 10、搜索-从数据库动态显示用户输入的音乐


- 每400毫秒获取一次用户输入的value值
- 把value值传进一个函数，该函数从数据库获取所有音乐的信息，并找出包含value值得音乐，并返回数组

```
 search(keyword){
    return new Promise((resolve,reject)=>{
        let database=this.model.data
        let result = database.filter(function(item){
             return item.name.indexOf(keyword)>=0
        })
             resolve(result)
      })
}
```

获取该数组的音乐的信息，进行页面的渲染

```
renderSongList(result){
    $('.searchSongList>a').remove()
    result.map((item)=>{
        $aTag=$(`
           <a href="./song.html?id=${item.id}">
               <img src="./img/search.png" alt="">
               ${item.name}                            
           </a>`)
        $('.searchSongList').append($aTag)
     })
 },
 renderNoSong(){
    $(this.el).find('.searchSongList>a>p').text('sorry,该歌曲暂未收入')
 }
 ```

整段代码如下：

 ```
 let timer
 $(this.view.el).find('input[type="search"]').on('input',(e)=>{
    let $input=$(e.currentTarget)
    let value = $input.val().trim()
    if(timer){
        clearTimeout(timer)
     }
     timer = setTimeout(()=>{
        if(value.length===0){
             this.view.changeDelete(value)
             this.view.isHotSong()
         }else{
             this.view.changeDelete(value)
             this.view.isSearch()
             this.view.renderSearchText(value)  
             this.search(value).then((result)=>{
                 let songNumber = result.length
                 if(songNumber==0){
                     this.view.renderNoSong()               
                 }else{
                     this.view.renderSongList(result)     
                 }
              })
         }
     },400)
 })
 ```

 ## 11、播放音乐页面-光盘旋转
 <img src="https://i.loli.net/2018/06/30/5b3798893186b.jpg" width="70%">

- 三张图片进行旋转
- 根据播放和暂停来激活active 旋转

 ```
 @keyframes circle{
    0%{
 transform: rotate(0);
    }
    100%{
 transform: rotate(360deg);
    }
}
.disc-container>.disc.active{
    animation: circle 20s linear infinite;
}
```

## 12、播放音乐页面-音乐播放与暂停

对播放与暂停的icon进行onclick监听，并通过evenHub通知光盘模块进行切换旋转状态，以及icon切换

```
$('.icon-play').on('click',function(){
    window.eventHub.emit('stop')
    let audio = $(view.el).find('audio')[0]
    audio.play()
})
$('.icon-pause').on('click',function(){
    window.eventHub.emit('play')
    let audio = $(view.el).find('audio')[0]
    audio.pause();
})    
```

## 13、播放音乐页面-歌词同步
**原理**：lyric是对应了时间time1和歌词的，然后我们获取当前time2=audio.currentTime,进行时间的对比，然后显示某段歌词，再移动和高亮。

获取歌词

```
getContentAndRender(){
    window.eventHub.on('songInformaition',(data)=>{
       let lyric=data.lyric //获取歌词
       this.model.initLyric(lyric) //歌词处理
       this.view.render(data.descript,this.model.array) //歌词渲染页面           
    })      
}
```

歌词处理

```
initLyric(lyric){
    let array = lyric.split('\\n')             
    let regex = /^\[(.+)\](.*)$/ 
    array = array.map(function(string,index){
          let matches = string.match(regex)
          if(matches){
              return {
                time:matches[1],
                words:matches[2]
               }
          }
     }) 
 this.array=array            
}
```

歌词渲染页面

```
render(descript,array){
    $(this.el).find('h2').text(descript)
    let $line = $('.lyric>.line')
    array.map(function(object){
        let $p = $(`${object.words}`)
        $p.attr('data-time',object.time)
        $line.append($p)
    })
}
```

歌词移动

```
moveLyric(){
    window.eventHub.on('seconds',(time)=>{
    let $lines = $(this.view.el).find('.line>p')
    let $thisLine
    for(let i=0;i<$lines.length;i++){
    let currentLineTime = $lines.eq(i).attr('data-time')
    let nextLineTime = $lines.eq(i+1).attr('data-time')
        if( $lines.eq(i+1).length !== 0 && currentLineTime < time && nextLineTime > time ){
           //显示第i行
           $thisLine = $lines.eq(i)
        }
    }
     if($thisLine){
        $thisLine.addClass('active').prev().removeClass('active')
        let top = $thisLine.offset().top
        let lineTop = $('.line').offset().top
        let delta = top -lineTop-$('.lyric').height()/3
        $('.line').css('top',`-${delta}px`)                  
    }
   })
}
```

# 二、bug解决方案

## 1、遇到报错


- 查StackOverFlow
- 查GOOGLE
- 查知乎
- 查组件官网


## 2、没有报错，但不出结果


- JS：console.log('xxx')、console.log(变量)
- CSS：border:1px solid red


## 3、手机端没有控制台

- 把console.log(),改为alert()
- [使用vConsole](https://link.zhihu.com/?target=https%3A//github.com/yuyunzhi/vConsole)
- 使用onerror事件


```
<script>
    window.onerror=function(message,file,row){
      alert(message)
      alert(file)
      alert(row)
  }
</script>
```
