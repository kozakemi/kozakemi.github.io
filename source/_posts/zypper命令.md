---
title: zypper命令
tags:
  - LINUX
  - openSUSE
  - zypper
id: '390'
categories:
  - - LINUX
  - - openSUSE
  - - system
abbrlink: e8479034
date: 2021-09-13 23:21:37
---

# zypper

[ZYpp](https://baike.baidu.com/item/ZYpp/13025395)

## Links

[openSUSE官网](https://www.opensuse.org)

[openSUSE维基](https://zh.opensuse.org/首页)

[openSUSE 镜像使用帮助](https://mirrors.tuna.tsinghua.edu.cn/help/opensuse/)

## zypper软件管理器

注意:在sudo 或 root用户下运行

[zypper命令使用示例](https://blog.csdn.net/linuxnews/article/details/51003580)

### 软件源配置

[openSUSE 镜像使用帮助](https://mirrors.tuna.tsinghua.edu.cn/help/opensuse/)

#### openSUSE Tumbleweed

禁用所有软件源

需要启用的软件源用YaST再次开启

```
sudo zypper mr -da
```

添加 TUNA 镜像源

```
sudo zypper ar -cfg 'https://mirrors.tuna.tsinghua.edu.cn/opensuse/tumbleweed/repo/oss/' tuna-oss
sudo zypper ar -cfg 'https://mirrors.tuna.tsinghua.edu.cn/opensuse/tumbleweed/repo/non-oss/' tuna-non-oss
```

刷新软件源

```
sudo zypper ref
```

#### openSUSE Leap

禁用所有软件源

需要启用的软件源用YaST再次开启

```
sudo zypper mr -da
```

添加 TUNA 镜像源

```
sudo zypper ar -cfg 'https://mirrors.tuna.tsinghua.edu.cn/opensuse/distribution/leap/$releasever/repo/oss/' tuna-oss
sudo zypper ar -cfg 'https://mirrors.tuna.tsinghua.edu.cn/opensuse/distribution/leap/$releasever/repo/non-oss/' tuna-non-oss
sudo zypper ar -cfg 'https://mirrors.tuna.tsinghua.edu.cn/opensuse/update/leap/$releasever/oss/' tuna-update
sudo zypper ar -cfg 'https://mirrors.tuna.tsinghua.edu.cn/opensuse/update/leap/$releasever/non-oss/' tuna-update-non-oss
```

Leap beat用户还需添加 sle 和 backports 源

```
sudo zypper ar -cfg 'https://mirrors.tuna.tsinghua.edu.cn/opensuse/update/leap/$releasever/sle/' tuna-sle-update
sudo zypper ar -cfg 'https://mirrors.tuna.tsinghua.edu.cn/opensuse/update/leap/$releasever/backports/' tuna-backports-update
```

Leap beat注：若在安装时**没有**启用在线软件源， sle 源和 backports 源将在系统首次更新后引入，请确保系统在更新后仅启用了**六个**所需软件源。可使用 `zypper lr` 检查软件源状态，并使用 `zypper mr -d` 禁用多余的软件源。

### 查找命令

```
sudo zypper se <name>
```

安装.rpm

```
sudo zypper in <.rpm文件>
```

### 安装命令

```
sudo zypper in <name>
```

### 软件安装

部分软件可以使用[Tmoe-linux](https://gitee.com/mo2/linux)安装软件，效果相同

#### 安装.rpm

在.rpm文件下

```
sudo zypper in <name.rpm>
```

#### Arduino

[arduino](https://www.arduino.cc/en/software)建议使用下载软件加速

解压 运行 install.sh 文件

[ch340驱动](https://blog.csdn.net/bingyu9875/article/details/80651778)linux自带旧版，也可根据链接安装新版本

#### Typora

[csdn](https://blog.csdn.net/y_universe/article/details/107184300)

#### 网易云音乐

```
sudo zypper in netease-cloud-music
```

#### 迅雷/微信/QQ

![截屏2021-08-15 下午10.16.42](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-08-15%20%E4%B8%8B%E5%8D%8810.16.42.png)

参考：Linux->Tmoe-linux

#### vmtools

系统自动安装

手动：

```
sudo zypper in open-vm-tools 
```

#### steam

安装steam之前，需要安装32位运行环境

32位运行环境：

打开YaST 打开软件管理 选择模组 勾选32位运行环境 接受

![截屏2021-08-15 下午10.18.17](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-08-15%20%E4%B8%8B%E5%8D%8810.18.17.png)

```
sudo zypper in steam
```

或者使用[Tmoe-linux](https://gitee.com/mo2/linux)进行安装

### 卸载命令

```
sudo zypper rm <name>
```

### 更新命令

```
sudo zypper up <name>
```