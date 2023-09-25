---
title: Linux 归档与压缩
abbrlink: 90fa8abe
date: 2023-08-30 22:13:19
tags:
    - Linux
    - 文件管理
    - 目录管理
    - shell
categories:
    - 系统管理
---

### [tar命令介绍](https://gnu-linux.readthedocs.io/zh/latest/Chapter01/00_tar.html)

归档/压缩包管理命令

| 参数 | 含义           |
| ---- | -------------- |
| -j   | bz2格式        |
| -z   | gz格式         |
| -c   | 创建归档       |
| -x   | 从归档提取     |
| -f   | 指定文件名称   |
| -C   | 指定压缩位置   |
| -v   | 显示进度       |
| -t   | 阅读归档但不解 |
| -r   | 追加文件归档   |

### 归档

归档时，不指定-j/-z

文件后缀为.tar

#### 归档操作

```shell
tar -cvf myarchive.tar /etc /root/anaconda-ks.cfg
#归档 显示进度 指定文件名称
```

#### 解包操作

```shell
tar -xvf myarchive.tar -C /tmp/
#解包 显示进度 指定文件路径
```

### 压缩

#### gzip格式

##### 压缩

```shell
tar zcvf bk.tar.gz *c
#tar gz格式、压缩、显示进度、指定压缩名称 <压缩包名称> <被操作文件>
```

##### 解压缩

```shell
tar zxcf bk.tar.gz
#tar gz格式、解压缩、显示进度、指定压缩包名称 <压缩包名称>
```

解压缩到指定位置

```shell
tar zxvf bk.tar.gz -C ./test
#tar gz格式、解压缩、显示进度、指定压缩包名称 -C 指定位置位置
```

#### bz2格式

与前者一致

压缩比率高，但耗时长

```shell
tar jcvf bk.tar.bz2 *c
#bz2格式 压缩 显示进度 指定文件
```

