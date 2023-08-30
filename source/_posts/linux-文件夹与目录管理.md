---
title: linux 文件夹与目录管理
date: 2023-08-30 21:57:51
tags:
---

### 列出文件 ls

```shell
ls
横向排列列出文件及文件夹
ls -a
显示隐藏的文件
ls -l
列表形式显示
ls -hl
将文件大小以更人性化方式显示 kb  mb gb
```

### 查看当前路径 pwd

打印当前目录

```shell
kozakemivm@kozakemivm-virtual-machine:~/cp/1$ pwd
/home/kozakemivm/cp/1
```

### 切换目录 cd

```shell
cd ~
切换家目录
cd /
切换根目录
cd ..
切换到上一目录
cd -
切换到上一个目录
```

### 创建文件夹 mkdir

创建目录

```shell
mkdir dirname
```

### 创建文件 touch

创建文件

```shell
touch filename
```

### 删除文件或文件夹 rm

删除

```shell
rm -r
递归删除（文件夹）
rm -f
强制删除（无权限）
rm -rf *
删除文件夹下所有文件
```

### 清除终端 clear

清除

```shell
清除终端
```

### 复制文件或文件夹 cp

复制

```shell
cp 文件 文件
cp -r 文件夹 文件夹
cp -v 
显示文件操作进度
```

### 移动文件或文件夹 mv

移动

```shell
mv 文件 文件
mv -r 文件夹 文件夹
mv -v 
显示文件操作进度
```

### 读取或链接文件 cat

读取和连接文件

```shell
cat 文件
读取文件
```

### 查看文件编码 file

查看编码

```shell
kozakemivm@kozakemivm-virtual-machine:~$ file text.txt 
text.txt: UTF-8 Unicode text
```

### 查看命令路径 which

查看命令所在文件夹

```shell
kozakemivm@kozakemivm-virtual-machine:~$ which ls
/bin/ls
```

