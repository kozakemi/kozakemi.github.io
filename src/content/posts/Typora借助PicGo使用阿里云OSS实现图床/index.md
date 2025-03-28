---
title: Typora借助PicGo使用阿里云OSS实现图床
published: 2023-08-31 18:41:47
tags: [Typora,图床,OSS]
category: 开发工具
draft: false
---

## 参考链接：

[zhihu-阿里云OSS PicGo 配置图床教程 超详细](https://zhuanlan.zhihu.com/p/104152479)

## 安装typora

[Typora官方中文站](https://typoraio.cn/)

## 安装PicGo

[GitHub Release](https://github.com/Molunerfinn/PicGo/releases)

注意记录安装的位置

## 访问阿里云OSS获取信息

1、进入[对象存储页面](https://cn.aliyun.com/product/oss?from_alibabacloud=)，点击右上控制台

![image-20230831184655053](image-20230831184655053.png)

2、进入后点击对象存储OSS **控制台**

![image-20230831185254908](image-20230831185254908.png)

3、点击左侧栏 **Bucket列表**

![image-20230831185452824](image-20230831185452824.png)

4、点击Bucket名称 这里是**kozakemi**

5、点击概览

![image-20230831185556953](image-20230831185556953.png)

需要记住以下信息：

![image-20230831185631146](image-20230831185631146.png)

6、获取key 右上角 **AccessKey管理**

![image-20230831185656487](image-20230831185656487.png)

7、**查看Secret** 并记录内容

![image-20230831185805697](image-20230831185805697.png)



## 填写并测试

keyid对应AccessKey ID

KeySecret对应AccessKey Secret

存储空间为Bucket名称

存储区域为外网访问的除去aliyun.com之外的部分：oss-cn-beijing

存储地址自定

自定义域名不填

![image-20230831190433634](image-20230831190433634.png)

![image-20230831190710493](image-20230831190710493.png)

![image-20230831190614519](image-20230831190614519.png)

