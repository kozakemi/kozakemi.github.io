---
title: STM32 TIM输入捕获与PWM频率测定
published: 2023-09-22 20:59:55
description: 本文介绍了在STM32嵌入式开发中使用TIM定时器进行输入捕获和PWM频率测定的方法。文章提供了相关函数和代码示例，包括配置输入捕获单元、选择触发源和触发操作等步骤。
tags: [MCU,ST,STM32,定时器,定时器输入捕获]
category: 嵌入式
draft: false
---

【STM32入门教程-2023持续更新中】https://www.bilibili.com/video/BV1th411z7sn?p=17&vd_source=a72069186a610509925b0932bb49f8cc

## 相关函数

```c
void TIM_ICInit(TIM_TypeDef* TIMx, TIM_ICInitTypeDef* TIM_ICInitStruct);//配置输入捕获单元
void TIM_PWMIConfig(TIM_TypeDef* TIMx, TIM_ICInitTypeDef* TIM_ICInitStruct);//快速配置PWMI模式
void TIM_ICStructInit(TIM_ICInitTypeDef* TIM_ICInitStruct);//为结构体赋值初值
void TIM_SelectInputTrigger(TIM_TypeDef* TIMx, uint16_t TIM_InputTriggerSource);//选择输入触发源
void TIM_SelectOutputTrigger(TIM_TypeDef* TIMx, uint16_t TIM_TRGOSource);//选择输出触发源
void TIM_SelectSlaveMode(TIM_TypeDef* TIMx, uint16_t TIM_SlaveMode);//从模式选择
void TIM_SetCompare1(TIM_TypeDef* TIMx, uint16_t Compare1);//配置1234的分频器
void TIM_SetCompare2(TIM_TypeDef* TIMx, uint16_t Compare2);
void TIM_SetCompare3(TIM_TypeDef* TIMx, uint16_t Compare3);
void TIM_SetCompare4(TIM_TypeDef* TIMx, uint16_t Compare4);
```



## 输入捕获

### 开启定时器与GPIO的时钟

```c
RCC_APB1PeriphClockCmd(RCC_APB1Periph_TIM3, ENABLE);//PA6
RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);
```

### 初始化GPIO输入模式

```c
GPIO_InitTypeDef GPIO_InitStructure;
GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IPU;//上拉输入
GPIO_InitStructure.GPIO_Pin = GPIO_Pin_6;
GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
GPIO_Init(GPIOA, &GPIO_InitStructure);
```

### 配置时基单元

```c
TIM_InternalClockConfig(TIM3);
```

```c
TIM_TimeBaseInitTypeDef TIM_TimeBaseInitStructure;
TIM_TimeBaseInitStructure.TIM_ClockDivision = TIM_CKD_DIV1;
TIM_TimeBaseInitStructure.TIM_CounterMode = TIM_CounterMode_Up;
TIM_TimeBaseInitStructure.TIM_Period = 65536 - 1;		//ARR防止计数溢出
TIM_TimeBaseInitStructure.TIM_Prescaler = 72 - 1;		//PSC
TIM_TimeBaseInitStructure.TIM_RepetitionCounter = 0;
TIM_TimeBaseInit(TIM3, &TIM_TimeBaseInitStructure);
```



### 配置输入捕获单元

```c
TIM_ICInitTypeDef TIM_ICInitStructure;
TIM_ICInitStructure.TIM_Channel = TIM_Channel_1;
TIM_ICInitStructure.TIM_ICFilter = 0xF;
TIM_ICInitStructure.TIM_ICPolarity = TIM_ICPolarity_Rising;
TIM_ICInitStructure.TIM_ICPrescaler = TIM_ICPSC_DIV1;
TIM_ICInitStructure.TIM_ICSelection = TIM_ICSelection_DirectTI;
TIM_ICInit(TIM3, &TIM_ICInitStructure);
```

```c
/** 
  * @brief  TIM Input Capture Init structure definition  
  */

typedef struct
{

  uint16_t TIM_Channel;      /*!< Specifies the TIM channel.
                                  This parameter can be a value of @ref TIM_Channel */

  uint16_t TIM_ICPolarity;   /*!< Specifies the active edge of the input signal.
                                  This parameter can be a value of @ref TIM_Input_Capture_Polarity */

  uint16_t TIM_ICSelection;  /*!< Specifies the input.
                                  This parameter can be a value of @ref TIM_Input_Capture_Selection */

  uint16_t TIM_ICPrescaler;  /*!< Specifies the Input Capture Prescaler.
                                  This parameter can be a value of @ref TIM_Input_Capture_Prescaler */

  uint16_t TIM_ICFilter;     /*!< Specifies the input capture filter.
                                  This parameter can be a number between 0x0 and 0xF */
} TIM_ICInitTypeDef;
```

> TIM_Channel：选择通道
>
> ```c
> #define TIM_Channel_1                      ((uint16_t)0x0000)
> #define TIM_Channel_2                      ((uint16_t)0x0004)
> #define TIM_Channel_3                      ((uint16_t)0x0008)
> #define TIM_Channel_4                      ((uint16_t)0x000C)
> ```
>
> PA6为通道1 **TIM_ICInitStructure.TIM_Channel = TIM_Channel_1;**

> TIM_ICFilter：配置输入捕获滤波器，范围0x0 ~ 0xF

> TIM_ICPolarity：边沿检测极性选择
>
> ```c
> #define  TIM_ICPolarity_Rising             ((uint16_t)0x0000)//上升沿触发
> #define  TIM_ICPolarity_Falling            ((uint16_t)0x0002)//下降沿触发
> #define  TIM_ICPolarity_BothEdge           ((uint16_t)0x000A)//两者都触发
> ```

> TIM_ICPrescaler：设置分频系数
>
> ```c
> #define TIM_ICPSC_DIV1                     ((uint16_t)0x0000) /*!< Capture performed each time an edge is detected on the capture input. */
> #define TIM_ICPSC_DIV2                     ((uint16_t)0x0004) /*!< Capture performed once every 2 events. */
> #define TIM_ICPSC_DIV4                     ((uint16_t)0x0008) /*!< Capture performed once every 4 events. */
> #define TIM_ICPSC_DIV8                     ((uint16_t)0x000C) /*!< Capture performed once every 8 events. */
> ```
>
> 1 2 4 8 分频

> TIM_ICSelection：选择信号触发引脚
>
> ```c
> #define TIM_ICSelection_DirectTI           ((uint16_t)0x0001) /*!< TIM Input 1, 2, 3 or 4 is selected to be 
>                                                                    connected to IC1, IC2, IC3 or IC4, respectively */
> #define TIM_ICSelection_IndirectTI         ((uint16_t)0x0002) /*!< TIM Input 1, 2, 3 or 4 is selected to be
> ```
>
> 直连通道 交叉通道

### 选择触发源

```c
TIM_SelectInputTrigger(TIM3, TIM_TS_TI1FP1);
```

```c
/**
  * @brief  Selects the Input Trigger source
  * @param  TIMx: where x can be  1, 2, 3, 4, 5, 8, 9, 12 or 15 to select the TIM peripheral.
  * @param  TIM_InputTriggerSource: The Input Trigger source.
  *   This parameter can be one of the following values:
  *     @arg TIM_TS_ITR0: Internal Trigger 0
  *     @arg TIM_TS_ITR1: Internal Trigger 1
  *     @arg TIM_TS_ITR2: Internal Trigger 2
  *     @arg TIM_TS_ITR3: Internal Trigger 3
  *     @arg TIM_TS_TI1F_ED: TI1 Edge Detector
  *     @arg TIM_TS_TI1FP1: Filtered Timer Input 1
  *     @arg TIM_TS_TI2FP2: Filtered Timer Input 2
  *     @arg TIM_TS_ETRF: External Trigger input
  * @retval None
  */
void TIM_SelectInputTrigger(TIM_TypeDef* TIMx, uint16_t TIM_InputTriggerSource)
```

> 参数1、TIMx
>
> 参数2、触发源

### 选择触发操作

```c
TIM_SelectSlaveMode(TIM3, TIM_SlaveMode_Reset);
```

### 启用TIM定时

```c
TIM_Cmd(TIM3, ENABLE);
```

## PWMI

在上一个代码 **配置输入捕获单元** 之后添加以下代码

```c
TIM_PWMIConfig(TIM3, &TIM_ICInitStructure);
```

然后比较通道1与通道2的CCR值即可得到占空比，占空比的值为CCR1/CCR2

占空比计算函数：

```c
uint32_t IC_GetDuty(void)
{
	return (TIM_GetCapture2(TIM3) + 1) * 100 / (TIM_GetCapture1(TIM3) + 1);
}
```

```c
/**
  * @brief  Gets the TIMx Input Capture 1 value.
  * @param  TIMx: where x can be 1 to 17 except 6 and 7 to select the TIM peripheral.
  * @retval Capture Compare 1 Register value.
  */
uint16_t TIM_GetCapture1(TIM_TypeDef* TIMx)
{
  /* Check the parameters */
  assert_param(IS_TIM_LIST8_PERIPH(TIMx));
  /* Get the Capture 1 Register value */
  return TIMx->CCR1;
}
```

> 函数功能为获取CCR1的值
>
> 参数：TIMx
