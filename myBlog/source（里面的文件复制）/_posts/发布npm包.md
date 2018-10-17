---
title: 发布npm包
date: 2018-10-16 17:06:44
categories: [npm]
tags: [npm]
---

# 一、发布前

- 确保自己的代码已经测试通过才发布

# 二、上传代码至npmjs.org

- 注意每次上传时要更新 package.json 版本
- 在根目录下创建index.js,在index.js里将你想要导出的内容全部导出
- 如：

```
import Button from './src/button'
import ButtonGroup from './src/button-group'
import Icon from './src/icon'
export {Button,ButtonGroup,Icon}

由于node目前不支持import 所以需要进行转义，在上传前对自己的代码输入以下命令行
npx parcel build index.js --no-minify
将 package.json 的 main 改为 dist/index.js
```

- 注册一个账户:https://www.npmjs.com/
- 确认一下邮箱（必须）
- 根目录运行 npm adduser，如果错误提示里面含有 https://registry.npm.taobao.org 则说明你的 npm 源目前为淘宝源，需要更换为 npm 官方源
- 运行 npm publish 同时需要输入账号和密码

# 三、下载使用包

- 下载包 npm i xxx
- 更新包 npm update xxx

如果是为了本地调试可以在项目目录使用 npm link，然后在使用之处运行 npm link xxx，就是最新了

