---
title: MSP-EXP430G2
tags:
  - G2553
  - TI
id: '228'
categories:
  - - MCU
  - - TI
abbrlink: 669d6f9d
date: 2021-07-13 19:46:37
---

# IO

## PxDIR PxOUT PxIN

### PxDIR 方向寄存器

作用:配置读/写模式

8位寄存器 初始化00000000 0代表输入 1表示输出

​ 例P1.0输出:00000001

​ P1DIR=BIT0 //等价于:P1DIR=P1DIRBIT0 ( 无论如何将P1.0置1)

​ P1DIR &=~BIT0 //等价于:P1DIR=P1DIR&~BIT0 (无论如何将P1.0置0)

来自百度文库:[正确理解MSP430的](https://wenku.baidu.com/view/226945e9f8c75fbfc77db279.html)[PxDIR](https://wenku.baidu.com/view/226945e9f8c75fbfc77db279.html)[和PxIN、PxOUT之间的关系](https://wenku.baidu.com/view/226945e9f8c75fbfc77db279.html)

### PxOUT输出寄存器

​ 作用:输出

​ 例P1.7->1 P1.0->0 P1OUT,1xxxxxx0

### PxIN输入寄存器

作用:读取外部状态

P1.6 若P1IN,x1xxxxxx 则P1.6高电平。x0xxxxxx 低电平。

## 点亮LED

### Light\_A\_LED

```
#include <msp430.h> 
int main(void)
{
  WDTCTL = WDTPW + WDTHOLD;    //关闭看门狗定时器
  P1DIR=BIT0;//将P1.0设为输出    P1DIR=P1DIRBIT0   按位[或]
  P1OUT=BIT0;//令P1.0输出高电平
  while(1){

  }
 return 0;   
}
```

## LED闪烁

### Flash\_LED

```
#include <msp430.h>

int main(void)
{
    WDTCTL = WDTPW  WDTHOLD;   // stop watchdog timer
    P1DIR=BIT6;//将P1.0设为输出    P1DIR=P1DIRBIT0   按位[或]
  P1OUT=BIT6;//令P1.0输出高电平
  while(1){
    P1OUT^=BIT6;//^异或
   __delay_cycles(500000);//500ms延迟(系统时钟约1MHZ)
  }
    return 0;
}
```

## 按键控制LED (PxIN)

检测io电平的方式判断按键

### Key\_Light\_LED

```
#include <msp430.h>

int main(void)
{
    WDTCTL = WDTPW  WDTHOLD;   // stop watchdog timer
    P1DIR=BIT6;//将P1.0设为输出    P1DIR=P1DIRBIT0   按位[或]
    P1OUT&=~BIT6;//令P1.0输出高电平
    P1DIR&=~BIT3;//输入状态

    P1REN=BIT3;//使能
    P1OUT=BIT3;//上拉电阻
    while(1)
    {
      if(P1IN & BIT3){//未按下
        P1OUT&=~BIT6;
      }
      else{//按下
        P1OUT=BIT6;
      }
     }
    return 0;
}
```

# IO中断

## P1和P2中断

![截屏2021-07-14 下午5.43.56](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-14%20%E4%B8%8B%E5%8D%885.43.56.png)

仅P1、P2有中断

P1-7 共用1个中断服务函数

P2同理 但P2.6-7不可用

## 中断标志寄存器P1IFG,P2IFG

![截屏2021-07-15 下午3.44.10](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-15%20%E4%B8%8B%E5%8D%883.44.10.png)

作用:将中断明确到某一针脚 例:P1.7

被选择的输入信号:中断边沿选择寄存器设置的上升沿/下降沿

P.S.必须软件复位不然将一直执行中断

## 中断边沿选择寄存器P1IES，P2IES

![截屏2021-07-15 下午3.48.09](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-15%20%E4%B8%8B%E5%8D%883.48.09.png)

![截屏2021-07-15 下午3.51.49](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-15%20%E4%B8%8B%E5%8D%883.51.49.png)

设定怎样的电平改变(高 ->低(下降沿) / 低 ->高(上升沿))来判断中断改变PxIFGx

## 中断使能(开起/关闭 中断)P1IE，P2IE

![截屏2021-07-15 下午3.54.07](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-15%20%E4%B8%8B%E5%8D%883.54.07.png)

打开总中断GIE 查看:中断标志寄存器

## 步骤

#### 打开某端口中断(中断使能)

​ 打开P1.3中断

```
P1IE=BIT3;
```

#### 配置触发中断方式(中断边沿选择寄存器)

```
P1IES=BIT3;//下降沿
```

### 清除中断标志位(中断标志寄存器)

```
P1IFG &= ~BIT3;
```

#### 打开全局中断GIE

```
__bis_SR_register(GIE);
```

#### 配置中断函数(发生中断运行的函数)

pragma vector = 中断向量

中断向量在头文件中查找

​ msp430g2553

```
/************************************************************
* Interrupt Vectors (offset from 0xFFE0)
************************************************************/
```

![image-20210715172034212](https://kozakemi.oss-cn-beijing.aliyuncs.com/image-20210715172034212.png)

​ main.c中断函数模版

​ 执行完后记得清除中断标志位

```
#pragma vector = PORT1_VECTOR//PORT1_VECTOR中断向量在头文件中查找
__interrupt void Port1_ISR(void)//void Port1_ISR 函数名,随意命名
{

}
```

## 按键控制LED (中断)

#### Key\_Light\_LED\_UsingInterrupt

```
#include <msp430.h> 
int main(void)
{
    WDTCTL = WDTPW  WDTHOLD;    // 关闭看门狗定时器
    /*初始化LED2所在IO口P1.6为输出*/
    P1DIR = BIT6;
    /*初始化LED2所在IO口P1.6为低电平，LED2初始状态为灭*/
    P1OUT &= ~BIT6;
    /*初始化按键所在IO口P1.3为输入*/
    P1DIR &= ~BIT3;
    /*使能P1.3口的上拉电阻*/
    P1REN = BIT3;
    P1OUT = BIT3;
    /*打开P1.3口中断*/
    P1IE = BIT3;
    /*设定为下降沿触发*/
    P1IES = BIT3;
    /*清除中断标志位*/
    P1IFG &= ~BIT3;
    /*打开全局中断*/
    __bis_SR_register(GIE);
    while(1)
    {

    }
    return 0;
}

#pragma vector = PORT1_VECTOR
__interrupt void Port1_ISR(void)
{
    if(P1IFG & BIT3)//判断是否是P1.3产生中断
    {
        P1IFG &= ~BIT3;//清除标志位
        P1OUT ^= BIT6;
    }
}
```

# 时钟

##### MSP430G2553有以下高亮标注的时钟

![截屏2021-07-11 下午8.03.26](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-11%20%E4%B8%8B%E5%8D%888.03.26.png)

### 修改主机时钟MCLK

### System\_Clock

```
#include <msp430.h> 
int main(void)
{
    WDTCTL = WDTPW  WDTHOLD;    // Stop watchdog timer
    /*设置MCLK频率*/
    DCOCTL = CALDCO_8MHZ;
    BCSCTL1 = CALBC1_8MHZ;
  //预置有1,2,8,12,17MHZ
  /*初始化LED2所在IO口P1.6为输出*/
    P1DIR = BIT6;
    /*初始化LED2所在IO口P1.6为低电平，LED2初始状态为灭*/
    P1OUT &= ~BIT6;
    while(1)
    {
        P1OUT ^= BIT6;
        __delay_cycles(500000);
    }
    return 0;
}
```

# 串口配置

MAP430G2553仅有USCI\_A0,USCI\_B0模块

![截屏2021-07-13 下午5.34.51](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-13%20%E4%B8%8B%E5%8D%885.34.51.png)

## 确认以下参数:

校验位 停止位 数据位 波特率

## **USCI** 初始化和复位

![截屏2021-07-13 下午6.02.29](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-13%20%E4%B8%8B%E5%8D%886.02.29.png)

### 1.**UCAxCTL0**，**USCI\_Ax** 控制寄存器 **0**(UCA0CTL0)

​ UCPEN UCPAR UCMSB UC7BIT UCSPB UCMODEx **UCSYNC**

![截屏2021-07-13 下午5.55.20](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-13%20%E4%B8%8B%E5%8D%885.55.20.png)

```
//UART模式:异步模式
```

### 2.**UCAxCTL1**，**USCI\_Ax** 控制寄存器 **1**(UCA0CTL1)

位5~1不需要了解

**UCSSELx** _UCRXEIE UCBRKIE UCDORM UCTXADDR UCTXBRK_ **UCSWRST**

![÷](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-13%20%E4%B8%8B%E5%8D%885.59.51.png)

### 3.波特率相关

**UCAxMCTL**，**USCI\_Ax** 调制控制寄存器

UCAxBR0 USCI\_Ax

![截屏2021-07-13 下午6.09.06](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-13%20%E4%B8%8B%E5%8D%886.09.06.png)

**UCAxMCTL**，**USCI\_Ax** 调制控制寄存器

UCBRFx UCBRSx UCOS16

![截屏2021-07-13 下午6.09.42](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-13%20%E4%B8%8B%E5%8D%886.09.42.png)

### 4.状态检测相关

**UCAxSTAT**，**USCI\_Ax** 状态寄存器

UCLISTEN UCFE UCOE UCPE UCBRK UCRXERR UCADDR UCIDLE **UCBUSY**

仅需知:位0

![截屏2021-07-13 下午6.12.55](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-13%20%E4%B8%8B%E5%8D%886.12.55.png)

### 5.内容相关

**UCAxRXBUF**，**USCI\_Ax** 接收缓冲寄存器

​ **UCRXBUFx**

**UCAxTXBUF**，**USCI\_Ax** 发送缓冲寄存器

​ **UCTXBUFx**

**UCAxIRTCTL**，**USCI\_Ax IrDA** 发送缓冲寄存器

​ **UCIRTXPLx UCIRTXCLK**

​ **UCIREN**

**UCAxIRRCTL**，**USCI\_Ax IrDA** 接收控制寄存器

​ \*\*UCIRRXFLx \*\*

​ **UCIRRXPL**

​ **UCIRRXFE**

![截屏2021-07-13 下午6.16.54](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-13%20%E4%B8%8B%E5%8D%886.16.54.png)

### 6.中断相关

## 步骤

### 1.配置系统时钟

串口十分依赖于系统时钟

配置数字控制振荡器为1MHZ

详细参数通过查找msp430g2553.h来查找

​ msp430g2553.h:

```
/************************************************************
* Calibration Data in Info Mem
************************************************************/
```

![截屏2021-07-13 下午6.25.47](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-13%20%E4%B8%8B%E5%8D%886.25.47.png)

#### 查找得矫正的数据

​ msp430g2553.h:

```
SFR_8BIT(CALDCO_1MHZ);                        /* DCOCTL  Calibration Data for 1MHz */
SFR_8BIT(CALBC1_1MHZ);                        /* BCSCTL1 Calibration Data for 1MHz *
```

注意不是:

​ msp430g2553.h:

```
#define CAL_DCO_1MHZ           (0x0008)       /* Index for DCOCTL  Calibration Data for 1MHz */
#define CAL_BC1_1MHZ           (0x0009)       /* Index for BCSCTL1 Calibration Data for 1MHz */
```

#### 配置DCO(内部振荡器)

​ main.c

```
/*配置DCO为1MHz*/
DCOCTL = CALDCO_1MHZ;
BCSCTL1 = CALBC1_1MHZ;
```

#### 配置SMCLK(系统子时钟)为DCO

SELMx DIVMx SELS DIVSx **DCOR**

位三

![截屏2021-07-13 下午6.32.22](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-13%20%E4%B8%8B%E5%8D%886.32.22.png)

```
//默认即为0(DCOCLK),以下代码只是演示用
/*配置SMCLK的时钟源为DCO*/
    BCSCTL2 &= ~SELS;
```

#### 配置SMCLK的分频系数

SELMx DIVMx SELS **DIVSx** DCOR

位2-1

![截屏2021-07-13 下午6.32.22](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-13%20%E4%B8%8B%E5%8D%886.32.22.png)

​ msp430g2553.h:

```
#define DIVS0                  (0x02)         /* SMCLK Divider 0 */
#define DIVS1                  (0x04)         /* SMCLK Divider 1 */
```

​ main.c

```
/*SMCLK的分频系数置为1*/
BCSCTL2 &= ~(DIVS0  DIVS1);
```

### 2.配置串口寄存器

#### 复位

查看:**USCI** 初始化和复位 \\ 2.**UCAxCTL1**，**USCI\_Ax** 控制寄存器 **1**(UCA0CTL1) 位 0

UCSSELx _UCRXEIE UCBRKIE UCDORM UCTXADDR UCTXBRK_ **UCSWRST**

![÷](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-13%20%E4%B8%8B%E5%8D%885.59.51.png)

main.c

```
/*复位USCI_Ax   (置1)*/
UCA0CTL1 = UCSWRST;
```

#### 选择模式

异步模式

查看:**USCI** 初始化和复位 \\ 2.**UCAxCTL1**，**USCI\_Ax** 控制寄存器 **1**(UCA0CTL1) 位 0

​ UCPEN UCPAR UCMSB UC7BIT UCSPB UCMODEx **UCSYNC**

![截屏2021-07-13 下午5.55.20](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-13%20%E4%B8%8B%E5%8D%885.55.20.png)

​ main.c

```
/*选择USCI_Ax为UART模式*/
UCA0CTL0 &= ~UCSYNC;
```

1.奇偶校验

2.MSB

3.字符长度

4.停止位

5.USCI模式

以上五项默认即可

### 3.配置时钟源

查看:**USCI** 初始化和复位 \\ 2.**UCAxCTL1**，**USCI\_Ax** 控制寄存器 **1**(UCA0CTL1) 位 7-6

推荐SMCLK

**UCSSELx** _UCRXEIE UCBRKIE UCDORM UCTXADDR UCTXBRK_ UCSWRST

![÷](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-13%20%E4%B8%8B%E5%8D%885.59.51.png)

```
/*配置UART时钟源为SMCLK*/
UCA0CTL1 = UCSSEL1;
```

### 4.配置波特率

查看:**USCI** 初始化和复位 \\ 波特率相关 15.4.3-4

​ **UCAxBR0**，**USCI\_Ax** 波特率控制寄存器 **0**

​ **UCAxBR1**，**USCI\_Ax** 波特率控制寄存器 **1**

​ **UCBRx**

![截屏2021-07-13 下午6.09.06](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-13%20%E4%B8%8B%E5%8D%886.09.06.png)

寄存器0低8位

寄存器1高8位

查看：额外 \\ 波特率设置 UCOS16=0

![截屏2021-07-13 下午5.46.28](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-13%20%E4%B8%8B%E5%8D%885.46.28.png)

将**UCBRx** 的值(10) - > (16). 例104(10) - > 0x68)(16)。进制转换

UCA0MCTL 根据上图与下图配置UCBRSx

查看:**USCI** 初始化和复位 \\ 波特率相关 \\ 15.4.5. UCA0MCTL

​ UCBRFx UCBRSx UCOS16

![截屏2021-07-13 下午6.09.42](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-13%20%E4%B8%8B%E5%8D%886.09.42.png)

​ main.c

```
/*配置波特率为9600@1MHz*/
    UCA0BR0 = 0x68;
    UCA0BR1 = 0x00;//可由低8位表示,无需高8位,故置0
    UCA0MCTL = 1 << 1;//“1”左移1位//UCBRSx
```

### 5.配置端口

![截屏2021-07-13 下午7.25.49](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-13%20%E4%B8%8B%E5%8D%887.25.49.png)

​ main.c

```
   /*使能端口复用,配置端口*/
    P1SEL = BIT1 + BIT2;
    P1SEL2 = BIT1 + BIT2;
```

### 6.软件清除

查看: 步骤 \\ 2.配置串口寄存器 \\ 复位 将其置0

```
/*清除复位位，使能UART*/
UCA0CTL1 &= ~UCSWRST;
```

### 7.配置内容

**USCI** 初始化和复位 \\ 5.内容相关 \\15.4.8

![截屏2021-07-16 下午5.31.24](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-16%20%E4%B8%8B%E5%8D%885.31.24.png)

​ main.c

```
UCA0TXBUF = 0xAA;
```

并设置延时防止发送数据出错

​ main.c

```
__delay_cycles(500000);//500ms
```

## Uart\_SendChar

```
#include <msp430.h> 
/*
 * main.c
 */
int main(void)
{
    WDTCTL = WDTPW  WDTHOLD;    // Stop watchdog timer
    /*配置DCO为1MHz*/
    DCOCTL = CALDCO_1MHZ;
    BCSCTL1 = CALBC1_1MHZ;
    /*配置SMCLK的时钟源为DCO*/
    BCSCTL2 &= ~SELS;
    /*SMCLK的分频系数置为1*/
    BCSCTL2 &= ~(DIVS0  DIVS1);

    /*复位USCI_Ax*/
    UCA0CTL1 = UCSWRST;

    /*选择USCI_Ax为UART模式*/
    UCA0CTL0 &= ~UCSYNC;

    /*配置UART时钟源为SMCLK*/
    UCA0CTL1 = UCSSEL1;

    /*配置波特率为9600@1MHz*/
    UCA0BR0 = 0x68;
    UCA0BR1 = 0x00;
    UCA0MCTL = 1 << 1;
    /*使能端口复用,配置端口*/
    P1SEL = BIT1 + BIT2;
    P1SEL2 = BIT1 + BIT2;
    /*清除复位位，使能UART*/
    UCA0CTL1 &= ~UCSWRST;

    while(1)
    {
        UCA0TXBUF = 0xAA;
        __delay_cycles(500000);
    }
    return 0;
}
```

## PS.

使用虚拟串口需要拔下RXDTXD处左两个跳帽,并横插

![截屏2021-07-13 下午7.43.54](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-13%20%E4%B8%8B%E5%8D%887.43.54.png)

# 串口调试

## 判断发送状态

查看:串口配置\\USCI初始化和复位\\4.状态监测相关

UCLISTEN UCFE UCOE UCPE UCBRK UCRXERR UCADDR UCIDLE **UCBUSY**

位0

![截屏2021-07-13 下午6.12.55](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-13%20%E4%B8%8B%E5%8D%886.12.55.png)

```
UCA0STAT & UCBUSY
```

## Uart\_Print

​ 将Uart\_SendChar部分代码封装为函数,使代码更为简洁

```
#include <msp430.h> 
#include "stdint.h"
/*
 * @fn:        void InitSystemClock(void)
 * @brief:    初始化系统时钟
 * @para:    none
 * @return:    none
 * @comment:初始化系统时钟
 */
void InitSystemClock(void)
{
    /*配置DCO为1MHz*/
    DCOCTL = CALDCO_1MHZ;
    BCSCTL1 = CALBC1_1MHZ;
    /*配置SMCLK的时钟源为DCO*/
    BCSCTL2 &= ~SELS;
    /*SMCLK的分频系数置为1*/
    BCSCTL2 &= ~(DIVS0  DIVS1);
}
/*
 * @fn:        void InitUART(void)
 * @brief:    初始化串口，包括设置波特率，数据位，校验位等
 * @para:    none
 * @return:    none
 * @comment:初始化串口
 */
void InitUART(void)
{
    /*复位USCI_Ax*/
    UCA0CTL1 = UCSWRST;

    /*选择USCI_Ax为UART模式*/
    UCA0CTL0 &= ~UCSYNC;

    /*配置UART时钟源为SMCLK*/
    UCA0CTL1 = UCSSEL1;

    /*配置波特率为9600@1MHz*/
    UCA0BR0 = 0x68;
    UCA0BR1 = 0x00;
    UCA0MCTL = 1 << 1;
    /*使能端口复用*/
    P1SEL = BIT1 + BIT2;
    P1SEL2 = BIT1 + BIT2;
    /*清除复位位，使能UART*/
    UCA0CTL1 &= ~UCSWRST;
}
/*
 * @fn:        void UARTSendString(uint8_t *pbuff,uint8_t num)
 * @brief:    通过串口发送字符串
 * @para:    pbuff:指向要发送字符串的指针
 *             num:要发送的字符个数
 * @return:    none
 * @comment:通过串口发送字符串
 */
void UARTSendString(uint8_t *pbuff,uint8_t num)
{
    uint8_t cnt = 0;
    for(cnt = 0;cnt < num;cnt ++)
    {
        while(UCA0STAT & UCBUSY);
        //__delay_cycles(5000);
        UCA0TXBUF = *(pbuff + cnt);
    }
}
/*
 * @fn:        void PrintNumber(uint16_t num)
 * @brief:    通过串口发送数字
 * @para:    num:变量
 * @return:    none
 * @comment:通过串口发送数字
 */
void PrintNumber(uint16_t num)
{
    uint8_t buff[6] = {0,0,0,0,0,'\n'};
    uint8_t cnt = 0;
    for(cnt = 0;cnt < 5;cnt ++)
    {
        buff[4 - cnt] = (uint8_t)(num % 10 + '0');
        num /= 10;
    }
    UARTSendString(buff,6);
}
/*
 * main.c
 */
int main(void)
{
    WDTCTL = WDTPW  WDTHOLD;    // Stop watchdog timer

    InitSystemClock();
    InitUART();

    while(1)
    {
        PrintNumber(32415);
      //UARTSendString(“ASDFGH”,6);
        __delay_cycles(500000);
    }
    return 0;
}
```

# 定时器 A

## 定时器A的四种计数模式

![截屏2021-07-15 下午8.13.06](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-15%20%E4%B8%8B%E5%8D%888.13.06.png)

### 1.停止模式

### 2.向上模式

![截屏2021-07-15 下午8.15.19](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-15%20%E4%B8%8B%E5%8D%888.15.19.png)

![截屏2021-07-15 下午8.40.15](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-15%20%E4%B8%8B%E5%8D%888.40.15.png)

![截屏2021-07-15 下午8.40.34](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-15%20%E4%B8%8B%E5%8D%888.40.34.png)

其中TACCR0的值由用户自己设定,且与0FFFFh不同

### 3连续模式

![截屏2021-07-15 下午8.17.28](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-15%20%E4%B8%8B%E5%8D%888.17.28.png)

![截屏2021-07-15 下午8.40.54](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-15%20%E4%B8%8B%E5%8D%888.40.54.png)

![截屏2021-07-15 下午8.41.52](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-15%20%E4%B8%8B%E5%8D%888.41.52.png)

### 4增加减少模式

![截屏2021-07-15 下午8.18.59](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-15%20%E4%B8%8B%E5%8D%888.18.59.png)

![截屏2021-07-15 下午8.42.21](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-15%20%E4%B8%8B%E5%8D%888.42.21.png)

![截屏2021-07-15 下午8.42.39](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-15%20%E4%B8%8B%E5%8D%888.42.39.png)

TACCR0由用户设置

## 定时器A的两种工作模式

### 1.捕捉模式

应用:时间确定

![截屏2021-07-15 下午8.29.39](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-15%20%E4%B8%8B%E5%8D%888.29.39.png)

![截屏2021-07-15 下午8.30.00](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-15%20%E4%B8%8B%E5%8D%888.30.00.png)

### 2.比较模式

TAR与TACCRx比较,二者相等时,中断标志置位,若开启了中断.将会执行中断服务函数

![截屏2021-07-15 下午8.30.39](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-15%20%E4%B8%8B%E5%8D%888.30.39.png)

## 用途

借助四种计数模式与两种工作模式可实现以下功能

### 1.定时

准确的时间间隔

### 2.产生PWM波

脉冲宽度调制

### 3.测量频率

测量信号间的时间间隔

## 定时器A的定时功能

以向上计数模式为例

![截屏2021-07-15 下午8.15.19](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-15%20%E4%B8%8B%E5%8D%888.15.19.png)

![截屏2021-07-15 下午8.40.15](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-15%20%E4%B8%8B%E5%8D%888.40.15-20210716185725816.png)

![截屏2021-07-15 下午8.40.34](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-15%20%E4%B8%8B%E5%8D%888.40.34-20210716185717748.png)

CCR0->0h时会置位TAIFG标志

CCR0-1->CCR0时会置位CCIFG标志位

#### 步骤

##### 1.配置DCO（内部振荡器）

查看：串口配置\\步骤\\1.配置系统时钟

```
/*配置DCO为1MHz*/
DCOCTL = CALDCO_1MHZ;
BCSCTL1 = CALBC1_1MHZ;
```

##### 2.设置定时器时钟源

**TACTL** TASSELx IDx MCx TACLRx TAIE TAIFG

![截屏2021-07-16 下午3.16.06](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-16%20%E4%B8%8B%E5%8D%883.16.06.png)

以SMCLK为例

位9-8

```
/*设置时钟源为SMCLK*/
    TA1CTL = TASSEL_2;//TASSEL_2可从msp430g2553.h文件查看
```

![image-20210716153534498](https://kozakemi.oss-cn-beijing.aliyuncs.com/image-20210716153534498.png)

##### 3.设置计数模式

位5-4

```
/*设置工作模式为Up Mode*/
    TA1CTL = MC_1;//同在msp430g2553.h文件
```

##### 4.设置工作模式

默认比较模式

##### 5.设置定时间隔

设定TA1CCR0的值（上数模式）

```
/*设置定时间隔*/
    TA1CCR0 = 49999;//1MHz 计 50000个数，1/1MHz * 50000 20Hz 0.05s  T=TA1CCR0/f
```

如果使用TAIFG则TA1CCR0 = 49999，如果使用 CCIFG则TA1CCR0 = 50000

##### 6.确定运行1周期

位0

```
TA1CTL & TAIFG
```

判断完成后清除标识位

```
TA1CTL &= ~TAIFG;
```

##### P.S.

连续模式默认记65536个数，不可自定义，一般不用

增加减少模式到达TACCR0时产生标识位CCIFG，到达0时产生标识位 TAIFG（计数周期变为两倍向上在向下计数）

### Timer\_1S

由于始终检测标志位，复杂的程序可能出现计时错误

```
#include <msp430.h> 
#include "stdint.h"
/*
 * main.c
 */
int main(void)
{
    uint8_t cnt = 0;
    WDTCTL = WDTPW  WDTHOLD;    // Stop watchdog timer
    /*配置DCO频率为1MHz*/
    DCOCTL = CALDCO_1MHZ;
    BCSCTL1 = CALBC1_1MHZ;
    /*设置P1.6为输出*/
    P1DIR = BIT6;

    /*设置时钟源为SMCLK*/
    TA1CTL = TASSEL_2;
    /*设置工作模式为Up Mode*/
    TA1CTL = MC_1;//Up Mode模式下，16bit Timer计数器 TAR会从0递增到TACCR0，然后回到0，重复这个过程
    /*设置定时间隔*/
    TA1CCR0 = 49999;//1MHz 计 50000个数，1/1MHz * 50000 20Hz 0.05s
    while(1)
    {
        if(TA1CTL & TAIFG)
        {
            cnt ++;
            TA1CTL &= ~TAIFG;
            if(cnt == 20)//0.05s * 20 = 1s
            {
                P1OUT ^= BIT6;
                cnt = 0;
            }
        }
    }
    return 0;
}
```

### 解决：

使用中断法解决上程序的缺陷

TACTL TASSELx IDx MCx TACLRx **TAIE** TAIFG

![截屏2021-07-16 下午3.16.06](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-16%20%E4%B8%8B%E5%8D%883.16.06.png)

位1

将TAIE位置1

启用TAIFG中断

```
TA1CTL =TAIE;//可理解为计时器的溢出中断
```

打开全局中断**GIE**

```
__bis_SR_register(GIE);
```

**TAIV**

![截屏2021-07-16 下午4.46.41](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-16%20%E4%B8%8B%E5%8D%884.46.41.png)

### Uart\_Clock

```
#include <msp430.h> 
#include "stdint.h"

uint32_t currenttime = 40500;//用来保存时间的变量，初值代表11:15:00
/*
 * @fn:        void InitSystemClock(void)
 * @brief:    初始化系统时钟
 * @para:    none
 * @return:    none
 * @comment:初始化系统时钟
 */
void InitSystemClock(void)
{
    /*配置DCO为1MHz*/
    DCOCTL = CALDCO_1MHZ;
    BCSCTL1 = CALBC1_1MHZ;
    /*配置SMCLK的时钟源为DCO*/
    BCSCTL2 &= ~SELS;
    /*SMCLK的分频系数置为1*/
    BCSCTL2 &= ~(DIVS0  DIVS1);
}
/*
 * @fn:        void InitUART(void)
 * @brief:    初始化串口，包括设置波特率，数据位，校验位等
 * @para:    none
 * @return:    none
 * @comment:初始化串口
 */
void InitUART(void)
{
    /*复位USCI_Ax*/
    UCA0CTL1 = UCSWRST;

    /*选择USCI_Ax为UART模式*/
    UCA0CTL0 &= ~UCSYNC;

    /*配置UART时钟源为SMCLK*/
    UCA0CTL1 = UCSSEL1;

    /*配置波特率为9600@1MHz*/
    UCA0BR0 = 0x68;
    UCA0BR1 = 0x00;
    UCA0MCTL = 1 << 1;
    /*使能端口复用*/
    P1SEL = BIT1 + BIT2;
    P1SEL2 = BIT1 + BIT2;
    /*清除复位位，使能UART*/
    UCA0CTL1 &= ~UCSWRST;
}
/*
 * @fn:        void UARTSendString(uint8_t *pbuff,uint8_t num)
 * @brief:    通过串口发送字符串
 * @para:    pbuff:指向要发送字符串的指针
 *             num:要发送的字符个数
 * @return:    none
 * @comment:通过串口发送字符串
 */
void UARTSendString(uint8_t *pbuff,uint8_t num)
{
    uint8_t cnt = 0;
    for(cnt = 0;cnt < num;cnt ++)
    {
        while(UCA0STAT & UCBUSY);
        UCA0TXBUF = *(pbuff + cnt);
    }
}
/*
 * @fn:        void PrintTime(uint32_t time)
 * @brief:    通过串口发送当前时间
 * @para:    time:当前时间
 * @return:    none
 * @comment:通过串口发送当前时间
 */
void PrintTime(uint32_t time)
{
    uint8_t charbuff[] = {0,0,':',0,0,':',0,0};
    charbuff[7] = (uint8_t)((time % 60) % 10) + '0';//得到当前秒个位
    charbuff[6] = (uint8_t)((time % 60) / 10) + '0';//得到当前秒十位
    charbuff[4] = (uint8_t)((time % 3600) / 60 % 10) + '0';//得到当前分个位
    charbuff[3] = (uint8_t)((time % 3600) / 60 / 10) + '0';//得到当前分十位
    charbuff[1] = (uint8_t)((time / 3600) % 10) + '0';//得到当前时个位
    charbuff[0] = (uint8_t)(time / 3600 / 10) + '0';//得到当前时十位
    UARTSendString("当前时间：",10);
    UARTSendString(charbuff,8);
}
/*
 * main.c
 */
uint8_t flag = 0;

int main(void)
{
    WDTCTL = WDTPW  WDTHOLD;    // Stop watchdog timer
    InitSystemClock();
    InitUART();
    /*设置P1.6为输出*/
    P1DIR = BIT6;

    /*设置时钟源为SMCLK*/
    TA1CTL = TASSEL_2;
    /*设置工作模式为Up Mode*/
    TA1CTL = MC_1;
    /*设置定时间隔*/
    TA1CCR0 = 49999;// 50ms 1MHz 1/1MHz 1ns 50ms / 1ns = 50000 50000 - 1 = 49999

    /*开启TAIFG中断*/
    TA1CTL = TAIE;
    /*打开全局中断*/
    __bis_SR_register(GIE);
    while(1)
    {
        if(flag == 1)
        {
            flag = 0;
            P1OUT ^= BIT6;
            PrintTime(currenttime);
        }
    }
    return 0;
}

#pragma vector = TIMER1_A1_VECTOR
__interrupt void Time_Tick(void)
{
    static uint8_t cnt = 0;
    switch(TA1IV)//TAIV自动清除，TAIV只读
    {
    case 0x02://捕捉/比较寄存器1
        break;
    case 0x04://捕捉/比较寄存器2
        break;
    case 0x0A://定时器A溢出
        cnt ++;
        if(cnt == 20)
        {
            cnt = 0;//清零计数器
            flag = 1;//1s 到了
            currenttime ++;//时间加1
            currenttime %= 86400;//一天24小时，防止超出
        }
        break;
    default:
        break;
    }
}
```

## 定时器A产生PWM波

PWM波：矩形波 可改变占空比，来实现各种功能例如电机的调速，灯光亮度的控制

使用定时器A的比较工作模式

![截屏2021-07-13 下午5.30.15](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-13%20%E4%B8%8B%E5%8D%885.30.15.png)

![截屏2021-07-15 下午8.30.39](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-15%20%E4%B8%8B%E5%8D%888.30.39.png)

内部信号通过TA0.0-TA1.2输出

![截屏2021-07-16 下午5.45.25](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-16%20%E4%B8%8B%E5%8D%885.45.25.png)

![截屏2021-07-16 下午5.45.51](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-16%20%E4%B8%8B%E5%8D%885.45.51.png)

### 向上模式

![截屏2021-07-16 下午5.45.41](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-16%20%E4%B8%8B%E5%8D%885.46.15.png)

Output Mode 1: Set 复位

Output Mode 2:Toggle/Reset 翻转/复位

Output Mode 3: Set/Reset 置位/复位

Output Mode 4:Toggle 切换

Output Mode 5: Reset 复位

Output Mode 6:Toggle/Set 切换/置位

Output Mode 7: Reset/Set 复位/置位

捕捉/比较寄存器，TACCR1做捕捉/比较寄存器，所以从TA1.1输出，TACCR2则TA1.2输出

TACCR0为最大值无法输出

2、3、6、7产生PWM波形

### 连续模式

![截屏2021-07-16 下午6.30.24](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-16%20%E4%B8%8B%E5%8D%886.30.24.png)

![截屏2021-07-16 下午6.30.57](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-16%20%E4%B8%8B%E5%8D%886.30.57.png)

### 增加减少模式

![截屏2021-07-16 下午6.32.06](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-16%20%E4%B8%8B%E5%8D%886.32.06.png)

![截屏2021-07-16 下午6.48.03](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-16%20%E4%B8%8B%E5%8D%886.48.03.png)

![截屏2021-07-16 下午6.48.25](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-16%20%E4%B8%8B%E5%8D%886.48.25.png)

![截屏2021-07-16 下午6.49.45](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-16%20%E4%B8%8B%E5%8D%886.49.45.png)

![截屏2021-07-16 下午6.50.04](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-16%20%E4%B8%8B%E5%8D%886.50.04.png)

![截屏2021-07-16 下午6.51.10](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-16%20%E4%B8%8B%E5%8D%886.51.10.png)

### 步骤

1.配置时钟

2.配置工作模式

### Breath\_LED

使用跳帽**连接P1.6,P2.5**，并注意在代码中设置P1.6为输入

```
#include <msp430.h> 

/*
 * main.c
 */
int main(void)
{
    unsigned int cnt = 0;
    WDTCTL = WDTPW  WDTHOLD;    // Stop watchdog timer
    /*配置DCO频率为1MHz*/
    DCOCTL = CALDCO_1MHZ;
    BCSCTL1 = CALBC1_1MHZ;
    /*初始化P1.6为输入*/
    P1DIR &= ~BIT6;//否则会可能会有短路
    /*设置时钟源为SMCLK*/
    TA1CTL = TASSEL1;
    /*设置工作模式为Up&Down*/
    TA1CTL = MC0MC1;
    /*设置TA1CCR0为0x00FF*/
    TA1CCR0 = 0x00FF;
    /*设置TA1CCR2为0x00FF*/
    TA1CCR2 = 0x00FF;//占空比(TACCR0 - TACCR2) / TACCR0,频率=SMCLK/(TACCR0+1)/2
    /*设置为比较模式*/
    TA1CCTL0 &= ~CAP;
    TA1CCTL2 &= ~CAP;
    /*设置比较输出模式*/
    TA1CCTL2 = OUTMOD_6;//Output Mode 6:Toggle/Set         切换/置位
    /*设置IO复用*/
    P2SEL = BIT5;
    P2DIR = BIT5;
    while(1)
    {
        for(cnt = 0;cnt < 0x00FF;cnt ++)
        {
            TA1CCR2 = cnt;
            __delay_cycles(5000);
        }
        for(cnt = 0x00FF;cnt > 0;cnt --)
        {
            TA1CCR2 = cnt;
            __delay_cycles(5000);
        }
    }
    return 0;
}
```

## 定时器A测定频率（输入捕捉功能）

![截屏2021-07-17 上午8.55.05](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-17%20%E4%B8%8A%E5%8D%888.55.05.png)

![截屏2021-07-17 上午8.58.10](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-17%20%E4%B8%8A%E5%8D%888.58.10.png)  
**CMx**位15-14

**CMx** CCISx SCS SCCI CAP OUTMOD CCIE CCI OUT COV CCIFG

![截屏2021-07-17 上午9.21.14](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-17%20%E4%B8%8A%E5%8D%889.21.14.png)

从何处输入？

![截屏2021-07-13 下午5.30.15](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-13%20%E4%B8%8B%E5%8D%885.30.15.png)

引脚功能表中有**CCIxA**字样说明可以可作为定时器A输入捕捉比较端口

例：P1.1

![截屏2021-07-17 上午9.50.19](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-17%20%E4%B8%8A%E5%8D%889.50.19.png)

![截屏2021-07-17 上午9.49.38](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-17%20%E4%B8%8A%E5%8D%889.49.38.png)

P1.1 作为定时器0输入捕捉比较端口

P2.5:

![截屏2021-07-16 下午6.51.10](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-16%20%E4%B8%8B%E5%8D%886.51.10.png)

```
/*P2.5作为捕捉输入(CCI2B)*/
    TA1CCTL2 = CCIS0;
    P2SEL = BIT5;
```

**CAP** 位8

![截屏2021-07-17 下午3.17.21](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-17%20%E4%B8%8B%E5%8D%883.17.21.png)

CMx CCISx SCS SCCI CAP OUTMOD CCIE CCI OUT COV CCIF

![截屏2021-07-17 上午9.21.14](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-17%20%E4%B8%8A%E5%8D%889.21.14-20210717153139162-20210717153238583.png)

**CMx**选择捕捉上升沿/下降沿/两者

**CCISx**位13-12

**CMx** **CCISx** SCS SCCI CAP OUTMOD CCIE CCI OUT COV CCIF

![截屏2021-07-17 上午9.21.14](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-17%20%E4%B8%8A%E5%8D%889.21.14-20210717153139162.png)

### Freq\_Meter

```
#include <msp430.h> 
#include "stdint.h"
#include <stdio.h>
uint32_t timestamp = 0;//时间戳
uint16_t capvalue_1 = 0;//第一次捕捉值
uint16_t capvalue_2 = 0;//第二次捕捉值
uint32_t timestamp_1 = 0;//第一次时间戳
uint32_t timestamp_2 = 0;//第二次时间戳
uint32_t totaltime = 0;
float freq = 0;
/*
 * @fn:        void InitSystemClock(void)
 * @brief:    初始化系统时钟
 * @para:    none
 * @return:    none
 * @comment:初始化系统时钟
 */
void InitSystemClock(void)
{
    /*配置DCO为1MHz*/
    DCOCTL = CALDCO_1MHZ;
    BCSCTL1 = CALBC1_1MHZ;
    /*配置SMCLK的时钟源为DCO*/
    BCSCTL2 &= ~SELS;
    /*SMCLK的分频系数置为1*/
    BCSCTL2 &= ~(DIVS0  DIVS1);
}
/*
 * @fn:        void InitUART(void)
 * @brief:    初始化串口，包括设置波特率，数据位，校验位等
 * @para:    none
 * @return:    none
 * @comment:初始化串口
 */
void InitUART(void)
{
    /*复位USCI_Ax*/
    UCA0CTL1 = UCSWRST;

    /*选择USCI_Ax为UART模式*/
    UCA0CTL0 &= ~UCSYNC;

    /*配置UART时钟源为SMCLK*/
    UCA0CTL1 = UCSSEL1;

    /*配置波特率为9600@1MHz*/
    UCA0BR0 = 0x68;
    UCA0BR1 = 0x00;
    UCA0MCTL = 1 << 1;
    /*使能端口复用*/
    P1SEL = BIT1 + BIT2;
    P1SEL2 = BIT1 + BIT2;
    /*清除复位位，使能UART*/
    UCA0CTL1 &= ~UCSWRST;
}
/*
 * @fn:        void UARTSendString(uint8_t *pbuff,uint8_t num)
 * @brief:    通过串口发送字符串
 * @para:    pbuff:指向要发送字符串的指针
 *             num:要发送的字符个数
 * @return:    none
 * @comment:通过串口发送字符串
 */
void UARTSendString(uint8_t *pbuff,uint8_t num)
{
    uint8_t cnt = 0;
    for(cnt = 0;cnt < num;cnt ++)
    {
        while(UCA0STAT & UCBUSY);
        UCA0TXBUF = *(pbuff + cnt);
    }
}
/*
 * @fn:        void UARTPrint(uint8_t *pbuff)
 * @brief:    通过串口发送字符串
 * @para:    pbuff:指向要发送字符串的指针
 * @return:    none
 * @comment:通过串口发送字符串,遇到'\0'停止发送
 */
void UARTPrint(uint8_t *pbuff)
{
    uint8_t cnt = 0;
    while(*(pbuff + cnt) != '\0')
    {
        while(UCA0STAT & UCBUSY);
        UCA0TXBUF = *(pbuff + cnt);
        cnt ++;
    }
}
/*
 * @fn:        void PrintFreq(float freq)
 * @brief:    通过串口发送频率
 * @para:    freq:频率
 * @return:    none
 * @comment:通过串口发送频率
 */
void PrintFreq(float freq)
{
    uint32_t temp = (uint32_t)(freq * 1000);
    uint8_t charbuff[] = {0,0,0,0,0,0,0,0,0};//最大999999.999Hz
    int8_t cnt = 0;
    for(cnt = 8;cnt >= 0;cnt --)
    {
        charbuff[cnt] = (uint8_t)(temp % 10) + '0';
        temp /= 10;
    }
    UARTSendString("频率为：",8);
    UARTSendString(charbuff,6);
    UARTSendString(".",1);
    UARTSendString(charbuff + 6,3);
    UARTSendString("Hz",2);
}
/*
 * main.c
 */

int main(void)
{
    WDTCTL = WDTPW  WDTHOLD;    // Stop watchdog timer
    InitSystemClock();
    InitUART();

    /*设置时钟源为SMCLK*/
    TA1CTL = TASSEL_2;
    /*设置工作模式为Up Mode*/
    TA1CTL = MC_1;
    /*设置定时间隔*/
    TA1CCR0 = 49999;//50ms
    /*开启TAIFG中断*/
    TA1CTL = TAIE;

    /*TA1,CCR2用于捕捉功能*/
    TA1CCTL2 = CAP;
    /*上升沿捕捉*/
    TA1CCTL2 = CM0;
    /*P2.5作为捕捉输入(CCI2B)*/
    TA1CCTL2 = CCIS0;
    P2SEL = BIT5;
    /*允许捕捉比较中断*/
    TA1CCTL2 = CCIE;

    /*设置时钟源为SMCLK*/
    TA0CTL = TASSEL1;
    /*设置工作模式为Up&Down*/
    TA0CTL = MC0MC1;
    /*设置TA0CCR0为0x00FF*/
    TA0CCR0 = 0x0AAA;
    /*设置TA0CCR1为0x00FF*/
    TA0CCR1 = 0x0555;//占空比(TACCR0 - TACCR1) / TACCR0,频率=SMCLK/(TACCR0)/2
    /*设置为比较模式*/
    TA0CCTL0 &= ~CAP;
    TA0CCTL1 &= ~CAP;
    /*设置比较输出模式*/
    TA0CCTL1 = OUTMOD_6;
    /*设置IO复用*/
    P1SEL = BIT6;
    P1DIR = BIT6;//P1.6生成PWM波


    /*打开全局中断*/
    __bis_SR_register(GIE);
    while(1)
    {
        __delay_cycles(500000);
        freq = (float)(1000000.0) / totaltime;
        PrintFreq(freq);
    }
    return 0;
}

#pragma vector = TIMER1_A1_VECTOR
__interrupt void Time_Tick(void)
{
    static uint8_t cnt = 0;
    __bis_SR_register(GIE);//允许中断嵌套
    switch(TA1IV)
    {
    case 0x02://捕捉比较中断1
        break;
    case 0x04://捕捉比较中断2
        if(cnt == 0)
        {
            capvalue_1 = TA1CCR2;//保存第一次捕捉值
            timestamp_1 = timestamp;//保存第一次时间戳
            cnt ++;
        }
        else
        {
            capvalue_2 = TA1CCR2;//保存第二次捕捉值
            timestamp_2 = timestamp;//保存第二次时间戳
            cnt = 0;
            totaltime = (timestamp_2 - timestamp_1) * 50000 + capvalue_2 - capvalue_1;//计算总时间
        }
        break;
    case 0x0A://溢出中断
        timestamp ++;
        break;
    default:
        break;
    }
}

//定时器1的0通道做溢出中断
//定时器1的2通道做捕捉功能
//定时器0产生PWM波P1.6
//定时器1测频率P2.5
```

# ADC10

![截屏2021-07-13 下午5.30.15](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-13%20%E4%B8%8B%E5%8D%885.30.15.png)

A0，A1，A2，A3，A4，A5，A6，A7

**分辨率**：十位

**基准电压**：外部

​ 内部

## 步骤

### 1.设置时钟源

ADC需要时钟驱动

```
/*设置ADC时钟MCLK*/
      ADC10CTL1 = ADC10SSEL_2;
```

### 2.设置分频系数

```
/*ADC 2分频*/
      ADC10CTL1 = ADC10DIV_0;
```

### 3.设置基准源

位15-13

**SREFx** ADC10SHTx ADC10SR REFOUT REFBURST MSC REF2\_5V REFON ADC10ON ADC10IE

![截屏2021-07-19 上午9.43.37](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-19%20%E4%B8%8A%E5%8D%889.43.37.png)

一般用001

VR+：ADC基准电压的正极

VR-：ADC基准电压的负极

VREF+：内部基准电压

VeREF+：外部基准电压

[百度知道](https://zhidao.baidu.com/question/293263013.html)

```
/*设置ADC基准源*/
      ADC10CTL0 = SREF_1;//内部基准源
```

### 4.设置采样保持时间（一般无需修改）

```
/*设置ADC采样保持时间64CLK*/
      ADC10CTL0 = ADC10SHT_3;
```

### 5.设置采样率（一般无需修改）

```
/*设置ADC采样率200k*/
      ADC10CTL0 &= ~ADC10SR;
```

### 6.设置基准电压

```
/*ADC基准选择2.5V*/
      ADC10CTL0 = REF2_5V;
```

```
/*ADC基准选择1.5V*/
      ADC10CTL0 &= ～REF2_5V;
```

### 7.开启基准

位5

SREFx ADC10SHTx ADC10SR REFOUT REFBURST MSC REF2\_5V REFON ADC10ON **ADC10IE**

![截屏2021-07-19 上午9.43.37](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-19%20%E4%B8%8A%E5%8D%889.43.37.png)

```
/*开启基准*/
      ADC10CTL0 = REFON;
```

### 8.设置ADC输入IO 「一般只改此项」

```
/*选择ADC输入通道A0*/
      ADC10CTL1 = INCH_0;
```

### 9.开启通道对应的模拟输入

```
/*允许A0模拟输入*/
      ADC10AE0 = 0x0001;
```

### 10.开启ADC总开关

```
/*开启ADC*/
      ADC10CTL0 = ADC10ON;
```

### 11.将模拟转化为数字信号

ADC10IFG **ENC ADC10SC**

位1-0

注意：位0转换完成后会自动清零 即只转换1次

![截屏2021-07-19 上午10.10.57](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-19%20%E4%B8%8A%E5%8D%8810.10.57.png)

**ADC10BUSY**

位0

INCHx SHSx ADC10DF ISSH ADC10DIVx ADC10SSELx CONSEQx **ADC10BUSY**

![截屏2021-07-19 上午10.14.30](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-19%20%E4%B8%8A%E5%8D%8810.14.30.png)

![截屏2021-07-19 上午10.15.18](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-19%20%E4%B8%8A%E5%8D%8810.15.18.png)

**ADC10MEM**

![截屏2021-07-19 上午10.16.44](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-19%20%E4%B8%8A%E5%8D%8810.16.44.png)

```
/*
 * @fn:        uint16_t GetADCValue(void)
 * @brief:    进行一次ADC转换并返回ADC转换结果
 * @para:    none
 * @return:    ADC转换结果
 * @comment:ADC转换结果为10bit，以uint16_t类型返回，低10位为有效数据
 */
uint16_t GetADCValue(void)
{
      /*开始转换*/
      ADC10CTL0 = ADC10SCENC;
      /*等待转换完成*/
      while(ADC10CTL1&ADC10BUSY);
      /*返回结果*/
      return ADC10MEM;
}
```

## 函数void InitADC(void) 「1-10」

```
/*
 * @fn:        void InitADC(void)
 * @brief:    初始化ADC
 * @para:    none
 * @return:    none
 * @comment:初始化ADC
 */
void InitADC(void)
{
      /*设置ADC时钟MCLK*/
      ADC10CTL1 = ADC10SSEL_2;
      /*ADC 2分频*/
      ADC10CTL1 = ADC10DIV_0;
      /*设置ADC基准源*/
      ADC10CTL0 = SREF_1;
      /*设置ADC采样保持时间64CLK*/
      ADC10CTL0 = ADC10SHT_3;
      /*设置ADC采样率200k*/
      ADC10CTL0 &= ~ADC10SR;
      /*ADC基准选择2.5V*/
      ADC10CTL0 = REF2_5V;
      /*开启基准*/
      ADC10CTL0 = REFON;
      /*选择ADC输入通道A0*/
      ADC10CTL1 = INCH_0;//一般仅修改通道相关
      /*允许A0模拟输入*/
      ADC10AE0 = 0x0001;
      /*开启ADC*/
      ADC10CTL0 = ADC10ON;
}
```

## Volt\_Meter

```
#include <msp430.h> 
#include "stdint.h"
/*
 * @fn:        void InitSystemClock(void)
 * @brief:    初始化系统时钟
 * @para:    none
 * @return:    none
 * @comment:初始化系统时钟
 */
void InitSystemClock(void)
{
    /*配置DCO为1MHz*/
    DCOCTL = CALDCO_1MHZ;
    BCSCTL1 = CALBC1_1MHZ;
    /*配置SMCLK的时钟源为DCO*/
    BCSCTL2 &= ~SELS;
    /*SMCLK的分频系数置为1*/
    BCSCTL2 &= ~(DIVS0  DIVS1);
}
/*
 * @fn:        void InitUART(void)
 * @brief:    初始化串口，包括设置波特率，数据位，校验位等
 * @para:    none
 * @return:    none
 * @comment:初始化串口
 */
void InitUART(void)
{
    /*复位USCI_Ax*/
    UCA0CTL1 = UCSWRST;

    /*选择USCI_Ax为UART模式*/
    UCA0CTL0 &= ~UCSYNC;

    /*配置UART时钟源为SMCLK*/
    UCA0CTL1 = UCSSEL1;

    /*配置波特率为9600@1MHz*/
    UCA0BR0 = 0x68;
    UCA0BR1 = 0x00;
    UCA0MCTL = 1 << 1;
    /*使能端口复用*/
    P1SEL = BIT1 + BIT2;
    P1SEL2 = BIT1 + BIT2;
    /*清除复位位，使能UART*/
    UCA0CTL1 &= ~UCSWRST;
}
/*
 * @fn:        void UARTSendString(uint8_t *pbuff,uint8_t num)
 * @brief:    通过串口发送字符串
 * @para:    pbuff:指向要发送字符串的指针
 *             num:要发送的字符个数
 * @return:    none
 * @comment:通过串口发送字符串
 */
void UARTSendString(uint8_t *pbuff,uint8_t num)
{
    uint8_t cnt = 0;
    for(cnt = 0;cnt < num;cnt ++)
    {
        while(UCA0STAT & UCBUSY);
        UCA0TXBUF = *(pbuff + cnt);
    }
}
/*
 * @fn:        void PrintNumber(uint16_t num)
 * @brief:    通过串口发送数字
 * @para:    num:变量
 * @return:    none
 * @comment:通过串口发送数字
 */
void PrintNumber(uint16_t num)
{
    uint8_t buff[6] = {0,0,0,0,0,'\n'};
    uint8_t cnt = 0;
    for(cnt = 0;cnt < 5;cnt ++)
    {
        buff[4 - cnt] = (uint8_t)(num % 10 + '0');
        num /= 10;
    }
    UARTSendString(buff,6);
}
/*
 * @fn:        void PrintFloat(float num)
 * @brief:    通过串口发送浮点数
 * @para:    num:变量
 * @return:    none
 * @comment:通过串口发送浮点数，可发送1位整数位+3位小数位
 */
void PrintFloat(float num)
{
    uint8_t charbuff[] = {0,'.',0,0,0};
    uint16_t temp = (uint16_t)(num * 1000);
    charbuff[0] = (uint8_t)(temp / 1000) + '0';
    charbuff[2] = (uint8_t)((temp % 1000) / 100) + '0';
    charbuff[3] = (uint8_t)((temp % 100) / 10) + '0';
    charbuff[4] = (uint8_t)(temp % 10) + '0';
    UARTSendString(charbuff,5);
}
/*
 * @fn:        void InitADC(void)
 * @brief:    初始化ADC
 * @para:    none
 * @return:    none
 * @comment:初始化ADC
 */
void InitADC(void)
{
      /*设置ADC时钟MCLK*/
      ADC10CTL1 = ADC10SSEL_2;
      /*ADC 2分频*/
      ADC10CTL1 = ADC10DIV_0;
      /*设置ADC基准源*/
      ADC10CTL0 = SREF_1;
      /*设置ADC采样保持时间64CLK*/
      ADC10CTL0 = ADC10SHT_3;
      /*设置ADC采样率200k*/
      ADC10CTL0 &= ~ADC10SR;
      /*ADC基准选择2.5V*/
      ADC10CTL0 = REF2_5V;
      /*开启基准*/
      ADC10CTL0 = REFON;
      /*选择ADC输入通道A0*/
      ADC10CTL1 = INCH_0;
      /*允许A0模拟输入*/
      ADC10AE0 = 0x0001;
      /*开启ADC*/
      ADC10CTL0 = ADC10ON;
}
/*
 * @fn:        uint16_t GetADCValue(void)
 * @brief:    进行一次ADC转换并返回ADC转换结果
 * @para:    none
 * @return:    ADC转换结果
 * @comment:ADC转换结果为10bit，以uint16_t类型返回，低10位为有效数据
 */
uint16_t GetADCValue(void)
{
      /*开始转换*/
      ADC10CTL0 = ADC10SCENC;
      /*等待转换完成*/
      while(ADC10CTL1&ADC10BUSY);
      /*返回结果*/
      return ADC10MEM;
}
/*
 * main.c
 */
int main(void)
{
    float voltage = 0;
    uint16_t adcvalue = 0;
    WDTCTL = WDTPW  WDTHOLD;    // Stop watchdog timer
    InitSystemClock();
    InitUART();
    InitADC();
    while(1)
    {
        adcvalue = GetADCValue();
        voltage = adcvalue * 2.5 / 1023;
        UARTSendString("ADC10转换结果为：",17);
        PrintNumber(adcvalue);
        UARTSendString("相应电压值为：",14);
        PrintFloat(voltage);
        __delay_cycles(300000);
    }
    return 0;
}
```

## DTC

## ADC\_DTC

# \======额外======

# 逻辑运算

![src=http---www.51wendang.com-pic-898ed908de06178fd5f1e9c9-2-783-jpg_6_0_______-650-0-0-650.jpg&refer=http---www.51wendang.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg](https://kozakemi.oss-cn-beijing.aliyuncs.com/src=http---www.51wendang.com-pic-898ed908de06178fd5f1e9c9-2-783-jpg_6_0_______-650-0-0-650.jpg&refer=http---www.51wendang.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg.jpeg)  
异或  
![截屏2021-07-11 下午3.25.22](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-11%20%E4%B8%8B%E5%8D%883.25.22.png)

# MSPG2553 引脚配置

![截屏2021-07-13 下午5.30.15](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-13%20%E4%B8%8B%E5%8D%885.30.15.png)

# 波特率设置

![截屏2021-07-13 下午5.46.28](https://kozakemi.oss-cn-beijing.aliyuncs.com/%E6%88%AA%E5%B1%8F2021-07-13%20%E4%B8%8B%E5%8D%885.46.28.png)

# \======程序======

# P2.5产生PWM波，P1.3按钮调整占空比

```
#include <msp430.h>

long int flag=100;
/*
 * main.c
 */

void delay(void)
{
    unsigned int tmp;
    for(tmp = 120;tmp > 0;tmp--);
}

void PWM_Init()
{
    /*配置DCO频率为1MHz*/
        DCOCTL = CALDCO_1MHZ;
        BCSCTL1 = CALBC1_1MHZ;
        /*初始化P1.6为输入*/
        P1DIR &= ~BIT6;

        /*设置时钟源为SMCLK*/
        TA1CTL = TASSEL1;
        /*设置工作模式为Up&Down*/
        TA1CTL = MC0MC1;
        /*设置TA1CCR0为0x00FF*/
        TA1CCR0 = 100;
        /*设置TA1CCR2为0x00FF*/
        //TA1CCR2 = flag;//占空比(TACCR0 - TACCR2) / TACCR0,频率=SMCLK/(TACCR0+1)/2
        /*设置为比较模式*/
        TA1CCTL0 &= ~CAP;
        TA1CCTL2 &= ~CAP;
        /*设置比较输出模式*/
        TA1CCTL2 = OUTMOD_6;
        /*设置IO复用*/
        P2SEL = BIT5;
        P2DIR = BIT5;
}
void Key()
{
  P1DIR&=~(BIT3+BIT3);//设置IO为输入模式
  P1REN=BIT3;//启用上拉/下拉模式
  P1OUT=BIT3;//设置为上拉模式
  {
    if((P1IN&BIT3)==0)//按键按下
    {

     delay();  //延时用来消除按键的抖动
      while(!(P1IN&BIT3));      //等待按键松手
      flag-=10;                  //flag+1   用来改变占空比
      if(flag==0)              //占空比到100  清零
        flag=100;
    }

  }

}
int main(void)
{

    WDTCTL = WDTPW  WDTHOLD;

    PWM_Init();
   // PWM_Init();
      for (;;)
      {
        Key();
        TA1CCR2 = flag;
      }


}
```

原文

```
#include <msp430.h>
long int flag=10;//决定PWM占空比的变量
/****************************************************************************
函数名称 ：delay
函数功能：延时   延时的长短由tmp的数值决定
函数参数：无
函数返回值：无
*****************************************************************************/
void delay(void)
{
    unsigned int tmp;
    for(tmp = 120;tmp > 0;tmp--);
}

/*****************************************************************************
函数名称 ：Key
函数功能 ：检测按键  用来改变flag的值 进而改变PWM的占空比  目前PWM的精确度为1%
函数参数：无
函数返回值：无
*****************************************************************************/
void Key()
{ 
  P1DIR&=~(BIT3+BIT3);//设置IO为输入模式
  P1REN=BIT3+BIT4;//启用上拉/下拉模式
  P1OUT=BIT3+BIT4;//设置为上拉模式               
  {         
    if((P1IN&BIT3)==0)//按键按下
    {
      delay();                  //延时用来消除按键的抖动
      while(!(P1IN&BIT3));      //等待按键松手
      flag+=1;                  //flag+1   用来改变占空比
      if(flag>100)              //占空比到100  清零
        flag=0;
    }
    if((P1IN&BIT4)==0)//同上注释
    {
      delay();
      while(!(P1IN&BIT4));
      flag-=1;
      if(flag>100)
        flag=0;
    }
  }
}

/**************************************************************************
函数名称： PWM_Init
函数功能：初始化PWM
函数参数：无
函数返回值：无
***************************************************************************/
void  PWM_Init()
{
  P1DIR = BIT2;                            // 设置P1.2为输出
  P1SEL = BIT2;                            // 选择P1.2的PWM功能
  P1SEL2 = 0;                    //选择默认引脚功能 
  CCR0 = 100;                              // 设定PWM  周期  
  CCTL1 = OUTMOD_7;                         
  TACTL = TASSEL_2 + MC_1;                  // SMCLK时钟  增计数模式 
}

int main(void)
{
  WDTCTL = WDTPW + WDTHOLD;                 // 关闭看门狗
  PWM_Init();
  for (;;)
  {
    Key();
    CCR1 = flag;
  }
}

//————————————————
//版权声明：本文为CSDN博主「HopesunIce」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
//原文链接：https://blog.csdn.net/HopesunIce/article/details/78711486
```

改进，使用中断

```
#include <msp430.h>

long int flag=100;
/*
 * main.c
 */

void delay(void)
{
    unsigned int tmp;
    for(tmp = 120;tmp > 0;tmp--);
}

void PWM_Init()
{
    /*配置DCO频率为1MHz*/
        DCOCTL = CALDCO_1MHZ;
        BCSCTL1 = CALBC1_1MHZ;
        /*初始化P1.6为输入*/
        P1DIR &= ~BIT6;

        /*设置时钟源为SMCLK*/
        TA1CTL = TASSEL1;
        /*设置工作模式为Up&Down*/
        TA1CTL = MC0MC1;
        /*设置TA1CCR0为0x00FF*/
        TA1CCR0 = 100;
        /*设置TA1CCR2为0x00FF*/
        //TA1CCR2 = flag;//占空比(TACCR0 - TACCR2) / TACCR0,频率=SMCLK/(TACCR0+1)/2
        /*设置为比较模式*/
        TA1CCTL0 &= ~CAP;
        TA1CCTL2 &= ~CAP;
        /*设置比较输出模式*/
        TA1CCTL2 = OUTMOD_6;
        /*设置IO复用*/
        P2SEL = BIT5;
        P2DIR = BIT5;
}
void Key()
{
    /*初始化按键所在IO口P1.3为输入*/
        P1DIR &= ~BIT3;
    /*使能P1.3口的上拉电阻*/
        P1REN = BIT3;
        P1OUT = BIT3;
        /*打开P1.3口中断*/
        P1IE = BIT3;
        /*设定为下降沿触发*/
        P1IES = BIT3;
        /*清除中断标志位*/
        P1IFG &= ~BIT3;
        /*打开全局中断*/
        __bis_SR_register(GIE);


}
int main(void)
{

    WDTCTL = WDTPW  WDTHOLD;

    PWM_Init();
   // PWM_Init();
      for (;;)
      {
        Key();
        TA1CCR2 = flag;
      }


}
#pragma vector = PORT1_VECTOR
__interrupt void Port1_ISR(void)
{if(P1IFG & BIT3)//判断是否是P1.3产生中断
{
    P1IFG &= ~BIT3;//清除标志位
    flag-=10;                  //flag+1   用来改变占空比
          if(flag==0)              //占空比到100  清零
            flag=100;

}
}
```

```
#include "msp430g2553.h"
int flag=500;
int main( void )
{
  // Stop watchdog timer to prevent time out reset
  WDTCTL = WDTPW + WDTHOLD;

  DCOCTL = CALDCO_16MHZ;
    BCSCTL1 = CALBC1_16MHZ;
    P1DIR&=~BIT6;//配置LEDP1.6为输入
    P1DIR&=~BIT0;//配置LEDP1.0为输入


    TA0CTL=BIT9;//配置为SMCLK
    TA0CTL&=~BIT8;
    TA0CTL&=~TACLR;
    TA0CTL=MC_3;//UP/DOWN 模式
    TA0CCR0=500;
    TA0CCR1=0;
    TA0CCTL1=OUTMOD_6;
    P1DIR=BIT2;
    P1SEL=BIT2;



    TA1CTL=BIT9;//配置为SMCLK
    TA1CTL&=~BIT8;
    TA1CTL&=~TACLR;
    TA1CTL=MC_3;
    TA1CCR0 = 500;
    TA1CCR2 = 300;//占空比(TACCR0 - TACCR2) / TACCR0,频率=SMCLK/(TACCR0+1)/2
    TA1CCTL2 = OUTMOD_6;//Output Mode 6:Toggle/Set         切换/置位
    P2SEL = BIT5;
    P2DIR = BIT5;



    while(1)
    {
        //LPM3;
        /*初始化按键所在IO口P1.3为输入*/
           P1DIR &= ~BIT3;
           /*使能P1.3口的上拉电阻*/
           P1REN = BIT3;
           P1OUT = BIT3;
           /*打开P1.3口中断*/
           P1IE = BIT3;
           /*设定为下降沿触发*/
           P1IES = BIT3;
           /*清除中断标志位*/
           P1IFG &= ~BIT3;
           /*打开全局中断*/
           __bis_SR_register(GIE);
           TA0CCR1=flag;
    }
  return 0;
}
//————————————————
//版权声明：本文为CSDN博主「角落de男孩」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
//原文链接：https://blog.csdn.net/qq_35813104/article/details/72823234

#pragma vector = PORT1_VECTOR
__interrupt void Port1_ISR(void)
{
    if(P1IFG & BIT3)//判断是否是P1.3产生中断
    {
        P1IFG &= ~BIT3;//清除标志位
        //P1OUT ^= BIT6;
        if (flag<=500) {
            flag+=100;
        }else{
            flag=0;
        }
    }
}
```

# Kuruma

```
#include <msp430g2553.h>
char i=0;
char leftspeed=10;
char rightspeed=10;
void init()
{
        P1DIR &= ~(BIT3+BIT4+BIT1+BIT2);
        P2DIR=BIT3;
        P2DIR=BIT2;
        TA0CTL=MC_1+TASSEL_2+TACLR;
        TA0CCTL0=CCIE;
        TA0CCR0=50;
    }
void main(void)
{
    WDTCTL = WDTPW  WDTHOLD;
    P1DIR = BIT6;
    P1OUT &= ~BIT6;
    init();
    __bis_SR_register(GIE);
    while(1)
    {
        if(!(P1IN & BIT3))   //判断是否P1.3产生中断
        {
            P1OUT ^= BIT6;
            leftspeed=10;
            rightspeed=20;
        }
        else  if(!(P1IN & BIT4))   //判断是否P1.3产生中断
        {
            P1OUT ^= BIT6;
            leftspeed=20;
            rightspeed=10;
        }
        if(!(P1IN & BIT1))
        {
            leftspeed=40;
            rightspeed=5;
        }
        if(!(P1IN & BIT2))
            {
                leftspeed=40;
                rightspeed=5;
         }
    }
}


#pragma vector = TIMER0_A0_VECTOR
__interrupt void Timer_A(void)
{
    __disable_interrupt();
    i++;
    if(i<leftspeed)
    {
        P2OUT=BIT3;
    }
    else if(leftspeed<=i<=100)
    {
        P2OUT&=~BIT3;
     }
     if(i<rightspeed)
        {
            P2OUT=BIT2;
        }
     else if(rightspeed<=i<=100)
        {
           P2OUT&=~BIT2;
         }
       if(i==100)
       {i=0;}
    __enable_interrupt();
}
```