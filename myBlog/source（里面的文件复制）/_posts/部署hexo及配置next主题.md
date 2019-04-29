---
title: 部署hexo及配置next主题
date: 2018-09-04 21:52:09
categories: [Hexo]
tags: [Hexo]
---



这个部署分为三个部分：

- 配置本地电脑与github的SSH(这里就不说了)
- 安装hexo，安装next主题
- 替换部分配置文件


## 一、安装

### 1、安装hexo

- 进入一个安全的目录，比如 cd ~/Desktop 或者 cd ~/Documents，别在根目录 / 瞎搞。以后所有的教程第一步都是「进入一个安全的目录，别在根目录瞎搞」，只有 ~ 里面的目录是你能碰的！ **重新部署可省略该步骤**

- 在 GitHub 上新建一个空 repo，repo 名称是「你的用户名.github.io」（注意个用户名是你的GitHub用户名，不是你的电脑用户名）

- `npm install -g hexo-cli`，安装 Hexo

- `hexo init myBlog`

- `cd myBlog`

- `npm i`

- `hexo new 开博大吉`，你会看到一个 md 文件的路径 **重新部署可省略该步骤**

- `start xxxxx.md`,编辑这个 md 文件，内容自己想（Ubuntu 系统用 xdg-open xxxxx.md 命令） **重新部署可省略该步骤**

- `start _config.yml`,编辑网站配置:  **重新部署可省略该步骤**
    - 把第 6 行的 title 改成你想要的名字;
    - 把第 9 行的 author 改成你的大名;
    - 把最后一行的 type 改成 type: git
    - 在最后一行后面新增一行，左边与 type 平齐，加上一行 repo: 仓库地址 （请将仓库地址改为「你的用户名.github.io」对应的仓库地址，仓库地址以 git@github.com: 开头你知道吧？不知道？不知道的话现在你知道了）
    - 第 4 步的 repo: 后面有个空格，不要眼瞎。

- `npm install hexo-deployer-git --save`,安装 git 部署插件

- `hexo deploy` **重新部署可省略该步骤**

- 进入「你的用户名.github.io」对应的 repo，打开 GitHub Pages 功能，如果已经打开了，就直接点击预览链接

- 你现在应该看到了你的博客！

### 2、安装next主题

- `https://github.com/hexojs/hexo/wiki/Themes` 上面有主题合集

- 随便找一个主题，进入主题的 GitHub 首页，比如我找的是 `https://github.com/iissnan/hexo-theme-next`

- 复制它的 SSH 地址或 HTTPS 地址，假设地址为 `git@github.com:iissnan/hexo-theme-next.git`

- `cd themes`

- `git clone git@github.com:iissnan/hexo-theme-next.git`

- `cd ..`

- `将 _config.yml 的第 75 行改为 theme: hexo-theme-next，保存`

- `hexo generate`

- `hexo deploy`

- `等一分钟，然后刷新你的博客页面，你会看到一个新的外观。如果不喜欢这个主题，就回到第 1 步，重选一个主题。`

## 二、替换文件

- `git clone 本仓库代码` [github地址点这里](https://github.com/yuyunzhi/Blog-Program)

- `进入myBlog目录准备替换文件`

- `node_modules`这个文件夹，直接替换，注意：是文件夹直接替换！！！

- 进入`source`文件夹，把里面的内容全部替换，注意：是进入source后然后替换所有的文件及文件夹！！！

- 进入`themes/hexo-theme-next`文件夹，把里面的内容全部替换，注意：是进入hexo-theme-next后然后替换所有的文件及文件夹！！！

- 在`myBlog`目录下，把剩余的文件都替换就行了.

- 完成上述内容，就可以直接在新的电脑部署hexo网站。

- 注意，如果配置少了功能，可以参考该篇文章[Hexo+Next主题优化](https://zhuanlan.zhihu.com/p/30836436)
