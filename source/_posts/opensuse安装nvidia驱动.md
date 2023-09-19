---
title: openSUSE安装NVIDIA驱动
tags:
  - LINUX
  - NVIDIA
  - openSUSE
id: '395'
categories:
  - - LINUX
  - - openSUSE
  - - system
abbrlink: 1792a0e4
date: 2021-09-13 23:31:14
---

## 驱动安装

### 驱动下载

[NVIDIA 驱动程序下载](https://www.nvidia.cn/Download/index.aspx?lang=cn)

假定 NVIDIA-Linux-x86\_64-470.63.01.run为下载完成后的驱动文件名

### 禁用 nouveau

```
echo -e 'blacklist nouveau \noptions nouveau modeset=0'  sudo tee -a /etc/modprobe.d/50-blacklist.conf
```

### 更新内核

```
sudo mkinitrd
```

### 重启,使新内核生效

```
sudo reboot
```

### 重启完成后登录系统,安装必要的编译环境

```
sudo zypper install gcc kernel-devel pkg-config libglvnd
```

### 安装当前版本的核心开发包

```
sudo zypper install -y kernel-default-devel=$(uname -r  sed 's/\-default//')
```

### 安装nvidia驱动

此命令在下载的驱动文件夹下执行或拖拽文件替换NVIDIA-Linux-x86\_64-470.63.01.run

```
sudo bash ./NVIDIA-Linux-x86_64-470.63.01.run --no-x-check -z --no-opengl-files
```

安装过程所有选项无需考虑回车即可

## 显卡切换

[openSUSE维基](https://zh.opensuse.org/SDB:NVIDIA_SUSE_Prime)

### 安装suse-prime

```
sudo zypper in suse-prime
```

### 要在intel显卡和NVIDIA显卡之间转换，可以运行：

```
sudo prime-select nvidia
```

或者

```
sudo prime-select intel
```

### 然后注销并重新登录以应用显卡变更。

验证

要确证你使用的是哪一张显卡，你可以通过命令确认：

```
sudo glxinfo  grep -i render  grep -Ei "intelnvidia"
```

如果显示的红色字样为 “Intel” ，则正在使用 Intel 显卡，若显示的红色字样为“NVIDIA” ，则使用的显卡为 NVIDIA 显卡。