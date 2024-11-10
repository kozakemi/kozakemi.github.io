---
title: STM32 编码器接口测速
published: 2023-10-06 22:01:06
description: 本文介绍了在STM32嵌入式开发中使用TIM定时器进行输入捕获方式实现编码器检测，用于检测编码器速度和位置。
draft: false
---

假设使用TIM3,两个引脚便是PA6与PA7，分别为CH1与CH2

## 步骤

第一步，配置RCC开启时钟，开启GPIO和定时器的时钟

第二步，配置GPIO为输入模式

第三步，配置时基单元，不分频，自动重装最大值

第四步，配置输入捕获单元

第五步，配置编码器捕获模式，获取编码器位置需要读取CNT值即可，如果需要获取旋转速度或者方向需要间隔时间取CNT并清零

## 相关函数

```c
/**
  * @brief  Configures the TIMx Encoder Interface.
  * @param  TIMx: where x can be  1, 2, 3, 4, 5 or 8 to select the TIM peripheral.
  * @param  TIM_EncoderMode: specifies the TIMx Encoder Mode.
  *   This parameter can be one of the following values:
  *     @arg TIM_EncoderMode_TI1: Counter counts on TI1FP1 edge depending on TI2FP2 level.
  *     @arg TIM_EncoderMode_TI2: Counter counts on TI2FP2 edge depending on TI1FP1 level.
  *     @arg TIM_EncoderMode_TI12: Counter counts on both TI1FP1 and TI2FP2 edges depending
  *                                on the level of the other input.
  * @param  TIM_IC1Polarity: specifies the IC1 Polarity
  *   This parameter can be one of the following values:
  *     @arg TIM_ICPolarity_Falling: IC Falling edge.
  *     @arg TIM_ICPolarity_Rising: IC Rising edge.
  * @param  TIM_IC2Polarity: specifies the IC2 Polarity
  *   This parameter can be one of the following values:
  *     @arg TIM_ICPolarity_Falling: IC Falling edge.
  *     @arg TIM_ICPolarity_Rising: IC Rising edge.
  * @retval None
  */
void TIM_EncoderInterfaceConfig(TIM_TypeDef* TIMx, uint16_t TIM_EncoderMode,
                                uint16_t TIM_IC1Polarity, uint16_t TIM_IC2Polarity)
```

> 定时器编码器接口配置
>
> 参数：
>
> 1、定时器
>
> 2、编码器模式
>
> 3、4通道接口极性

## 配置步骤

### 配置GPIO与定时器RCC开启时钟

```c
RCC_APB1PeriphClockCmd(RCC_APB1Periph_TIM3, ENABLE);
RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);
```

### 配置GPIO为输入模式

```c
GPIO_InitTypeDef GPIO_InitStructure;
GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IPU;
GPIO_InitStructure.GPIO_Pin = GPIO_Pin_6 | GPIO_Pin_7;
GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
GPIO_Init(GPIOA, &GPIO_InitStructure);
```

### 配置定时器的时基单元

```c
TIM_TimeBaseInitTypeDef TIM_TimeBaseInitStructure;
TIM_TimeBaseInitStructure.TIM_ClockDivision = TIM_CKD_DIV1;
TIM_TimeBaseInitStructure.TIM_CounterMode = TIM_CounterMode_Up;
TIM_TimeBaseInitStructure.TIM_Period = 65536 - 1;		//ARR
TIM_TimeBaseInitStructure.TIM_Prescaler = 1 - 1;		//PSC
TIM_TimeBaseInitStructure.TIM_RepetitionCounter = 0;
TIM_TimeBaseInit(TIM3, &TIM_TimeBaseInitStructure);
```

### 配置定时器输入捕获单元

```c
TIM_ICInitTypeDef TIM_ICInitStructure;
TIM_ICStructInit(&TIM_ICInitStructure);
TIM_ICInitStructure.TIM_Channel = TIM_Channel_1;
TIM_ICInitStructure.TIM_ICFilter = 0xF;
TIM_ICInit(TIM3, &TIM_ICInitStructure);
TIM_ICInitStructure.TIM_Channel = TIM_Channel_2;
TIM_ICInitStructure.TIM_ICFilter = 0xF;
TIM_ICInit(TIM3, &TIM_ICInitStructure);
```

### 配置编码器捕获模式

```c
TIM_EncoderInterfaceConfig(TIM3, TIM_EncoderMode_TI12, TIM_ICPolarity_Rising, TIM_ICPolarity_Rising);
```

修改其中一个极性记录的方向反向

### 开启定时器

```c
TIM_Cmd(TIM3, ENABLE);
```

