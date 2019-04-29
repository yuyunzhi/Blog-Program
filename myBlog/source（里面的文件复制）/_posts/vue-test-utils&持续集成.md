---
title: vue-test-utils&持续集成
date: 2018-10-15 15:56:09
categories: [Vue]
tags: [Vue,单元测试,持续集成,TDD]
---


## 一、测试有哪些分类

### 1、黑白测试

- 根据代码的思路写测试叫白盒测试
- 不知道代码的思路测试叫黑盒测试（大部分人工测试都是这个）

### 2、规模（小->大）

- 单元测试（最小的测试是函数，也是白盒测试）
- 模块测试（根据API测试）
- 功能测试
- 集成测试（多个系统端对端测试E2E，也是黑盒测试）

## 二、单元测试的必要性

- 你不一定一辈子当前端
- 开发库（公司里的库必须要有），没有单元测试公司很难有优质代码的积累
- 测试驱动开发

## 三、前端业务代码是否需要测试

- 不需要
- 业务代码中的共用代码才需要测试：比如表单验证（抽离出组件，测组件）
- 然后做高质量的组件，输出给团队用


## 四、什么才算是好的单元测试

- 测试覆盖率>90%，基本上都是好的测试
- 测试内容：语句、分支、函数、行

## 五、测试工具介绍

- 这里使用Karma + Mocha做单元测试，TravisCI做持续集成
- Karma（[ˈkɑrmə] 卡玛）是一个测试运行器，它可以呼起浏览器，加载测试脚本，然后运行测试用例
- Mocha（[ˈmoʊkə] 摩卡）是一个单元测试框架/库，它可以用来写测试用例
- Sinon（西农）是一个 spy / stub / mock 库，用以辅助测试
- karma-coverage 来设置 Karma 的代码覆盖率
- chaijs 提供了dscribe it断言

除此之外了解两个词语：
- BDD 行为驱动开发  Behavior Driven Development
- TDD 测试驱动开发 Test Driven Development

## 五、安装工具及配置

### 1、创建karma 配置

- 安装依赖

```
npm add -D karma karma-chrome-launcher karma-mocha karma-sourcemap-loader karma-spec-reporter karma-webpack chai sinon sinon-chai
```

**安装成功：**
![安装成功](http://pj71rnyh2.bkt.clouddn.com/1543891661%281%29.png)

- 创建 karma.config.js

```

//frameworks引入'mocha', 就会拥有descibe it函数，frameworks引入'sinon-chai'，就会有sinon.fake()，expect函数
//files 检索 tests/**/*.spec.js文件
//browsers 使用的是ChromeHeadless,无头浏览器

var webpackConfig = require('@vue/cli-service/webpack.config.js')

module.exports = function (config) {
  config.set({
    frameworks: ['mocha'],

    files: [
      'tests/**/*.spec.js'
    ],

    preprocessors: {
      '**/*.spec.js': ['webpack', 'sourcemap']
    },

    webpack: webpackConfig,

    reporters: ['spec'],
    autoWatch: true,

    browsers: ['ChromeHeadless']
  })
}
```

- 修改package.json 中scripts

```
  "scripts": {
      ...
    "test": "karma start --single-run",
    "test:unit": "karma start",
    "test:unit:old": "vue-cli-service test:unit",//vue原有提供的代码
    ...
  },

```

### 2、创建单元测试覆盖率

- 安装依赖

**vue-test-utils**:[vue-test-utils官网配置覆盖率](https://vue-test-utils.vuejs.org/zh/guides/#%E7%94%A8-karma-%E6%B5%8B%E8%AF%95%E5%8D%95%E6%96%87%E4%BB%B6%E7%BB%84%E4%BB%B6)


```
npm install --save-dev karma-coverage cross-env
npm install --save-dev babel-plugin-istanbul

```

- 更新.babelrc 文件

```
{
  ……
  env: {
    test: {
      "plugins": ["istanbul"]
    }
  }
  ……
}
```
- 更新 karma.conf.js文件

```
module.exports = function (config) {
  config.set({
  // ...

    reporters: ['spec', 'coverage'],

    coverageReporter: {
      dir: './coverage',
      reporters: [
        { type: 'lcov', subdir: '.' },
        { type: 'text-summary' }
      ]
    }
  })
}
```

- 更新test脚本

```
// 加上cross-env BABEL_ENV=test
{
  "scripts": {
    "test": "cross-env BABEL_ENV=test karma start --single-run"
  }
}
```

## 六、如何写单元测试

- 在vue脚手架中，不同的单元测试写在tests/unit/**.spec.js

- vue-test-utils提供的API：[官网教你怎么写代码](https://vue-test-utils.vuejs.org/zh/api/)

- 举例button组件

```vue/test-utils
//引入chai，所以有dscribe it expect 函数
import chai, {expect} from 'chai'

//sinon（西农）是一个 spy / stub / mock 库，用以辅助测试
import sinon from 'sinon'
import sinonChai from 'sinon-chai'

//vue-test-utils 提供的工具
import {shallowMount, mount} from '@vue/test-utils'

//引入要测试的组件
import Button from '../../src/button/button'

chai.use(sinonChai)


describe('Button.vue', () => {
    it('存在.', () => {
        expect(Button).to.exist
    })
    it('可以设置icon.', () => {
        const wrapper = mount(Button, {
            propsData: {
                icon: 'settings'
            }
        })
        const useElement = wrapper.find('use')
        expect(useElement.attributes()['href']).to.equal('#i-settings')
    })
    it('可以设置loading.', () => {
        const wrapper = mount(Button, {
            propsData: {
                icon: 'settings',
                loading: true
            }
        })
        const vm = wrapper.vm
        const useElements = vm.$el.querySelectorAll('use')
        expect(useElements.length).to.equal(1)
        expect(useElements[0].getAttribute('xlink:href')).to.equal('#i-loading')
    })

    it('icon 默认的 order 是 1', () => {
        const wrapper = mount(Button, {
            attachToDocument: true,
            propsData: {
                icon: 'settings',
            }
        })
        const vm = wrapper.vm
        const icon = vm.$el.querySelector('svg')
        expect(getComputedStyle(icon).order).to.eq('1')
    })

    it('设置 iconPosition 可以改变 order', () => {
        const wrapper = mount(Button, {
            attachToDocument: true,
            propsData: {
                icon: 'settings',
                iconPosition: 'right'
            }
        })
        const vm = wrapper.vm
        const icon = vm.$el.querySelector('svg')
        expect(getComputedStyle(icon).order).to.eq('2')
    })
    it('点击 button 触发 click 事件', () => {
        const wrapper = mount(Button, {
            propsData: {
                icon: 'settings',
            }
        })
        const vm = wrapper.vm
        const callback = sinon.fake();
        vm.$on('click', callback)
        vm.$el.click()
        expect(callback).to.have.been.called

    })
})
```

- 运行

```
npm run test:unit
```

**运行成功：单元测试及测试覆盖率**

![单元测试及测试覆盖率](http://pj71rnyh2.bkt.clouddn.com/1543895376%281%29.png)



## 七、TravisCI 持续集成

- 每次都需要输入npm run test:unit来测试，如果用TravisCI写好测试用例文件配置相关配置就会自动化测试。
- 阮一峰文章：http://www.ruanyifeng.com/blog/2017/12/travis_ci_tutorial.html
- 在根目录下配置.travis.yml文件

```
//nodejs环境，版本为8，选用稳定版本的chrome测试
language: node_js
node_js:
  - "8"
addons:
    chrome: stable
sudo: required
before_script:
  - "sudo chown root /opt/google/chrome/chrome-sandbox"
  - "sudo chmod 4755 /opt/google/chrome/chrome-sandbox"
```

- 使用github登录官网：https://travis-ci.org/
- 选中测试项目，且测试项目需要有测试脚本
- 每次github提交代码都会进行测试

![集成图片](http://pj71rnyh2.bkt.clouddn.com/1543895159%281%29.png)