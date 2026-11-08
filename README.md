# STM32 Delay 函数模块

基于 **SysTick 定时器** 实现的 STM32F10x 延时模块，提供微秒、毫秒、秒三级延时。

## 文件说明

| 文件 | 说明 |
|------|------|
| `Delay.c` | 延时函数实现 |
| `Delay.h` | 对外接口声明 |

## API

```c
void Delay_us(uint32_t us);   // 微秒级延时，范围 0 ~ 233015
void Delay_ms(uint32_t ms);   // 毫秒级延时，范围 0 ~ 4294967295
void Delay_s(uint32_t s);     // 秒级延时，范围 0 ~ 4294967295
```

## 使用方法

1. 把 `Delay.c` 和 `Delay.h` 加入 Keil 工程（`Delay.c` 加入源码组，`Delay.h` 加入头文件路径）
2. 在需要的地方包含头文件：

```c
#include "Delay.h"

int main(void)
{
    while (1)
    {
        GPIO_SetBits(GPIOA, GPIO_Pin_0);   // LED 亮
        Delay_ms(500);
        GPIO_ResetBits(GPIOA, GPIO_Pin_0); // LED 灭
        Delay_ms(500);
    }
}
```

## 实现原理

`Delay_us` 直接操作 SysTick 的 `LOAD` / `VAL` / `CTRL` 寄存器：

- 时钟源取 HCLK（72 MHz），装载值 = `72 * us`
- 启动计数并轮询 `COUNTFLAG` 等待计时结束
- 结束后关闭定时器

`Delay_ms` / `Delay_s` 通过循环调用下一级函数实现。

## 注意

- 假定系统主频为 **72 MHz**，若主频不同需修改 `Delay_us` 里的 `72` 系数
- SysTick 被本模块占用，其他功能（如 RTOS）若依赖 SysTick 需改用其他定时器

## 开发环境

- MCU：STM32F10x
- IDE：Keil MDK-ARM
- 标准外设库：`stm32f10x.h`
