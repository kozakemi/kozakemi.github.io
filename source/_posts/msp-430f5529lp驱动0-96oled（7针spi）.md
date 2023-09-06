---
title: MSP-430F5529LP驱动0.96‘OLED（7针SPI）
tags:
  - F5529
  - OLED
id: '465'
categories:
  - - MCU
  - - TI
date: 2021-09-25 20:49:33
---

![IMG_20210925_202514_edit_153019967420921](https://kozakemi.oss-cn-beijing.aliyuncs.com/20210925204610.jpg)

注意：此代码使用Code Composer Studio (TI官方IDE)，其他IDE可参考此代码，但不可直接使用

​ 电阻链接需4SPI模式

​ 仅适用于MSP-430F5529LP

# 接线定义：

```
GND---->GND
VCC---->3.3V (2.2~5.5V)
D0 ---->P4^0
D1 ---->P4^1
RES---->P4^2
DC ---->P4^3
CS ---->GND
```

# 资源地址

原文地址：[CSDN](https://download.csdn.net/download/xmt1139057136/10903521?utm_medium=distribute.pc_relevant.none-task-download-2%7Edefault%7EOPENSEARCH%7Edefault-9.no_search_link&depth_1-utm_source=distribute.pc_relevant.none-task-download-2%7Edefault%7EOPENSEARCH%7Edefault-9.no_search_link)

# 代码详情（不完全）

注意：使用此代码请在上链接购买下载，本文作者非代码作者下方的代码已经处理，无法直接使用，请注意

## OLED12864.c

```
/**************** Modified  By  LBP*********************
 * 程序功能：7针OLED12864显示驱动
 * 实验平台：MSP430F5529Lanuchpad
 * 开发环境：CCS V7.4
 * 7针OLED12864模块接口说明：
 * 模块引脚      单片机引脚
 *      GND---->GND
 *      VCC---->3.3V (2.2~5.5V)
 *          D0 ---->P4^0
 *          D1 ---->P4^1
 *          RES---->P4^2
 *       DC---->P4^3
 *       CS ---->GND
 ******************Modified  By  LBP********************/
#include  <msp430.h>
#include  <string.h>
#include  <stdio.h>
#include  <math.h>
#include  "OLED12864.h"
char str_temp[10];
#define set_bit(x,y)  (x=(1<<y))
#define clr_bit(x,y)  (x&=~(1<<y))
#define SCL_1 set_bit( P4OUT,0)   //sbit LCD_SCL=P4^0 ---->D0
#define SCL_0 clr_bit( P4OUT,0)

#define SDA_1 set_bit( P4OUT,1)  //sbit LCD_SDA=P4^1---->D1
#define SDA_0 clr_bit( P4OUT,1)

#define RST_1 set_bit( P4OUT,2)  //sbit LCD_RST=P4^2 ---->RES
#define RST_0 clr_bit( P4OUT,2)

#define DC_1 set_bit( P4OUT,3)//sbit LCD_DC =P4^3 ---->DC
#define DC_0 clr_bit( P4OUT,3)

void  OLED_Port_Init(void)
{
      P4DIR = BIT0;                            // P4设置为输出
      P4DIR = BIT1;
      P4DIR = BIT2;
      P4DIR = BIT3;
}
#define XLevelL        0x00
#define XLevelH        0x10
#define XLevel        ((XLevelH&0x0F)*16+XLevelL)
#define Max_Column    128
#define Max_Row        64
#define    Brightness  0xCF 

#define X_WIDTH 128
#define Y_WIDTH 64
//======================================
const prog_uchar F6x8[][6] =
{
{ 0x00, 0x00, 0x00, 0x00, 0x00, 0x00 },   // sp
{ 0x00, 0x00, 0x00, 0x2f, 0x00, 0x00 },   // !
{ 0x00, 0x00, 0x07, 0x00, 0x07, 0x00 },   // "
{ 0x00, 0x14, 0x7f, 0x14, 0x7f, 0x14 },   // #
{ 0x00, 0x24, 0x2a, 0x7f, 0x2a, 0x12 },   // $

{ 0x00, 0x1C, 0xA0, 0xA0, 0xA0, 0x7C },   // y
{ 0x00, 0x44, 0x64, 0x54, 0x4C, 0x44 },   // z
{ 0x14, 0x14, 0x14, 0x14, 0x14, 0x14 }    // horiz lines
};
//======================================================
// 128X64I液晶底层驱动[8X16]字体库
// 设计者: powerint
// 描  述: [8X16]西文字符的字模数据 (纵向取模,字节倒序)
// !"#$%&'()*+,-./0123456789:;<=>?@ABCDEFGHIJKLMNOPQRSTUVWXYZ[\]^_`abcdefghijklmnopqrstuvwxyz{}~
//======================================================
const prog_uchar F8X16[]=
{
  0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,// 0
  0x00,0x00,0x00,0xF8,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x33,0x30,0x00,0x00,0x00,//!1
  0x00,0x10,0x0C,0x06,0x10,0x0C,0x06,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,//"2
  0x40,0xC0,0x78,0x40,0xC0,0x78,0x40,0x00,0x04,0x3F,0x04,0x04,0x3F,0x04,0x04,0x00,//#3
  0x00,0x70,0x88,0xFC,0x08,0x30,0x00,0x00,0x00,0x18,0x20,0xFF,0x21,0x1E,0x00,0x00,//$4
  0xF0,0x08,0xF0,0x00,0xE0,0x18,0x00,0x00,0x00,0x21,0x1C,0x03,0x1E,0x21,0x1E,0x00,//%5

  0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x01,0x01,0x01,0x01,0x01,0x01,0x01,//-13
  0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x30,0x30,0x00,0x00,0x00,0x00,0x00,//.14
  0x00,0x00,0x00,0x00,0x80,0x60,0x18,0x04,0x00,0x60,0x18,0x06,0x01,0x00,0x00,0x00,///15

  0x00,0x70,0x08,0x08,0x08,0x88,0x70,0x00,0x00,0x30,0x28,0x24,0x22,0x21,0x30,0x00,//218
  0x00,0x30,0x08,0x88,0x88,0x48,0x30,0x00,0x00,0x18,0x20,0x20,0x20,0x11,0x0E,0x00,//319
  0x00,0x00,0xC0,0x20,0x10,0xF8,0x00,0x00,0x00,0x07,0x04,0x24,0x24,0x3F,0x24,0x00,//420
  0x00,0xF8,0x08,0x88,0x88,0x08,0x08,0x00,0x00,0x19,0x21,0x20,0x20,0x11,0x0E,0x00,//521

  0x00,0x00,0x00,0xC0,0xC0,0x00,0x00,0x00,0x00,0x00,0x00,0x30,0x30,0x00,0x00,0x00,//:26

  0x40,0x40,0x40,0x40,0x40,0x40,0x40,0x00,0x04,0x04,0x04,0x04,0x04,0x04,0x04,0x00,//=29
  0x00,0x08,0x10,0x20,0x40,0x80,0x00,0x00,0x00,0x20,0x10,0x08,0x04,0x02,0x01,0x00,//>30
  0x00,0x70,0x48,0x08,0x08,0x08,0xF0,0x00,0x00,0x00,0x00,0x30,0x36,0x01,0x00,0x00,//?31

  0xC0,0x30,0x08,0x08,0x08,0x08,0x38,0x00,0x07,0x18,0x20,0x20,0x20,0x10,0x08,0x00,//C35
  0x08,0xF8,0x08,0x08,0x08,0x10,0xE0,0x00,0x20,0x3F,0x20,0x20,0x20,0x10,0x0F,0x00,//D36

  0x00,0x00,0x00,0x00,0x80,0x7C,0x02,0x02,0x00,0x00,0x00,0x00,0x00,0x3F,0x40,0x40,//{91
  0x00,0x00,0x00,0x00,0xFF,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0xFF,0x00,0x00,0x00,//92
  0x00,0x02,0x02,0x7C,0x80,0x00,0x00,0x00,0x00,0x40,0x40,0x3F,0x00,0x00,0x00,0x00,//}93
  0x00,0x06,0x01,0x01,0x02,0x02,0x04,0x04,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,//~94
};
void LCD_WrDat(unsigned char dat)
{
    unsigned char i=8;
    DC_1;
    __delay_cycles(1);
  SCL_0;
  __delay_cycles(1);
  while(i--)
  {
    if(dat&0x80)
    {
        SDA_1;
        __delay_cycles(1);
    }
    else
    {
        SDA_0;
        __delay_cycles(1);
    }
    SCL_1;
    __delay_cycles(3);

    dat<<=1; 
    __delay_cycles(1);
  }
}
void LCD_WrCmd(unsigned char cmd)
{
    unsigned char i=8;
    DC_0;
    __delay_cycles(1);
  SCL_0;
  __delay_cycles(1);
  while(i--)
  {
    if(cmd&0x80)
    {
        SDA_1;
        __delay_cycles(1);
    }
    else
    {
        SDA_0;
        __delay_cycles(1);
    }
    SCL_1;
    __delay_cycles(3);
    SCL_0;
    __delay_cycles(1);
    cmd<<=1;
    __delay_cycles(1);
  }     
}
void LCD_Set_Pos(unsigned char x, unsigned char y)
{ 
  LCD_WrCmd(0xb0+y);

  LCD_WrCmd((x&0x0f)0x01); 
} 
void LCD_Fill(unsigned int bmp_dat)
{
    unsigned char y,x;

    for(y=0;y<8;y++)
    {
        LCD_WrCmd(0xb0+y);
        LCD_WrCmd(0x01);
        LCD_WrCmd(0x10);
        for(x=0;x<X_WIDTH;x++)
            LCD_WrDat(bmp_dat);
    }
}
void LCD_CLS(void)
{
    unsigned char y,x;  
    for(y=0;y<8;y++)
    {
        LCD_WrCmd(0xb0+y);
        LCD_WrCmd(0x01);
        LCD_WrCmd(0x10); 
        for(x=0;x<X_WIDTH;x++)
        LCD_WrDat(0);
    }
}
void LCD_DLY_ms(unsigned int ms)
{                         
  unsigned int a;
  while(ms)
  {
    a=1335;
    while(a--);
    ms--;
  }
  return;
} 
void LCD_Init(void)        
{  
    OLED_Port_Init();
    SCL_1;
    RST_0;
     __delay_cycles(5);
    RST_1;
    //从上电到下面开始初始化要有足够的时间，即等待RC复位完毕   
  LCD_WrCmd(0xae);//--turn off oled panel
  LCD_WrCmd(0x00);//---set low column address
  LCD_WrCmd(0x10);//---set high column address
  LCD_WrCmd(0x40);//--set start line address  Set Mapping RAM Display Start Line (0x00~0x3F)
  LCD_WrCmd(0x81);//--set contrast control register
  LCD_WrCmd(0xcf); // Set SEG Output Current Brightness
  LCD_WrCmd(0xa1);//--Set SEG/Column Mapping     0xa0左右反置 0xa1正常
  LCD_WrCmd(0xc8);//Set COM/Row Scan Direction   0xc0上下反置 0xc8正常
  LCD_WrCmd(0xa6);//--set normal display
  LCD_WrCmd(0xa8);//--set multiplex ratio(1 to 64)
  LCD_WrCmd(0x3f);//--1/64 duty
  LCD_WrCmd(0xd3);//-set display offset    Shift Mapping RAM Counter (0x00~0x3F)

  LCD_WrCmd(0x20);//-Set Page Addressing Mode (0x00/0x01/0x02)
  LCD_WrCmd(0x02);//
  LCD_WrCmd(0x8d);//--set Charge Pump enable/disable
  LCD_WrCmd(0x14);//--set(0x10) disable
  LCD_WrCmd(0xa4);// Disable Entire Display On (0xa4/0xa5)
  LCD_WrCmd(0xa6);// Disable Inverse Display On (0xa6/a7) 
  LCD_WrCmd(0xaf);//--turn on oled panel
  LCD_Fill(0x00);  //初始清屏
  LCD_Set_Pos(0,0);
  LCD_CLS();
} 
//==============================================================
//函数名：LCD_P6x8Str(unsigned char x,unsigned char y,unsigned char *p)
//功能描述：写入一组标准ASCII字符串
//参数：显示的位置（x,y），y为页范围0〜7，要显示的字符串
//返回：无
//==============================================================  
void LCD_P6x8Str(byte x,byte y,char ch[])   //输出6*8字符串
{
  byte c=0,i=0,j=0;
  while (ch[j]!='\0')
  {
    c =ch[j]-32;
    if(x>126){x=0;y++;}

      for(i=0;i<6;i++)
       LCD_WrDat(F6x8[c][i]);
      x+=6;
      j++;
  }
}
//==============================================================
//函数名：LCD_P8x16Str(unsigned char x,unsigned char y,unsigned char *p)
//功能描述：写入一组标准ASCII字符串
//参数：显示的位置（x,y），y为页范围0〜7，要显示的字符串
//返回：无
//============================================================== 
void LCD_P8x16Str(byte x,byte y,char ch[])  //输出8*16字符串
{
  byte c=0,i=0,j=0;
  while (ch[j]!='\0')
  {

        LCD_WrDat(F8X16[c*16+i+8]);
      x+=8;
      j++;
  }
}
void LCD_Write_6x8Int(unsigned char X,unsigned char Y,int nn)  //输出6*8 int型变量
{
    sprintf(str_temp,"%d",nn);
    LCD_P6x8Str(X,Y,str_temp);
}
void LCD_Write_8x16Int(unsigned char X,unsigned char Y,int nn)  //输出8*16 int型变量
{
    sprintf(str_temp,"%d",nn);
    LCD_P8x16Str(X,Y,str_temp);
}
void LCD_Write_6x8Float(unsigned char X,unsigned char Y,float nn)  //输出6*8 float型变量
{
    int i=(int)nn;//整数
    int j=(int)fabs((nn-i)*1000);//小数

    int k1=(int)(j%100%10);
    sprintf(str_temp,"%d.%d%d%d",i,k3,k2,k1);
    LCD_P6x8Str(X,Y,str_temp);
}
void LCD_Write_8x16Float(unsigned char X,unsigned char Y,float nn)  //输出8*16 float型变量
{
    int i=(int)nn;//整数
    int j=(int)(fabs((nn-i)*1000));//小数

    int k1=(int)(j%100%10);
    sprintf(str_temp,"%d.%d%d%d",i,k3,k2,k1);
    LCD_P8x16Str(X,Y,str_temp);
}
```

## OLED12864.h

```
/**************** Modified  By  LBP*********************
 * 程序功能：7针OLED12864显示驱动
 * 实验平台：MSP430F5529Lanuchpad
 * 开发环境：CCS V7.4
 * 7针OLED12864模块接口说明：
 * 模块引脚      单片机引脚
 *      GND---->GND
 *      VCC---->3.3V (2.2~5.5V)
 *          D0 ---->P4^0
 *          D1 ---->P4^1
 *          RES---->P4^2
 *       DC---->P4^3
 *       CS ---->GND
 ******************Modified  By  LBP********************/
#ifndef _OLED12864_H
#define _OLED12864_H

#define prog_uchar  unsigned char
#define byte  unsigned char
#define word  unsigned int
#define dword unsigned long
void LCD_Init(void);
void LCD_CLS(void);

void LCD_P6x8Str(byte x,byte y,char ch[]);
//void LCD_P6x8Str(unsigned char x,unsigned char y,char * ch);
void LCD_P8x16Str(byte x,byte y,char ch[]);
void LCD_P8x16Str(unsigned char x,unsigned char y,char *ch);
//void LCD_P14x16Ch(byte x,byte y,byte N);
void LCD_Fill(word dat);
void LCD_Write_6x8Int(unsigned char x,unsigned char y,int nn);//int型变量
void LCD_Write_8x16Int(unsigned char X,unsigned char Y,int nn);
void LCD_Write_6x8Float(unsigned char X,unsigned char Y,float nn);//float型变量
void LCD_Write_8x16Float(unsigned char X,unsigned char Y,float nn);
void  OLED_Port_Init(void);

#endif /* 12864_H_ */
```

## main.c

```
/**************** Modified  By  LBP*********************
 * 程序功能：7针OLED12864显示驱动
 * 实验平台：MSP430F5529Lanuchpad
 * 开发环境：CCS V7.4
 * 7针OLED12864模块接口说明：
 * 模块引脚      单片机引脚
 *      GND---->GND
 *      VCC---->3.3V (2.2~5.5V)
 *          D0 ---->P4^0
 *          D1 ---->P4^1
 *          RES---->P4^2
 *       DC ---->P4^3
 *       CS ---->GND
 ******************Modified  By  LBP********************/
#include  <msp430.h>
#include  <string.h>
#include  <stdio.h>
#include  <math.h>
#include  "OLED12864.h"
int  main(void)
{
    WDTCTL = WDTPW + WDTHOLD;   //关闭看门狗
    LCD_Init();//OLED初始化
 while(1)
    {
        LCD_P8x16Str(0,0,"I");
        LCD_P8x16Str(0,2,"L");
        LCD_P8x16Str(32,4,"Y");
        LCD_P8x16Str(48,6,"SCC");
        //LCD_P8x16Str(32,5,"SCC");
    }
}
```