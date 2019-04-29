---
title: Vue虚拟DOM
date: 2018-11-21 12:49:10
categories: [Vue]
tags: [Vue]
---

## 一、为什么要使用虚拟DOM

- 首先说说为什么要使用Virturl DOM，因为操作真实DOM的耗费的性能代价太高，所以Vue内部使用js实现了一套dom结构，在每次操作在和真实dom之前，使用实现好的diff算法，对虚拟dom进行比较，递归找出有变化的dom节点，然后对其进行更新操作。
- 为了实现虚拟DOM，我们需要把每一种节点类型抽象成对象，每一种节点类型有自己的属性，也就是prop，每次进行diff的时候，会先比较该节点类型，假如节点类型不一样，那么Vue会直接删除该节点，然后直接创建新的节点插入到其中，假如节点类型一样，那么会比较prop是否有更新，假如有prop不一样，那么Vue会判定该节点有更新，那么重渲染该节点，然后在对其子节点进行比较，一层一层往下，直到没有子节点。

## 二、使用虚拟DOM

### 1、代码示例

```
<template>
    <div class="container">
        <y-div  @click="outerFny" level="1"></y-div>
    </div>
</template>

<script>
    export default {
        components: {
            'y-div': {
                data() {
                    return {
                        value: '123'
                    }
                },
                props: ['level'],
                render: function (createElement) {
                    return createElement(
                        'div',
                        {
                            'class': {
                                foo: true,
                                bar: false
                            },
                            style:{
                                width:'100px',
                                height:'100px',
                            }
                        },
                        [
                            createElement('p',{
                                on: {
                                    click: this.handler
                                },
                                'class': {
                                    foo: true,
                                    bar: false
                                },
                                style: {
                                    fontSize: '14px',
                                },
                            },'nnnnnnnnn'),
                            createElement('p','wwwwwww')
                        ]
                    )
                },
                methods: {
                    handler() {
                        console.log('value', this.value);//123
                        this.$emit('click', {a: '1', b: '2'})
                    }
                }
            },

        },
        methods: {
            outerFny(e) {
                console.log(e);// {a: '1', b: '2'}
            }
        }
    }
</script>
```

### 2、render函数的一个参数

- 第一个参数必选， string|Object|Function

```
Vue.component('child', {
//String--html标签
//Object---一个含有数据选项的对象
//FUnction---方法返回含有数据选项的对象
    render: function (createElement) {
        alert(typeof createElement)
// return createElement('h1')
// return createElement({
// template:'<div>锄禾日当午</div>'
// })
        var domFun = function () {
            return {
                template: '<div>锄禾日当午</div>'
            }
        }
        return createElement(domFun());
    }
});
```

### 3、render函数的第二个参数

- 第二个参数可选,第二个参数是数据对象----只能是Object
```
Vue.component('child', {
    render: function (createElement) {
        return createElement({
            template: '<div>我是龙的传人</div>'
        }, {
            'class': {
                foo: true,
                baz: false
            },
            style: {
                color: 'red',
                fontSize: '16px'
            },
//正常的html特性
            attrs: {
                id: 'foo',
                src: 'http://baidu.com'
            },
//用来写原生的Dom属性
            domProps: {
                innerHTML: '<span style="color:blue;font-size: 1
                8px">我是蓝色</span>'
            }
        })
    }
});
```

### 4、render函数的第三个参数
- 第三个参数也是可选===String | Array—作为我们构建函数的子节点来使用的

```
Vue.component('child',{
// ----第三个参数是可选的，可以是 String | Array---代表子节点
    render: function (createElement) {
        return createElement('div',[
            createElement('h1','我是h1标题'),
            createElement('h6','我是h6标题')
        ])
    }
});
```

### 5、this.$slots在render函数中的应用

```
<my-component>
    <div slot="header">1</div>
    <div slot="default">2</div>
    <div slot="footer">3</div>
</my-component>

Vue.component('my-component',{
    render:function (createElement) {
        var header = this.$slots.header; //--这返回的内容就是含有=VNODE的数组
        var main = this.$slots.default;
        var footer = this.$slots.footer;
        return createElement('div',[
            createElement('header',header),
            createElement('main',main),
            createElement('footer',footer)
        ]);
    }
})
```