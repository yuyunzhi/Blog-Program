---
title: git分支合并master
date: 2018-12-20 13:33:29
categories: [Git]
tags: [Git]
---


- 假如我们现在在dev分支上，刚开发完项目，执行了下列命令

```
git add .
git commit -m ‘dev'
git push -u origin dev
```

- 然后我们要把dev分支的代码合并到master分支上 该如何？ 

- 首先切换到master分支上

```
git checkout master
```

- 如果是多人开发的话 需要把远程master上的代码pull下来

```
git pull origin master
```

- 然后我们把dev分支的代码合并到master上

```
//有权限改动master
git merge dev

//没有权限改动master
git branch dev
git merge master
```

- 这个时候打开编辑器，处理冲突的代码
- 这个时候打开编辑器，处理冲突的代码
- 这个时候打开编辑器，处理冲突的代码

- 然后查看状态

```
git status
```

- 执行下面命令即可

```
//有权限
git push origin master

//无权限
git push origin dev
```

- 在merge的状态下，提交到仓库

```
git add .
git commit -m ‘xxx'
git push
```