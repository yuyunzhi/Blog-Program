---
title: Git入门
date: 2018-09-04 11:29:34
categories: [Git]
tags: [Git,GitHub,命令行]
---

![git](https://pic1.zhimg.com/v2-20924e96a258c5eb8804922925490cbf_1200x500.jpg)

# 一、安装git

直接官网下载，持续按下一步即可。(这里就不重复啰嗦了)

# 二、配置git

## 1、git bash打开后输入一下命令，否则Git无法使用

```
git config --global user.name xxx
git config --global user.email yyy
git config --global push.default simple
git config --global core.quotepath false
git config --global core.editor "vim"
```

## 2、输入命令生成秘钥：

```
ssh-keygen -t rsa -b 4096 -C "xxxxx@xxxxx.com" //邮箱更换成自己的
然后直接三个回车会生成id_rsa 和id_rsa.pub文件
cat ~/.ssh/id_rsa.pub   //输入命令打开id_rsa.pub
复制内容，在GitHub里新建SSH文件保存即
```

## 3、配置小插件（命令使用与查询）：

```
输入命令安装：npm install -g tldr
```

附上使用地址:[命令行查询](https://github.com/tldr-pages/tldr#tldr)

# 三、使用git仓库的方法

## 1、本地建立仓库

- 在D盘建立一个名为xxx的文件夹；
- 右击git bash打开;
- 输入命令git init回车，初始化仓库自动生成.git文件;
- 然后用户进行自定义创建，修改等步骤;
- 随后输入命令git add 文件名，把文件暂存;
- 输入命令git commit -m "修改解释"。
- 完成

## 2、将本地仓库上传到github上

* 在GitHub建立新的仓库，命名为xxx，与本地仓库名一致；
* 本地仓库修改完成了def的步骤后，依次在gitbash输入一下命令，直接复制最好；如果你有本地仓库，就直接输入第四行和第五行命令就行；

```
echo "abc" >> README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin git@github.com:yuyunzhi/xxx.git   备注xxx是仓库名
git push -u origin master
```

## 3、从github上下载仓库

- 进入一个仓库，复制ssh地址
- 建立一个文件夹用Git Bash打开，输入命令git clone "ssh地址"

## 4、vscode修改上传到github

本地仓库完成初始化后生成了.git 文件，把整个文件夹用vscode，在vscode中新增修改代码，分支上传、推送，刷新github仓库页面。

## 5、github page预览html

- 进入一个仓库，点击settings
- 往下滚动页面，选中master branch 点save
- 出现一个链接，在末尾加入仓库html文件名，在地址框输入即可
这样以后上传了demo，就可以在描述上添加预览链接。

# 四、更改本地仓内容，上传至GitHub

你在本地目录有任何变动，只需按照以下顺序就能上传：

```
git add 文件路径
git commit -m "信息"
git pull （相信我，你一定会忘记这一个命令）
git push
```

下面是例子：
```
cd git-demo-1
touch index2.html
git add index2.html
git commit -m "新建 index2.html"
git pull
git push
```