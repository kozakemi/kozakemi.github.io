---
title: ubuntu 软件包管理器 apt
date: 2023-08-30 21:50:19
tags:
---

ubuntu的软件包管理器之一？

 软件包管理器

### update 更新

```shell
sudo apt update
```

使用apt是是否可以不使用sudo？否除root

### search 搜索

在忘记包名时使用

```shell
sudo apt search chromium browser
```

```shell
chromium-browser/focal-updates 1:85.0.4183.83-0ubuntu0.20.04.3 amd64
  Transitional package - chromium-browser -> chromium snap
```

### install 安装

```bash
sudo apt install vim
```

### remove 卸载

```shell
sudo apt remove chromium-browser
```

### 补充知识：sudo 

**临时提提高权限**

何时临时撤销？  

超时未操作将会重新要求输入用户密码

超时时间由`/etc/sudoers` 中的 `Defaults specification` 中指定`imestamp_timeout=N`由N决定 

普通用户仅在/home/username 有权限，其余目录下需要sudo临时提权

