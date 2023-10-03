---
title: STM32 GPIO操作
abbrlink: fea5aa33
date: 2023-09-08 23:15:06
tags:
  - STM32
  - GPIO
categories:
  - 嵌入式开发
excerpt: 文章介绍了如何在STM32微控制器上进行GPIO操作，包括外设时钟控制、GPIO初始化和GPIO输出。它提供了相应的函数和结构体定义，并给出了一些示例代码来演示如何设置引脚的工作模式、设置引脚的电平状态以及写入端口数据。
---

## 外设时钟控制

```c
RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA,ENABLE);
```

跳转之后，函数提示输入以下信息：

```c
/**
  * @brief  Enables or disables the High Speed APB (APB2) peripheral clock.
  * @param  RCC_APB2Periph: specifies the APB2 peripheral to gates its clock.
  *   This parameter can be any combination of the following values:
  *     @arg RCC_APB2Periph_AFIO, RCC_APB2Periph_GPIOA, RCC_APB2Periph_GPIOB,
  *          RCC_APB2Periph_GPIOC, RCC_APB2Periph_GPIOD, RCC_APB2Periph_GPIOE,
  *          RCC_APB2Periph_GPIOF, RCC_APB2Periph_GPIOG, RCC_APB2Periph_ADC1,
  *          RCC_APB2Periph_ADC2, RCC_APB2Periph_TIM1, RCC_APB2Periph_SPI1,
  *          RCC_APB2Periph_TIM8, RCC_APB2Periph_USART1, RCC_APB2Periph_ADC3,
  *          RCC_APB2Periph_TIM15, RCC_APB2Periph_TIM16, RCC_APB2Periph_TIM17,
  *          RCC_APB2Periph_TIM9, RCC_APB2Periph_TIM10, RCC_APB2Periph_TIM11     
  * @param  NewState: new state of the specified peripheral clock.
  *   This parameter can be: ENABLE or DISABLE.
  * @retval None
  */
void RCC_APB2PeriphClockCmd(uint32_t RCC_APB2Periph, FunctionalState NewState)
```

> RCC_APB2PeriphClockCmd( )
>
> 参数1：假设使用A0口，即为RCC_APB2Periph_GPIOA
>
> 参数2：启用或禁用

## GPIO初始化

```c
GPIO_InitTypeDef GPIO_InitStructure;
GPIO_InitStructure.GPIO_Mode=GPIO_Mode_Out_PP;
GPIO_InitStructure.GPIO_Pin=GPIO_Pin_0;
GPIO_InitStructure.GPIO_Speed=GPIO_Speed_50MHz;
GPIO_Init(GPIOA,&GPIO_InitStructure);
```
### GPIO_Init( )

```c
/**
  * @brief  Initializes the GPIOx peripheral according to the specified
  *         parameters in the GPIO_InitStruct.
  * @param  GPIOx: where x can be (A..G) to select the GPIO peripheral.
  * @param  GPIO_InitStruct: pointer to a GPIO_InitTypeDef structure that
  *         contains the configuration information for the specified GPIO peripheral.
  * @retval None
  */
void GPIO_Init(GPIO_TypeDef* GPIOx, GPIO_InitTypeDef* GPIO_InitStruct)
```

> GPIO_Init( )
>
> 参数1：GPIOx，A0为A
>
> 参数2：结构体类型GPIO_InitTypeDef的地址

### 结构体 GPIO_InitTypeDef

用于初始化GPIO

```c
typedef struct
{
  uint16_t GPIO_Pin;             /*!< Specifies the GPIO pins to be configured.
                                      This parameter can be any value of @ref GPIO_pins_define */

  GPIOSpeed_TypeDef GPIO_Speed;  /*!< Specifies the speed for the selected pins.
                                      This parameter can be a value of @ref GPIOSpeed_TypeDef */

  GPIOMode_TypeDef GPIO_Mode;    /*!< Specifies the operating mode for the selected pins.
                                      This parameter can be a value of @ref GPIOMode_TypeDef */
}GPIO_InitTypeDef;
```

> **GPIO_Pin **查询GPIO_pins_define
>
> ```c
> /** @defgroup GPIO_pins_define 
>   * @{
>   */
> 
> #define GPIO_Pin_0                 ((uint16_t)0x0001)  /*!< Pin 0 selected */
> #define GPIO_Pin_1                 ((uint16_t)0x0002)  /*!< Pin 1 selected */
> #define GPIO_Pin_2                 ((uint16_t)0x0004)  /*!< Pin 2 selected */
> #define GPIO_Pin_3                 ((uint16_t)0x0008)  /*!< Pin 3 selected */
> #define GPIO_Pin_4                 ((uint16_t)0x0010)  /*!< Pin 4 selected . . . .
> #define GPIO_Pin_12                ((uint16_t)0x1000)  /*!< Pin 12 selected */
> #define GPIO_Pin_13                ((uint16_t)0x2000)  /*!< Pin 13 selected */
> #define GPIO_Pin_14                ((uint16_t)0x4000)  /*!< Pin 14 selected */
> #define GPIO_Pin_15                ((uint16_t)0x8000)  /*!< Pin 15 selected */
> #define GPIO_Pin_All               ((uint16_t)0xFFFF)  /*!< All pins selected */
> ```
> 
> 由于点灯使用A的0号引脚，故使用GPIO_Pin_0，即GPIO_InitStructure.GPIO_Pin=GPIO_Pin_0;

> **GPIO_Mode** 查询GPIOMode_TypeDef GPIO的八种工作模式
>
> ```c
> typedef enum
> { GPIO_Mode_AIN = 0x0,//模拟输入
> GPIO_Mode_IN_FLOATING = 0x04,//浮空输入
> GPIO_Mode_IPD = 0x28,//下拉输入
> GPIO_Mode_IPU = 0x48,//上拉输入
> GPIO_Mode_Out_OD = 0x14,//开漏输出
> GPIO_Mode_Out_PP = 0x10,//推挽输出
> GPIO_Mode_AF_OD = 0x1C,//复用开漏
> GPIO_Mode_AF_PP = 0x18//复用推挽
> }GPIOMode_TypeDef;
> ```
>
> 由于点灯使用推挽输出，故 GPIO_InitStructure.GPIO_Mode=GPIO_Mode_Out_PP；
>

> **GPIO_Speed** 查询 GPIOSpeed_TypeDef
>
> ```c
> typedef enum
> { 
> GPIO_Speed_10MHz = 1,
> GPIO_Speed_2MHz, 
> GPIO_Speed_50MHz
> }GPIOSpeed_TypeDef;
> ```
>
> 平常使用50mhz即可，即GPIO_InitStructure.GPIO_Speed=GPIO_Speed_50MHz;

## GPIO输出

### 初始化

1、设置时钟

2、初始化io

3、实现代码

```c
RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA,ENABLE);

GPIO_InitTypeDef GPIO_InitStructure;
GPIO_InitStructure.GPIO_Mode=GPIO_Mode_Out_PP;
GPIO_InitStructure.GPIO_Pin=GPIO_Pin_0;
GPIO_InitStructure.GPIO_Speed=GPIO_Speed_50MHz;
GPIO_Init(GPIOA,&GPIO_InitStructure);
```

### GPIO_SetBits( ) 设置高电平

```c
/**
  * @brief  Sets the selected data port bits.
  * @param  GPIOx: where x can be (A..G) to select the GPIO peripheral.
  * @param  GPIO_Pin: specifies the port bits to be written.
  *   This parameter can be any combination of GPIO_Pin_x where x can be (0..15).
  * @retval None
  */
void GPIO_SetBits(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin)
```

> 参数1：GPIOx (A..G)   例如A0 为GPIOA
>
> 参数2：GPIO_Pin_x (0..15)  例如A0 为GPIO_Pin_0

### GPIO_ResetBits( )设置低电平

```c
/**
  * @brief  Clears the selected data port bits.
  * @param  GPIOx: where x can be (A..G) to select the GPIO peripheral.
  * @param  GPIO_Pin: specifies the port bits to be written.
  *   This parameter can be any combination of GPIO_Pin_x where x can be (0..15).
  * @retval None
  */
void GPIO_ResetBits(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin)
```

> 参数1：GPIOx (A..G)   例如A0 为GPIOA
>
> 参数2：GPIO_Pin_x (0..15)  例如A0 为GPIO_Pin_0

### GPIO_WriteBit( ) 设置单个端口的值

```c
/**
  * @brief  Sets or clears the selected data port bit.
  * @param  GPIOx: where x can be (A..G) to select the GPIO peripheral.
  * @param  GPIO_Pin: specifies the port bit to be written.
  *   This parameter can be one of GPIO_Pin_x where x can be (0..15).
  * @param  BitVal: specifies the value to be written to the selected bit.
  *   This parameter can be one of the BitAction enum values:
  *     @arg Bit_RESET: to clear the port pin
  *     @arg Bit_SET: to set the port pin
  * @retval None
  */
void GPIO_WriteBit(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin, BitAction BitVal)
```

> 参数1：GPIOx (A..G)   例如A0 为GPIOA
>
> 参数2：GPIO_Pin_x (0..15)  例如A0 为 GPIO_Pin_0
>
> 参数3：Bit_RESET 清除端口值(清零)   Bit_SET设置端口值(置1)

### GPIO_Write( ) 设定GPIOx的端口值

```C
/**
  * @brief  Writes data to the specified GPIO data port.
  * @param  GPIOx: where x can be (A..G) to select the GPIO peripheral.
  * @param  PortVal: specifies the value to be written to the port output data register.
  * @retval None
  */
void GPIO_Write(GPIO_TypeDef* GPIOx, uint16_t PortVal)
```

> 参数1：GPIOx (A..G)   例如A0 为GPIOA
>
> 参数2：ODR寄存器值 16位二进制，但是c语言需要将其转为16进制
>
> ​			举例 设置A0为高   0000 0000 0000 0001
>
> ​									   0x0        0        0        1
>
> ​										0x0001;

### 总览

```c
GPIO_SetBits(GPIOA,GPIO_Pin_0);
GPIO_ResetBits(GPIOA,GPIO_Pin_0);
GPIO_WriteBit(GPIOA,GPIO_Pin_0,Bit_RESET );
GPIO_Write(GPIOA,0x0001);
```

### 应用

#### 灯闪烁

点亮led等待一段时间，然后再熄灭，循环

```c
#include "stm32f10x.h"                  // Device header
#include "Delay.h"
int main(void)
{
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA,ENABLE);//配置时钟
	
	GPIO_InitTypeDef GPIO_InitStructure;
	GPIO_InitStructure.GPIO_Mode=GPIO_Mode_Out_PP;
	GPIO_InitStructure.GPIO_Pin=GPIO_Pin_0;
	GPIO_InitStructure.GPIO_Speed=GPIO_Speed_50MHz;
	
	GPIO_Init(GPIOA,&GPIO_InitStructure);
//	GPIO_SetBits(GPIOA,GPIO_Pin_0);//高电平
//	GPIO_ResetBits(GPIOA,GPIO_Pin_0);//低电平
//	GPIO_WriteBit(GPIOA,GPIO_Pin_0,Bit_RESET);
	while(1){
//		GPIO_WriteBit(GPIOA,GPIO_Pin_0,Bit_RESET);
//		GPIO_ResetBits(GPIOA,GPIO_Pin_0);//低电平
		GPIO_WriteBit(GPIOA,GPIO_Pin_0,(BitAction)0);
		Delay_s(1);
//		GPIO_WriteBit(GPIOA,GPIO_Pin_0,Bit_SET);
//		GPIO_SetBits(GPIOA,GPIO_Pin_0);//高电平
		GPIO_WriteBit(GPIOA,GPIO_Pin_0,(BitAction)1);
		Delay_s(1);
	}
}
```

#### 流水灯

```c
#include "stm32f10x.h"                  // Device header
#include "Delay.h"
int main(void)
{
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA,ENABLE);//配置时钟
	
	GPIO_InitTypeDef GPIO_InitStructure;
	GPIO_InitStructure.GPIO_Mode=GPIO_Mode_Out_PP;
	GPIO_InitStructure.GPIO_Pin=GPIO_Pin_All;
	GPIO_InitStructure.GPIO_Speed=GPIO_Speed_50MHz;
	GPIO_Init(GPIOA,&GPIO_InitStructure);
	
	uint16_t mask = 0x0001;
	while (1) {
		for (int i = 0; i < 8; i++) {
			GPIO_Write(GPIOA, ~mask);
			Delay_s(1);
			mask = mask << 1;
		}
		mask = 0x0001;  // 重置 mask，重新开始循环
	}
}

```



## GPIO 输入

### 初始化

1、设置时钟

2、初始化io

3、实现代码

```c
RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB,ENABLE);//配置时钟

GPIO_InitTypeDef GPIO_InitStructure2;
GPIO_InitStructure2.GPIO_Mode=GPIO_Mode_IPU;
GPIO_InitStructure2.GPIO_Pin=GPIO_Pin_10;
GPIO_Init(GPIOA,&GPIO_InitStructure2);


```

观察发现，相比于GPIO输出仅变两处代码

> 1、删除GPIO_Speed，对于输入模式GPIO_Speed无意义
>
> 2、设置GPIO_Mode为GPIO_Mode_IPU



### GPIO_ReadInputDataBit( ) 读取单个端口的值

```c
/**
  * @brief  Reads the specified input port pin.
  * @param  GPIOx: where x can be (A..G) to select the GPIO peripheral.
  * @param  GPIO_Pin:  specifies the port bit to read.
  *   This parameter can be GPIO_Pin_x where x can be (0..15).
  * @retval The input port pin value.
  */
uint8_t GPIO_ReadInputDataBit(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin)
```

> 入参：GPIOx、GPIO—Pin
>
> 出参：value 1/0



### GPIO_ReadInputData( ) 读取端口GPIOx的值

```c
/**
  * @brief  Reads the specified GPIO input data port.
  * @param  GPIOx: where x can be (A..G) to select the GPIO peripheral.
  * @retval GPIO input data port value.
  */
uint16_t GPIO_ReadInputData(GPIO_TypeDef* GPIOx)
```

> 入参：GPIOx
>
> 出参：16位二进制 例如0x0001



### GPIO_ReadOutputDataBit( ) 查询设定的单个端口的值

```c
/**
  * @brief  Reads the specified output data port bit.
  * @param  GPIOx: where x can be (A..G) to select the GPIO peripheral.
  * @param  GPIO_Pin:  specifies the port bit to read.
  *   This parameter can be GPIO_Pin_x where x can be (0..15).
  * @retval The output port pin value.
  */
uint8_t GPIO_ReadOutputDataBit(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin)
```

> 入参：GPIOx、GPIO—Pin
>
> 出参：value 1/0



### GPIO_ReadOutputData( )查询设定的GPIOx的值

```c
/**
  * @brief  Reads the specified GPIO output data port.
  * @param  GPIOx: where x can be (A..G) to select the GPIO peripheral.
  * @retval GPIO output data port value.
  */
uint16_t GPIO_ReadOutputData(GPIO_TypeDef* GPIOx)
```

> 入参：GPIOx
>
> 出参：16位二进制 例如0x0001

### 总览

```c
xxx=GPIO_ReadInputDataBit(GPIOB,GPIO_Pin_10);
xxx=GPIO_ReadOutputDataBit(GPIOB);
xxx=GPIO_ReadOutputDataBit(GPIOB,GPIO_Pin_10);
xxx=GPIO_ReadOutputData(GPIOB);
```

### 应用

#### 按键改变灯光状态

```c
#include "stm32f10x.h"                  // Device header
#include "Delay.h"
int main(void)
{
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA,ENABLE);//配置时钟
	
	GPIO_InitTypeDef GPIO_InitStructure;
	GPIO_InitStructure.GPIO_Mode=GPIO_Mode_Out_PP;
	GPIO_InitStructure.GPIO_Pin=GPIO_Pin_0;
	GPIO_InitStructure.GPIO_Speed=GPIO_Speed_50MHz;
	GPIO_Init(GPIOA,&GPIO_InitStructure);
	
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB,ENABLE);//配置时钟
	GPIO_InitTypeDef GPIO_InitStructure2;
	GPIO_InitStructure2.GPIO_Mode=GPIO_Mode_IPU;
	GPIO_InitStructure2.GPIO_Pin=GPIO_Pin_10;
	GPIO_InitStructure2.GPIO_Speed=GPIO_Speed_50MHz;
	GPIO_Init(GPIOB,&GPIO_InitStructure2);
	
	int value=0;
	
	while(1){
		value=GPIO_ReadOutputDataBit(GPIOA,GPIO_Pin_0);
		if (GPIO_ReadInputDataBit(GPIOB,GPIO_Pin_10)==0){
			Delay_ms(20);
			while(GPIO_ReadInputDataBit(GPIOB,GPIO_Pin_10)==0){
				Delay_ms(20);
				if(value){
					GPIO_WriteBit(GPIOA,GPIO_Pin_0,(BitAction)0);
				}else{
					GPIO_WriteBit(GPIOA,GPIO_Pin_0,(BitAction)1);
				}
			}
		}
		
	}
}

```

> 不过需要注意的是
>
> ```c
> GPIO_WriteBit(GPIOA,GPIO_Pin_0,(BitAction)(~value));
> ```
>
> 这样写，不能正常工作
> 故使用以下语句代替
>
> ```C
> if(value){
>     GPIO_WriteBit(GPIOA,GPIO_Pin_0,(BitAction)0);
> }else{
>     GPIO_WriteBit(GPIOA,GPIO_Pin_0,(BitAction)1);
> }
> ```
> 感觉逻辑是对的，但是不知为何不工作

## 补充内容

### 推挽模式与开漏模式

推挽输出再高电平与低电平都具有驱动能力

开漏输出低电平有驱动能力，高电平相当于高阻态无驱动能力

## 总结

### 代码总体的书写流程

第一步，配置RCC外设时钟控制

第二步，初始化GPIO引脚，借助结构体配置io引脚号、模式、频率等，其中模式很重要

第三步，根据选择的模式，使用使用输入或者输出的函数，实现其余的代码逻辑



### GPIO的8种工作模式

1. `GPIO_Mode_AIN`（模拟输入）：将引脚配置为模拟输入模式。此模式适用于连接到模拟信号的引脚，用于测量或读取模拟电压。
2. `GPIO_Mode_IN_FLOATING`（浮空输入）：将引脚配置为浮空输入模式。在此模式下，引脚既不连接到外部电源也不连接到地，可以用作普通输入引脚。
3. `GPIO_Mode_IPD`（下拉输入）：将引脚配置为下拉输入模式。在此模式下，引脚被连接到地，并且在未连接到外部信号时，引脚被拉低为逻辑低电平。
4. `GPIO_Mode_IPU`（上拉输入）：将引脚配置为上拉输入模式。在此模式下，引脚被连接到外部电源，并且在未连接到外部信号时，引脚被拉高为逻辑高电平。
5. `GPIO_Mode_Out_OD`（开漏输出）：将引脚配置为开漏输出模式。在此模式下，引脚可以输出高电平或悬空（高阻态），但不能输出低电平。需要通过外部上拉电阻或其他引脚来拉低引脚电平。
6. `GPIO_Mode_Out_PP`（推挽输出）：将引脚配置为推挽输出模式。在此模式下，引脚可以输出高电平或低电平，可以直接驱动外部电路。
7. `GPIO_Mode_AF_OD`（复用开漏）：将引脚配置为复用开漏模式。在此模式下，引脚用于特定的外设功能，并且输出为开漏模式。需要通过外部上拉电阻或其他引脚来拉低引脚电平。
8. `GPIO_Mode_AF_PP`（复用推挽）：将引脚配置为复用推挽模式。在此模式下，引脚用于特定的外设功能，并且输出为推挽模式，可以直接驱动外部电路。
