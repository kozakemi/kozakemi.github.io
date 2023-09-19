---
title: Linux 文件与文件夹与目录管理
abbrlink: dec2fff5
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

### 文件查看

**5种方式**

#### more

​	以一个页的方式显示文件内容 按下space键翻下一页

#### less

​	向前或向后查看文件 u前进半页 y后退半页 q退出

#### head

​	查看文件开头内容 -n 指定行数 -c指定读取的字节数

#### tail

​	查看文件尾部内容，附加指令与head相同

#### cat

​	显示文件内容 -n 对文档编行号包括空格，-b不包括空格

#### **wc**

​	统计
### 文本与文件查找

#### 查找文件 find

查找指定文件

```shell
find 路径名 -name 文件名
```

#### 查找内容位置 grep

文件查找内容的行号

```shell
grep 文件内容 文件  -n
```

```shell
kozakemivm@kozakemivm-virtual-machine:~$ grep 2.c text.txt -n
1:2.c
```

递归查询含有此内容的文件

```shell
grep 文件内容 目录 -R -n
```

```shell
kozakemivm@kozakemivm-virtual-machine:~$ grep tcp ./* -R -n
匹配到二进制文件 ./下载/360jiagubao_linux_64.zip
匹配到二进制文件 ./下载/360jiagubao_linux_64/jiagu/java/lib/tools.jar
匹配到二进制文件 ./下载/360jiagubao_linux_64/jiagu/java/lib/amd64/libjfxwebkit.so
匹配到二进制文件 ./下载/360jiagubao_linux_64/jiagu/java/lib/amd64/server/libjvm.so
匹配到二进制文件 ./下载/360jiagubao_linux_64/jiagu/java/lib/rt.jar
匹配到二进制文件 ./下载/360jiagubao_linux_64/jiagu/jiagu.jar
匹配到二进制文件 ./下载/360jiagubao_linux_64/jiagu/tools/lib64/libLLVM_android.so
匹配到二进制文件 ./下载/360jiagubao_linux_64/jiagu/tools/lib64/libclang_android.so
匹配到二进制文件 ./下载/360jiagubao_linux_64/jiagu/tools/bundletool-all-1.10.0.jar
匹配到二进制文件 ./下载/360jiagubao_linux_64/jiagu/tools/LinuxJGHelperLoginHelper
```

借助管道，查询上一条治理中包含内容的输出

```shell
<cmd> | grep 内容
```

```shell
kozakemivm@kozakemivm-virtual-machine:~$ ls /bin | grep cp
cp
cpio
```

经常也是配合正则表达式筛选

