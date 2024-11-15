---
title: HC-05/HC-06蓝牙模块配置
published: 2021-11-19 21:34:45
tags: [蓝牙模块,HC-05,HC-06]
category: 嵌入式
draft: false
---

![HC-05](https://kozakemi.oss-cn-beijing.aliyuncs.com/9287121000_1339493813.400x400.jpg)

# 默认设置

*   模块工作角色：从模式
*   串口参数：38400bits/s 停止位 1位 无校验位
*   配对码：1234
*   设备名称：HC-05/HC06
*   连接模式：任意蓝牙设备连接模式

# 连接方式

## 1.TTL模块

![usb](https://kozakemi.oss-cn-beijing.aliyuncs.com/usb.jpeg)

RXD-TX

TXD-RX

5V(TTL)-VCC/5V(蓝牙)（5V-5V）

GND-GND

> 注意：TTL模块的3.3V和VCC使用跳帽或杜邦线短接

## 2.Arduino

![OIP-C](https://kozakemi.oss-cn-beijing.aliyuncs.com/OIP-C.jpeg)

当身边没有TTL模块时使用，不推荐

TXD-TX

RXD-RX

5V(Arduino)-VCC/5V(蓝牙)（5V-5V）

GND-GND

# 进入AT模式

## HC-05

按住按键不动，TTL或Arduino的USB口接入电脑。慢闪即说明进入成功

## HC-06

直接接入，调试期间不要用蓝牙设备连接蓝牙模块

# 配置AT使用的软件

1.xcom

蓝奏云：[https://kozakemi.lanzoui.com/iREAQwp9o2h](https://kozakemi.lanzoui.com/iREAQwp9o2h) 密码:26bn

![XCOM.exe](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-11-20%20%E4%B8%8B%E5%8D%8810.10.47.png)

2.stc-isp

参见：本站点[Keil uVision5 C51安装+烧录工具 – 樱落星河 (kozakemi.top)](https://www.kozakemi.top/archives/503)

![STC-ISP](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-11-20%20%E4%B8%8B%E5%8D%8810.15.55.png)

# AT命令

需要注意的是，AT命令每句话都要带回车

## HC-05

### 恢复默认设置

```
AT+ORGL
```

### 设置蓝牙名称

```
AT+NAME=kozakemi
```

> 蓝牙名称随意，不要中文或特殊符号

### 设置配对码

配对码（密码）为数字

```
AT+PSWD=1234
```

### 设置工作角色

```
AT+ROLE=1
```

> ‘ 1 ’为主机、‘ 2 ’为从机

### 设置串口参数

配置波特率、停止位和校验位

```
AT+UART=115200,0,0
```

### 查询蓝牙地址

```
AT+ADDR?
```

### 清空配对的蓝牙设备

目的：方便下一次连接蓝牙设备

```
AT+RMAAD
```

### 设置连接模式

```
AT+CMODE=1
```

> 值为‘ 1 ’时，允许任何设备连接，值为‘ 0 ’时，你应该设置要配对的蓝牙地址，且其他蓝牙设备无法联入此设备

### 退出AT模式

再次断电再接入即可退出AT模式

## HC-06

### 设置蓝牙名称

```
AT+NAMExxxx
```

例

```
AT+NAMEkozakemi
```

### 设置配对码

```
AT+PINxxxx
```

例

```
AT+PIN1234
```

### 设置波特率

```
AT+BAUDx
```

例

```
AT+BAUD
```

> 1--------- 1200
> 
> 2--------- 2400
> 
> 3--------- 4800
> 
> 4--------- 9600（默认就是这个设置）
> 
> 5--------- 19200
> 
> 6--------- 38400
> 
> 7--------- 57600
> 
> 8--------- 115200
> 
> 9--------- 230400
> 
> A--------- 460800
> 
> B--------- 921600
> 
> C--------- 1382400