---
title: Keil uVision5 C51安装+烧录工具
tags:
  - C51
  - keil5
  - 软件
id: '503'
categories:
  - - C/C++
  - - C51
  - - MCU
  - - system
  - - Windows
  - - 编程语言
date: 2021-10-19 13:13:59
---

# 关闭安全防护软件

## Windows7及以下

退出自己的杀毒软件即可

## Windows10/11

关闭自己的杀毒软件有概率微软会启动系统自带的杀毒软件

请在 设置 -> 更新与安全 -> Windows安全中心 ->打开Windows安全中心 -> 在左栏选择病毒和威胁防护 -> “病毒和威胁防护”设置 下方的管理设置 -> 关闭所有功能  
![截屏2021-10-19 上午9.09.47](https://kozakemi.oss-cn-beijing.aliyuncs.com/202110190909807.png)

# 安装所需文件

![image-20211019193657512](https://kozakemi.oss-cn-beijing.aliyuncs.com/202110191936670.png)

分别为keil5的软件包、ch340驱动、激活工具、烧录工具，上一步的操作是为了防止下载和运行破解工具时报毒，驱动在日后将程序写入单片机时可能会用到，烧录工具用于向keil5中写入库文件和烧录keil5编译的 .hex 文件

## 文件链接

蓝奏云（无需登录）：https://wws.lanzoui.com/isZKvvjlhqj 密码:61fu  
https://wws.lanzoui.com/iFF9Wvjd33e 密码:epd7

# 安装过程

## 运行c51v960a.exe

运行后如下界面点击 next

![image-20211019091635270](https://kozakemi.oss-cn-beijing.aliyuncs.com/202110190916312.png)

点击 I agree to all the … 前面的框勾选，点击next

![image-20211019091748591](https://kozakemi.oss-cn-beijing.aliyuncs.com/202110190917636.png)

这一步是设置文件路径（不可以有中文）

![image-20211019091915068](https://kozakemi.oss-cn-beijing.aliyuncs.com/202110190919113.png)

这个界面让你填写自己的信息，随便填就好 之后点击下一步

![image-20211019092406820](https://kozakemi.oss-cn-beijing.aliyuncs.com/202110190924863.png)

解包过程

![image-20211019092533367](https://kozakemi.oss-cn-beijing.aliyuncs.com/202110190925419.png)

取消第一个勾选 点击 finish

![image-20211019092720459](https://kozakemi.oss-cn-beijing.aliyuncs.com/202110190927503.png)

# 破解过程

上一步安装完后，会看到桌面有以下图标

![image-20211019092849719](https://kozakemi.oss-cn-beijing.aliyuncs.com/202110190928760.png)

右键，点击 以管理员身份运行 （必须以管理员身份运行）

![image-20211019092945526](https://kozakemi.oss-cn-beijing.aliyuncs.com/202110190929566.png)

点击 file -> License Man….. 复制自己的 CID 注意不要退出这个软件

![image-20211019093152497](https://kozakemi.oss-cn-beijing.aliyuncs.com/202110190931536.png)

![image-20211019093127069](https://kozakemi.oss-cn-beijing.aliyuncs.com/202110190931112.png)

解压 keil\_keygen(2032).zip

运行keygen\_new2032.exe 并出现以下界面

![image-20211019093500315](https://kozakemi.oss-cn-beijing.aliyuncs.com/202110190935368.png)

将CID复制进去 点击 Generate 选中并复制生成的key

![image-20211019093654553](https://kozakemi.oss-cn-beijing.aliyuncs.com/202110190936614.png)

将key复制到 New License ID Code 处 点击Add Lic 即可破解成功

![image-20211019093846911](https://kozakemi.oss-cn-beijing.aliyuncs.com/202110190938956.png)

# 驱动安装

解压CH34x\_Install\_Windows\_v3\_4.zip 右键以管理员身份运行（不以管理员身份运行有机率报错）

![image-20211019125707551](https://kozakemi.oss-cn-beijing.aliyuncs.com/202110191257594.png)

点击安装

![image-20211019125801372](https://kozakemi.oss-cn-beijing.aliyuncs.com/202110191258415.png)

![image-20211019125825468](https://kozakemi.oss-cn-beijing.aliyuncs.com/202110191258506.png)

# 烧录软件

## 普中自动下载软件

![image-20211019200852953](https://kozakemi.oss-cn-beijing.aliyuncs.com/202110192008000.png)

芯片类型：stc89c52rc :选择 stc89cxx （new）

​ Stc89c52 :选择stc89cxx series

串口号选择 插入的烧录工具的端口号 一般以ch340结尾

文件路径：选择编译好的 .hex文件

确认好后，点击程序下载

## stc-isp

解压stc-isp-15xx-v6.88I.zip

运行stc-isp-15xx-v6.88I

![image-20211019202340634](https://kozakemi.oss-cn-beijing.aliyuncs.com/202110192023723.png)

芯片型号按需选择，串口与程序文件选择与普中相同，点击下载/编程 将.hex文件烧录到单片机

![image-20211019202448578](https://kozakemi.oss-cn-beijing.aliyuncs.com/202110192024620.png)

### st-cisp为keil5完善库文件

右侧窗口选择keil仿真设置

![202110191947879](https://kozakemi.oss-cn-beijing.aliyuncs.com/202110192027043.png)

点击 添加型号和头文件到keil中

![202110191950210](https://kozakemi.oss-cn-beijing.aliyuncs.com/202110192028743.png)

选择keil安装目录

![202110191950547](https://kozakemi.oss-cn-beijing.aliyuncs.com/202110192028994.png)

点击确定即可添加stc芯片的型号和头文件

### 如何确定安装目录

方法1.安装时的选择

![image-20211019091915068](https://kozakemi.oss-cn-beijing.aliyuncs.com/202110192030177.png)  
方法2.右键keil5图标，点击属性

![image-20211019203113506](https://kozakemi.oss-cn-beijing.aliyuncs.com/202110192031564.png)

高亮标注区域就是keil的安装路径

![image-20211019203200256](https://kozakemi.oss-cn-beijing.aliyuncs.com/202110192032310.png)