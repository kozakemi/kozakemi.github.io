---
title: git 简单操作
typora-copy-images-to: git-简单操作
abbrlink: 6f2fd8c3
date: 2023-09-21 17:08:21
excerpt: 本文介绍了 Git 的基本操作流程，包括创建仓库、编写代码、添加文件、提交修改、查看状态、查看提交历史和恢复提交等操作。还包括了远程连接、创建分支、切换分支和删除分支等相关操作。通过这些简单的 Git 命令，你可以更好地管理和控制你的代码版本
tags:
  - git
  - 版本控制
categories:
  - 开发工具
---

## 代码管理

![image-20230921094200045](git-简单操作/image-20230921094200045.png)

## 创建git仓库

```shell
git init
```

## 书写代码

书写并保存文件

## 添加文件

```shell
git add .
```

## 添加提交

```shell
git commit -m "<注释>"
```

## 查看当前状态

```shell
git status
```

## 查看提交历史

```c
git log
```

## 查看修改

```c
git diff
```

## 恢复提交

```shell
git reset --hard <hash>
```

## 远程连接

```shell
git remote add origin git@github.com:<username>/<depository name>.git
git branch -M main
git push -u origin main
```

## 创建分支

```shell
git branch <branch-name>
```

## 切换分支

```shell
git checkout  <branch-name>
```

## 删除分支

```shell
git branch -d <branch-name>#删除本地分支

git push origin :<branch-name> #删除远程分支
```

## 提交分支更改

```shell
git push origin <branch-name>
```

