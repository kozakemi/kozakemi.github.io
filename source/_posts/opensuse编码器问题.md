---
title: openSUSE编码器问题
tags:
  - LINUX
  - opemSUSE
id: '393'
categories:
  - - LINUX
  - - openSUSE
  - - system
abbrlink: 1acf3ca2
date: 2021-09-13 23:25:10
---

## 媒体解码器

<!--解决无法播放h264视频，观看网页视频提示flash等问题-->

注意：选择正确的发行版

### openSUSE Tumbleweed

配置源

 sudo zypper ar -cfg 'https://mirrors.ustc.edu.cn/packman/suse/openSUSE\_Tumbleweed/' PACKMAN

刷新软件源

 sudo zypper ref

安装

 zypper install --allow-vendor-change ffmpeg-3 lame gstreamer-plugins-bad gstreamer-plugins-ugly gstreamer-plugins-ugly-orig-addon gstreamer-plugins-libav libavdevice58 libdvdcss2 vlc-codecs

### openSUSE Leap

配置源

 sudo zypper ar -cfg 'https://mirrors.ustc.edu.cn/packman/suse/openSUSE\_Leap\_/' PACKMAN

刷新软件源

 sudo zypper ref

安装

 zypper install --allow-vendor-change ffmpeg-3 lame gstreamer-plugins-bad gstreamer-plugins-ugly gstreamer-plugins-ugly-orig-addon gstreamer-plugins-libav libavdevice58 libdvdcss2 vlc-codecs