---
title: Git配置命令缩写
date: 2019-01-12 00:21:16
categories: [Git]
tags: [Git]
---

- 配置用户名之后git会在用户目录下面创建一个配置文件 .gitconfig

## 1、windows用户

- 一般在目录 $HOME 目录下, 注意, HOME是系统变量, 保存的是用户的目录. 运行以下命令则可以找到用户目录:

```
echo $HOME
```

## 2、Mac和Linux

- 配置在用户目录: ~/.gitconfig

## 3、推荐配置

```
[user]
    name = {Name}
    email = {Email}
[alias]
    st = status
    cm = commit
    br = branch
    co = checkout
    df = diff
    rt = remote
    lg = log --color --graph --pretty=format:'%C(yellow)%h%Creset -%Cred%d%Creset %s  %C(bold blue)(%cn %cr)%Creset'
    ll = log --color  --graph --pretty=format:'%C(yellow)%h%Creset -%Cred%d%Creset %s %C(bold blue)(%cn %cr)%Creset' --no-merges --stat
    up = push origin master
    pr = pull --rebase
[core]
    excludesfile = /Users/Kid7st/.gitignore_global
```