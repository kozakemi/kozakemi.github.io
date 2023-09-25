---
title: MSP-EXP430F5529LP「未完成」
id: '265'
tags:
  - MSP430
  - F5529
categories:
  - 嵌入式开发
  - TI
abbrlink: c973fd9a
date: 2021-08-05 17:36:47
---

# 时钟

Links：[DongshengHe的博客](http://blog.sina.com.cn/s/blog_767322520102wtgw.html)

​ [BILIBILI\_Barrix](https://www.bilibili.com/video/BV1Vy4y1h7Mf?p=48)

## 五个时钟源

XT1CLK XT2CLK VLOCLK DCOCLK

**FLL**

![Screenshot_20210725_155853](https://kozakemi.oss-cn-beijing.aliyuncs.com/Screenshot_20210725_155853.jpg)

FLL的默认参考时钟为XT1CLK

​ 可选的默认参考时钟有XT1CLK、XT2CLK、REFOCLK

## 三种时钟信号

![Screenshot_20210725_160609](https://kozakemi.oss-cn-beijing.aliyuncs.com/Screenshot_20210725_160609.jpg)

定时器、ADC常用SMCLK

## 输出频率计算公式

DCO频率:

![截屏2021-08-05 下午3.51.26](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-08-05%20%E4%B8%8B%E5%8D%883.51.26.png)

fFLLREFCLK（参考时钟）源的选择由UCSCTL3->SELREF控制

生成PWM波：频率=\[(A/S/M)/(TACC0+1)\]/2

D:UCSCTL2->FLLD;

N:UCSCTL2->FLLN;

fFLLREFCLK:UCSCTL3->SELREF;

n:UCSCTL3->FLLREFDIV

## UCSCTL0

一般无需配置

DCO MOD

![截屏2021-08-05 下午3.44.46](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-08-05%20%E4%B8%8B%E5%8D%883.44.46.png)

## UCSCTL1

DCORSEL DISMOD

![截屏2021-08-05 下午3.46.35](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-08-05%20%E4%B8%8B%E5%8D%883.46.35.png)

### DCO Frequency

调节DCORSEL

DCOx=31中选择

![image-20210726111927011](https://kozakemi.oss-cn-beijing.aliyuncs.com/image-20210805165936802.png)

DCOx=31

### Msp430f5529.h

```
#define DCORSEL_0              (0x0000)       /* DCO RSEL 0 *///0.7    1.7         MHZ
#define DCORSEL_1              (0x0010)       /* DCO RSEL 1 *///1.47    3.45
#define DCORSEL_2              (0x0020)       /* DCO RSEL 2 *///3.17    7.38
#define DCORSEL_3              (0x0030)       /* DCO RSEL 3 *///6.07    14
#define DCORSEL_4              (0x0040)       /* DCO RSEL 4 *///12.3    28.2
#define DCORSEL_5              (0x0050)       /* DCO RSEL 5 *///23.7    54.1
#define DCORSEL_6              (0x0060)       /* DCO RSEL 6 *///39    88
#define DCORSEL_7              (0x0070)       /* DCO RSEL 7 *///60    135
```

### 示例：

```
UCSCTL1=DCORSEL_0;//限制DCO频率为0.7～1.7MHZ
```

## UCSCTL2

FLLD FLLN

![截屏2021-08-05 下午4.04.53](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-08-05%20%E4%B8%8B%E5%8D%884.04.53.png)

![截屏2021-08-05 下午4.05.52](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-08-05%20%E4%B8%8B%E5%8D%884.05.52.png)

### msp430f5529.h

FLLD

```
#define FLLD_0                 (0x0000)       /* Multiply Selected Loop Freq. 1 */
#define FLLD_1                 (0x1000)       /* Multiply Selected Loop Freq. 2 */
#define FLLD_2                 (0x2000)       /* Multiply Selected Loop Freq. 4 */
#define FLLD_3                 (0x3000)       /* Multiply Selected Loop Freq. 8 */
#define FLLD_4                 (0x4000)       /* Multiply Selected Loop Freq. 16 */
#define FLLD_5                 (0x5000)       /* Multiply Selected Loop Freq. 32 */
#define FLLD_6                 (0x6000)       /* Multiply Selected Loop Freq. 32 */
#define FLLD_7                 (0x7000)       /* Multiply Selected Loop Freq. 32 */
#define FLLD__1                (0x0000)       /* Multiply Selected Loop Freq. By 1 */
#define FLLD__2                (0x1000)       /* Multiply Selected Loop Freq. By 2 */
#define FLLD__4                (0x2000)       /* Multiply Selected Loop Freq. By 4 */
#define FLLD__8                (0x3000)       /* Multiply Selected Loop Freq. By 8 */
#define FLLD__16               (0x4000)       /* Multiply Selected Loop Freq. By 16 */
#define FLLD__32               (0x5000)       /* Multiply Selected Loop Freq. By 32 */
```

FLLN直接以十进制+UCSCTL2即可

### 示例：

```
UCSCTL2=FLLD_0+1;//D=1,N=1
```

## UCSCTL3

SELREF FLLREFDIV

![截屏2021-08-05 下午4.06.58](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-08-05%20%E4%B8%8B%E5%8D%884.06.58.png)

配置fFFLREFCKLK得参考时钟源和分频

### msp430f5529.h

SELREF

```
#define SELREF_0               (0x0000)       /* FLL Reference Clock Select 0 *///XT1CLK
#define SELREF_1               (0x0010)       /* FLL Reference Clock Select 1 */
#define SELREF_2               (0x0020)       /* FLL Reference Clock Select 2 *///REFOCLK
#define SELREF_3               (0x0030)       /* FLL Reference Clock Select 3 */
#define SELREF_4               (0x0040)       /* FLL Reference Clock Select 4 */
#define SELREF_5               (0x0050)       /* FLL Reference Clock Select 5 *///XT2CLK
#define SELREF_6               (0x0060)       /* FLL Reference Clock Select 6 */
#define SELREF_7               (0x0070)       /* FLL Reference Clock Select 7 */
#define SELREF__XT1CLK         (0x0000)       /* Multiply Selected Loop Freq. By XT1CLK */
#define SELREF__REFOCLK        (0x0020)       /* Multiply Selected Loop Freq. By REFOCLK */
#define SELREF__XT2CLK         (0x0050)       /* Multiply Selected Loop Freq. By XT2CLK */
```

FLLREFDIV

```
#define FLLREFDIV_0            (0x0000)       /* Reference Divider: f(LFCLK)/1 */
#define FLLREFDIV_1            (0x0001)       /* Reference Divider: f(LFCLK)/2 */
#define FLLREFDIV_2            (0x0002)       /* Reference Divider: f(LFCLK)/4 */
#define FLLREFDIV_3            (0x0003)       /* Reference Divider: f(LFCLK)/8 */
#define FLLREFDIV_4            (0x0004)       /* Reference Divider: f(LFCLK)/12 */
#define FLLREFDIV_5            (0x0005)       /* Reference Divider: f(LFCLK)/16 */
#define FLLREFDIV_6            (0x0006)       /* Reference Divider: f(LFCLK)/16 */
#define FLLREFDIV_7            (0x0007)       /* Reference Divider: f(LFCLK)/16 */
#define FLLREFDIV__1           (0x0000)       /* Reference Divider: f(LFCLK)/1 */
#define FLLREFDIV__2           (0x0001)       /* Reference Divider: f(LFCLK)/2 */
#define FLLREFDIV__4           (0x0002)       /* Reference Divider: f(LFCLK)/4 */
#define FLLREFDIV__8           (0x0003)       /* Reference Divider: f(LFCLK)/8 */
#define FLLREFDIV__12          (0x0004)       /* Reference Divider: f(LFCLK)/12 */
#define FLLREFDIV__16          (0x0005)       /* Reference Divider: f(LFCLK)/16 */
```

### 示例

```
UCSCTL3=SELREF_5+FLLREFDIV_3;//n=8,FLLREFCLK时钟源为XT2CLK
```

## UCSCTL4

SELA SELS SELM

![截屏2021-08-05 下午4.01.58](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-08-05%20%E4%B8%8B%E5%8D%884.01.58.png)

![截屏2021-08-05 下午4.02.27](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-08-05%20%E4%B8%8B%E5%8D%884.02.27.png)

配置ACLK MCLK SMCLK的时钟源

### 配置方法如下：

![截屏2021-08-05 下午4.12.10](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-08-05%20%E4%B8%8B%E5%8D%884.12.10.png)

### 如果只配置一个：

```
UCSCTL4=SELS_3;//设置SMCLK时钟源为DCO
```

### msp430f5529.h

SELA

```
#define SELA_0                 (0x0000)       /* ACLK Source Select 0 *///XT1CLK
#define SELA_1                 (0x0100)       /* ACLK Source Select 1 *///VLOCLK
#define SELA_2                 (0x0200)       /* ACLK Source Select 2 *///REFOCLK
#define SELA_3                 (0x0300)       /* ACLK Source Select 3 *///DCOCLK 
#define SELA_4                 (0x0400)       /* ACLK Source Select 4 *///DCOCLKDIV
#define SELA_5                 (0x0500)       /* ACLK Source Select 5 *///XT2CLK 
#define SELA_6                 (0x0600)       /* ACLK Source Select 6 *///
#define SELA_7                 (0x0700)       /* ACLK Source Select 7 *///
#define SELA__XT1CLK           (0x0000)       /* ACLK Source Select XT1CLK */
#define SELA__VLOCLK           (0x0100)       /* ACLK Source Select VLOCLK */
#define SELA__REFOCLK          (0x0200)       /* ACLK Source Select REFOCLK */
#define SELA__DCOCLK           (0x0300)       /* ACLK Source Select DCOCLK */
#define SELA__DCOCLKDIV        (0x0400)       /* ACLK Source Select DCOCLKDIV */
#define SELA__XT2CLK           (0x0500)       /* ACLK Source Select XT2CLK */
```

SELS

```
#define SELS_0                 (0x0000)       /* SMCLK Source Select 0 *///XT1CLK
#define SELS_1                 (0x0010)       /* SMCLK Source Select 1 *///VLOCLK
#define SELS_2                 (0x0020)       /* SMCLK Source Select 2 *///REFOCLK
#define SELS_3                 (0x0030)       /* SMCLK Source Select 3 *///DCOCLK 
#define SELS_4                 (0x0040)       /* SMCLK Source Select 4 *///DCOCLKDIV
#define SELS_5                 (0x0050)       /* SMCLK Source Select 5 *///XT2CLK 
#define SELS_6                 (0x0060)       /* SMCLK Source Select 6 *///
#define SELS_7                 (0x0070)       /* SMCLK Source Select 7 *///
#define SELS__XT1CLK           (0x0000)       /* SMCLK Source Select XT1CLK */
#define SELS__VLOCLK           (0x0010)       /* SMCLK Source Select VLOCLK */
#define SELS__REFOCLK          (0x0020)       /* SMCLK Source Select REFOCLK */
#define SELS__DCOCLK           (0x0030)       /* SMCLK Source Select DCOCLK */
#define SELS__DCOCLKDIV        (0x0040)       /* SMCLK Source Select DCOCLKDIV */
#define SELS__XT2CLK           (0x0050)       /* SMCLK Source Select XT2CLK */
```

SELM

```
#define SELM_0                 (0x0000)       /* MCLK Source Select 0 *///XT1CLK
#define SELM_1                 (0x0001)       /* MCLK Source Select 1 *///VLOCLK
#define SELM_2                 (0x0002)       /* MCLK Source Select 2 *///REFOCLK
#define SELM_3                 (0x0003)       /* MCLK Source Select 3 *///DCOCLK 
#define SELM_4                 (0x0004)       /* MCLK Source Select 4 *///DCOCLKDIV
#define SELM_5                 (0x0005)       /* MCLK Source Select 5 *///XT2CLK 
#define SELM_6                 (0x0006)       /* MCLK Source Select 6 *///
#define SELM_7                 (0x0007)       /* MCLK Source Select 7 *///
#define SELM__XT1CLK           (0x0000)       /* MCLK Source Select XT1CLK */
#define SELM__VLOCLK           (0x0001)       /* MCLK Source Select VLOCLK */
#define SELM__REFOCLK          (0x0002)       /* MCLK Source Select REFOCLK */
#define SELM__DCOCLK           (0x0003)       /* MCLK Source Select DCOCLK */
#define SELM__DCOCLKDIV        (0x0004)       /* MCLK Source Select DCOCLKDIV */
#define SELM__XT2CLK           (0x0005)       /* MCLK Source Select XT2CLK */
```

L19、L20

​ **ACLK·P1.0输出**

![Screenshot_20210726_155321](https://kozakemi.oss-cn-beijing.aliyuncs.com/Screenshot_20210726_155534.jpg)

## UCSCTL5

DIVPA DIVA DIVS DIVM

![截屏2021-08-05 下午4.25.54](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-08-05%20%E4%B8%8B%E5%8D%884.25.54.png)

![截屏2021-08-05 下午4.26.30](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-08-05%20%E4%B8%8B%E5%8D%884.26.30.png)

配置ACLK、SMCLK、MCLK分频

### msp430f5529.h

DIVPA

```
#define DIVPA_0                (0x0000)       /* ACLK from Pin Source Divider 0 *///f(ACLK)/1 
#define DIVPA_1                (0x1000)       /* ACLK from Pin Source Divider 1 *///f(ACLK)/2 
#define DIVPA_2                (0x2000)       /* ACLK from Pin Source Divider 2 *///f(ACLK)/4 
#define DIVPA_3                (0x3000)       /* ACLK from Pin Source Divider 3 *///f(ACLK)/8 
#define DIVPA_4                (0x4000)       /* ACLK from Pin Source Divider 4 *///f(ACLK)/16
#define DIVPA_5                (0x5000)       /* ACLK from Pin Source Divider 5 *///f(ACLK)/32
#define DIVPA_6                (0x6000)       /* ACLK from Pin Source Divider 6 *///
#define DIVPA_7                (0x7000)       /* ACLK from Pin Source Divider 7 *///
#define DIVPA__1               (0x0000)       /* ACLK from Pin Source Divider f(ACLK)/1 */
#define DIVPA__2               (0x1000)       /* ACLK from Pin Source Divider f(ACLK)/2 */
#define DIVPA__4               (0x2000)       /* ACLK from Pin Source Divider f(ACLK)/4 */
#define DIVPA__8               (0x3000)       /* ACLK from Pin Source Divider f(ACLK)/8 */
#define DIVPA__16              (0x4000)       /* ACLK from Pin Source Divider f(ACLK)/16 */
#define DIVPA__32              (0x5000)       /* ACLK from Pin Source Divider f(ACLK)/32 */
```

DIVA

```
#define DIVA_0                 (0x0000)       /* ACLK Source Divider 0 *///f(ACLK)/1 
#define DIVA_1                 (0x0100)       /* ACLK Source Divider 1 *///f(ACLK)/2 
#define DIVA_2                 (0x0200)       /* ACLK Source Divider 2 *///f(ACLK)/4 
#define DIVA_3                 (0x0300)       /* ACLK Source Divider 3 *///f(ACLK)/8 
#define DIVA_4                 (0x0400)       /* ACLK Source Divider 4 *///f(ACLK)/16
#define DIVA_5                 (0x0500)       /* ACLK Source Divider 5 *///f(ACLK)/32
#define DIVA_6                 (0x0600)       /* ACLK Source Divider 6 */
#define DIVA_7                 (0x0700)       /* ACLK Source Divider 7 */
#define DIVA__1                (0x0000)       /* ACLK Source Divider f(ACLK)/1 */
#define DIVA__2                (0x0100)       /* ACLK Source Divider f(ACLK)/2 */
#define DIVA__4                (0x0200)       /* ACLK Source Divider f(ACLK)/4 */
#define DIVA__8                (0x0300)       /* ACLK Source Divider f(ACLK)/8 */
#define DIVA__16               (0x0400)       /* ACLK Source Divider f(ACLK)/16 */
#define DIVA__32               (0x0500)       /* ACLK Source Divider f(ACLK)/32 */
```

DIVS

```
#define DIVS_0                 (0x0000)       /* SMCLK Source Divider 0 *///f(SMCLK)/1 
#define DIVS_1                 (0x0010)       /* SMCLK Source Divider 1 *///f(SMCLK)/2 
#define DIVS_2                 (0x0020)       /* SMCLK Source Divider 2 *///f(SMCLK)/4 
#define DIVS_3                 (0x0030)       /* SMCLK Source Divider 3 *///f(SMCLK)/8 
#define DIVS_4                 (0x0040)       /* SMCLK Source Divider 4 *///f(SMCLK)/16
#define DIVS_5                 (0x0050)       /* SMCLK Source Divider 5 *///f(SMCLK)/32
#define DIVS_6                 (0x0060)       /* SMCLK Source Divider 6 */
#define DIVS_7                 (0x0070)       /* SMCLK Source Divider 7 */
#define DIVS__1                (0x0000)       /* SMCLK Source Divider f(SMCLK)/1 */
#define DIVS__2                (0x0010)       /* SMCLK Source Divider f(SMCLK)/2 */
#define DIVS__4                (0x0020)       /* SMCLK Source Divider f(SMCLK)/4 */
#define DIVS__8                (0x0030)       /* SMCLK Source Divider f(SMCLK)/8 */
#define DIVS__16               (0x0040)       /* SMCLK Source Divider f(SMCLK)/16 */
#define DIVS__32               (0x0050)       /* SMCLK Source Divider f(SMCLK)/32 */
```

DIVM

```
#define DIVM_0                 (0x0000)       /* MCLK Source Divider 0 *///f(MCLK)/1 
#define DIVM_1                 (0x0001)       /* MCLK Source Divider 1 *///f(MCLK)/2 
#define DIVM_2                 (0x0002)       /* MCLK Source Divider 2 *///f(MCLK)/4 
#define DIVM_3                 (0x0003)       /* MCLK Source Divider 3 *///f(MCLK)/8 
#define DIVM_4                 (0x0004)       /* MCLK Source Divider 4 *///f(MCLK)/16
#define DIVM_5                 (0x0005)       /* MCLK Source Divider 5 *///f(MCLK)/32
#define DIVM_6                 (0x0006)       /* MCLK Source Divider 6 */
#define DIVM_7                 (0x0007)       /* MCLK Source Divider 7 */
#define DIVM__1                (0x0000)       /* MCLK Source Divider f(MCLK)/1 */
#define DIVM__2                (0x0001)       /* MCLK Source Divider f(MCLK)/2 */
#define DIVM__4                (0x0002)       /* MCLK Source Divider f(MCLK)/4 */
#define DIVM__8                (0x0003)       /* MCLK Source Divider f(MCLK)/8 */
#define DIVM__16               (0x0004)       /* MCLK Source Divider f(MCLK)/16 */
#define DIVM__32               (0x0005)       /* MCLK Source Divider f(MCLK)/32 */
```

### 示例：

```
UCSCTL5=DIVS_0;//SMCLK 时钟源分频，1分频
```

## UCSCTL6

![截屏2021-08-05 下午4.31.13](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-08-05%20%E4%B8%8B%E5%8D%884.31.13.png)

![截屏2021-08-05 下午4.31.34](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-08-05%20%E4%B8%8B%E5%8D%884.31.34.png)

启用XT1

```
P5SEL = BIT4BIT5; //将IO配置为XT1功能 
UCSCTL6 &= ~XT1OFF; //使能XT1 
```

启用XT2

```
     P5SEL = BIT2 + BIT3; //XT2引脚功能选择
    UCSCTL6 &= ~XT2OFF;          //打开XT2
```

## 故障处理系统

## UCSCTL7

![截屏2021-08-05 下午4.32.16](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-08-05%20%E4%B8%8B%E5%8D%884.32.16.png)

![截屏2021-08-05 下午4.32.36](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-08-05%20%E4%B8%8B%E5%8D%884.32.36.png)

## UCSCTL8

![截屏2021-08-05 下午4.33.53](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-08-05%20%E4%B8%8B%E5%8D%884.33.53.png)

## 时钟配置模板

```
void initclock()
{
        P5SEL = BIT2 + BIT3; //XT2引脚功能选择
        UCSCTL6 &= ~XT2OFF;          //打开XT2
        UCSCTL1=DCORSEL_0;//1MHZ
        UCSCTL2=FLLD_0+1;//D=1,N=1
        UCSCTL3=SELREF_5+FLLREFDIV_3;//n=8,FLLREFCLK时钟源为XT2CLK
        UCSCTL4=SELS_3;//设置SMCLK时钟源为DCO
        UCSCTL5=DIVS_0;//SMCLK 时钟源分频，1分频
              //XT2 4MHZ
                //DCOCLK 1MHZ
              //SMCLK 1MHZ
}
```