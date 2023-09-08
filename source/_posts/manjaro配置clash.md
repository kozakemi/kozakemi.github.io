---
title: manjaro配置Clash
tags: []
id: '741'
categories:
  - - LINUX
  - - system
  - - 软件
date: 2022-09-15 15:49:23
---

> 参考：
> 
> [https://blog.linioi.com/posts/clash-on-arch/](https://blog.linioi.com/posts/clash-on-arch/)
> 
> [https://zhuanlan.zhihu.com/p/402481568](https://zhuanlan.zhihu.com/p/402481568)

也可使用Clash-for-windows,是Clash的图形化界面，操作更简便，但我的操作系统Service Mode容易出问题，始终没找到原因，故有了此教程

## 安装

```
sudo pacman -S clash
```

## 添加文件

启动clash以生成默认文件

```
clash
```

```
cd /home/{username}/.config/clash/   
```

```
wget wget -O config.yaml '配置链接'
```

从 `https://github.com/Dreamacro/maxmind-geoip/releases` 获取Country.mmdb的下载链接

```
wget -O Country.mmdb 'Country.mmdb的下载链接'
```

## 配置clash

```
clash
```

打开配置文件

```
vim config.yaml
```

打开http://clash.razord.top/ 根据config.yaml填写

![Screenshot_20220915_151353](https://kozakemi.oss-cn-beijing.aliyuncs.com/Screenshot_20220915_151353.png)

![Screenshot_20220915_151528.png](https://kozakemi.oss-cn-beijing.aliyuncs.com/Screenshot_20220915_151528.png)

## 配置系统代理

![congig.yaml](https://kozakemi.oss-cn-beijing.aliyuncs.com/Screenshot_20220915_151934.png)

此处展示kde环境，其他环境类似

![系统设置](https://kozakemi.oss-cn-beijing.aliyuncs.com/Screenshot_20220915_152153.png)

```
localhost,127.0.0.0/8,::1
```

## 终端使用代理

仅在执行此命令的终端生效，不影响其他环境，关闭后失效

端口根据配置文件填写

```
export http_proxy="socks5://127.0.0.1:端口"  
```

```
export https_proxy="socks5://127.0.0.1:端口"  
```

```
export ALL_PROXY="socks5://127.0.0.1:端口"  
```

```
例如：
export http_proxy="socks5://127.0.0.1:7890" 
export ALL_PROXY="socks5://127.0.0.1:7890" 
```

## 测试连接情况

```
curl -I http://www.google.com
```

长时间无响应表示连接失败

正常应类似如下：

```
HTTP/1.1 200 OK
Content-Type: text/html; charset=ISO-8859-1
P3P: CP="This is not a P3P policy! See g.co/p3phelp for more info."
Date: Thu, 15 Sep 2022 07:04:43 GMT
Server: gws
X-XSS-Protection: 0
X-Frame-Options: SAMEORIGIN
Transfer-Encoding: chunked
Expires: Thu, 15 Sep 2022 07:04:43 GMT
Cache-Control: private
Set-Cookie: 1P_JAR=2022-09-15-07; expires=Sat, 15-Oct-2022 07:04:43 GMT; path=/; domain=.google.com; Secure
Set-Cookie: AEC=AakniGNSiBFcMpLMgrQsBLzHvfKdl6N678aMX2KWX5BVvfm97AAfdpph2w; expires=Tue, 14-Mar-2023 07:04:43 GMT; path=/; domain=.google.com; Secure; HttpOnly; SameSite=lax
Set-Cookie: NID=511=OuLqdtxNLw7tA3bR-gH-tV08DY9NdN6K-1k0B-DDhnYaOI35HpU4FnGXZd7nnavgNFXSHiOLx37n0RS0QF5pj8ktE-1X5CIfR83sQe-5Wnony-8o_SVh9pSJ6aUowtf2LdaXgvI7jvD3E8oFcZr0PpD_0qXUIzdQ_NcSTtnjP6I; expires=Fri, 17-Mar-2023 07:04:43 GMT; path=/; domain=.google.com; HttpOnly
```