---
title: 使用Git上传项目文件夹(http方式)
tags:
  - 软件
id: '646'
categories:
  - - system
  - - Windows
  - - 软件
date: 2022-01-18 19:33:00
---

![1.11.12.58](https://kozakemi.oss-cn-beijing.aliyuncs.com/1.11.12.58.png)

## 安装git

git下载 官网地址: [https://git-scm.com/downloads](https://git-scm.com/downloads/)

一直点击next即安装完成

## 使用git上传

### 在要上传的文件夹内右键 点击 `Git Bash Here`

![image-20220111122650244](https://kozakemi.oss-cn-beijing.aliyuncs.com/image-20220111122650244.png)

### 输入 `git init`

![image-20220111122833222](https://kozakemi.oss-cn-beijing.aliyuncs.com/image-20220111122833222.png)

## 添加文件夹下所有文件

git add .

![image-20220111123634184](https://kozakemi.oss-cn-beijing.aliyuncs.com/image-20220111123634184.png)

### 填写注释

git commit -m "这里写注释的内容"

![image-20220111123724534](https://kozakemi.oss-cn-beijing.aliyuncs.com/image-20220111123724534.png)

## 设置邮箱，用户名

git config --global user.email "you@example.com"  
git config --global user.name  "Your name"

![image-20220111124034077](https://kozakemi.oss-cn-beijing.aliyuncs.com/image-20220111124034077.png)

### 上传

git remote add origin git链接

git链接可以按图示获取

![image-20220111124152846](https://kozakemi.oss-cn-beijing.aliyuncs.com/image-20220111124152846.png)

git push -u origin master -f

输入账号密码(有此仓库的管理权限)

![image-20220111130124809](https://kozakemi.oss-cn-beijing.aliyuncs.com/image-20220111130124809.png)

## 上传成功