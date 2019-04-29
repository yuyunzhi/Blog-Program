---
title: 设置Maven环境变量
date: 2019-02-05 09:59:31
categories: [Java]
tags: [Maven]
---

## 下载、安装

- 从[Maven官方](http://www-us.apache.org/dist/maven/maven-3/3.5.4/binaries/apache-maven-3.5.4-bin.zip)下载压缩包
- 解压文件夹 apache-maven-3.5.4 到任意文件夹，获取文件夹安装路径。例如我解压到 **D:\apache-maven-3.5.4**

## 设置环境变量

- 打开：我的电脑——右键——属性——高级系统设置——环境变量
- 第一步：点击“系统变量”，设置环境变量 **M2_HOME** 为maven安装的路径
- 第二步：添加 **%M2_HOME%\bin**到系统环境变量path中
- 第三步：点击确定

## 运行命令测试安装

- 运行命令

```
mvn -version
```