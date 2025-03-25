本篇文章为第二次学习stm32所记录，很后悔当初没有记录下来一些API
先记录HAL库，后续在记录标准库的学习
>[!NOTE]
>1.记得保存全部,才会保存项目配置:**save all**
# 简单外设（HAL）
## GPIO
### 1. `HAL_GPIO_WritePin`
**功能**: 设置 GPIO 引脚的电平状态（高电平/低电平）。
**原型**:
```c
void HAL_GPIO_WritePin(GPIO_TypeDef *GPIOx, uint16_t GPIO_Pin, GPIO_PinState PinState);
```
**示例**:
```c
HAL_GPIO_WritePin(GPIOA, GPIO_PIN_5, GPIO_PIN_SET);  // 设置 GPIOA Pin 5 高电平
```

---

### 2. `HAL_GPIO_TogglePin`
**功能**: 切换 GPIO 引脚状态。
**原型**:
```c
void HAL_GPIO_TogglePin(GPIO_TypeDef *GPIOx, uint16_t GPIO_Pin);
```
**示例**:
```c
HAL_GPIO_TogglePin(GPIOB, GPIO_PIN_0);  // 切换 GPIOB Pin 0 状态
```

---

### 3. `HAL_GPIO_ReadPin`
**功能**: 读取 GPIO 引脚电平状态。
**原型**:
```c
GPIO_PinState HAL_GPIO_ReadPin(GPIO_TypeDef *GPIOx, uint16_t GPIO_Pin);
```
**示例**:
```c
GPIO_PinState pinState = HAL_GPIO_ReadPin(GPIOC, GPIO_PIN_13);  // 读取 GPIOC Pin 13 电平状态
```

---

### 4. `HAL_GPIO_Init`
**功能**: 初始化 GPIO 引脚（配置引脚模式、上拉/下拉、电平速度等）。
**原型**:
```c
void HAL_GPIO_Init(GPIO_TypeDef *GPIOx, GPIO_InitTypeDef *GPIO_Init);
```
**示例**:
```c
GPIO_InitTypeDef GPIO_InitStruct = {0};
GPIO_InitStruct.Pin = GPIO_PIN_5;
GPIO_InitStruct.Mode = GPIO_MODE_OUTPUT_PP;
GPIO_InitStruct.Pull = GPIO_NOPULL;
GPIO_InitStruct.Speed = GPIO_SPEED_FREQ_LOW;
HAL_GPIO_Init(GPIOA, &GPIO_InitStruct);  // 初始化 GPIOA Pin 5 为推挽输出模式
```

---

### 5. `HAL_GPIO_DeInit`
**功能**: 反初始化 GPIO 引脚，恢复默认状态。
**原型**:
```c
void HAL_GPIO_DeInit(GPIO_TypeDef *GPIOx, uint32_t GPIO_Pin);
```
**示例**:
```c
HAL_GPIO_DeInit(GPIOB, GPIO_PIN_0);  // 反初始化 GPIOB Pin 0
```

---

### 常用 GPIO 配置选项

- **模式**:  
  - `GPIO_MODE_OUTPUT_PP`：推挽输出模式  
  - `GPIO_MODE_INPUT`：输入模式

- **上拉/下拉**:  
  - `GPIO_NOPULL`：无上拉下拉  
  - `GPIO_PULLUP`：上拉  
  - `GPIO_PULLDOWN`：下拉

- **速度**:  
  - `GPIO_SPEED_FREQ_LOW`：低速  
  - `GPIO_SPEED_FREQ_HIGH`：高速


## 串口
在 STM32 的 HAL 库中，串口通信通过三种方式：**堵塞模式**、**中断模式** 和 **DMA模式**。以下是详细的 API 介绍：
>[!NOTE]
> 1.DMA是有一个传输过半中断的,记得关闭:
> `__HAL_DMA_DISABLE_IT(&hdma_usart_rx, DMA_IT_HT);`  // 禁用 DMA 半传输中断
> 2. 有关蓝牙部分可以看[[蓝牙]]
> 3. 脑子别进水接反了


***Cubemx 参数:***
![[Pasted image 20240916142916.png]]
- **Baud Rate (115200 Bits/s)**: 数据传输速率为 115200 位/秒。
- **Word Length (8 Bits)**: 每个数据帧有 8 位数据。
- **Parity (None)**: 无校验位。
- **Stop Bits (1)**: 每个数据帧有 1 位停止位。
- **Data Direction (Receive and Transmit)**: 启用接收和发送功能。
- **Over Sampling (16 Samples)**: 采用 16 倍过采样模式。
***流程:***:
**使能串口(中断) --> 创建数组用于接收 --> 解析**
>可以使用串口重定向(记得勾选 microLib):
```c
int fputc(int ch, FILE * str)
{

	HAL_UART_Transmit(&huart1, (uint8_t *)&ch, 1, 10);
	return ch;
}
```
### 1. **堵塞模式**
#### `HAL_UART_Transmit()`
- **功能**: 发送数据，直到数据发送完成或超时，MCU 在此期间被阻塞。
- **原型**:
```c
HAL_StatusTypeDef HAL_UART_Transmit(UART_HandleTypeDef *huart, uint8_t *pData, uint16_t Size, uint32_t Timeout);
```
- **参数**:
  - `huart`: 串口句柄（UART 配置结构体）。
  - `pData`: 要发送的数据指针。
  - `Size`: 发送数据的字节数。
  - `Timeout`: 等待的超时时间。

#### `HAL_UART_Receive()`
- **功能**: 接收数据，直到数据接收完成或超时，期间 MCU 被阻塞。
- **原型**:
```c
HAL_StatusTypeDef HAL_UART_Receive(UART_HandleTypeDef *huart, uint8_t *pData, uint16_t Size, uint32_t Timeout);
```

### 2. **中断模式**
#### `HAL_UART_Transmit_IT()`
- **功能**: 非阻塞发送数据，通过中断方式传输。
- **原型**:
```c
HAL_StatusTypeDef HAL_UART_Transmit_IT(UART_HandleTypeDef *huart, uint8_t *pData, uint16_t Size);
```

#### `HAL_UART_Receive_IT()`
- **功能**: 非阻塞接收数据，通过中断方式接收。
- **原型**:
```c
HAL_StatusTypeDef HAL_UART_Receive_IT(UART_HandleTypeDef *huart, uint8_t *pData, uint16_t Size);
```

- **注意**: 传输和接收完成后，分别调用回调函数 `HAL_UART_TxCpltCallback()` 和 `HAL_UART_RxCpltCallback()`。

### 3. **DMA模式**
#### `HAL_UART_Transmit_DMA()`
- **功能**: 使用 DMA 非阻塞发送数据。DMA 自动完成数据传输，不占用 MCU 大量资源。
- **原型**:
```c
HAL_StatusTypeDef HAL_UART_Transmit_DMA(UART_HandleTypeDef *huart, uint8_t *pData, uint16_t Size);
```

#### `HAL_UART_Receive_DMA()`
- **功能**: 使用 DMA 非阻塞接收数据。
- **原型**:
```c
HAL_StatusTypeDef HAL_UART_Receive_DMA(UART_HandleTypeDef *huart, uint8_t *pData, uint16_t Size);
```

- **注意**: 传输和接收完成后，调用相同的回调函数 `HAL_UART_TxCpltCallback()` 和 `HAL_UART_RxCpltCallback()`。

### 4. **其他相关函数**
- **停止操作**:
  - `HAL_UART_Abort()`: 停止所有 UART 操作。
  - `HAL_UART_AbortTransmit()` 和 `HAL_UART_AbortReceive()` 用于分别中止发送和接收操作。
  
- **状态查询**:
  - `HAL_UART_GetState()`: 查询 UART 当前状态。

### 5. **回调函数**
当使用中断或 DMA 模式时，传输完成、接收完成或发生错误时，都会触发相应的回调函数：
- `HAL_UART_TxCpltCallback()`: 发送完成时的回调函数。
- `HAL_UART_RxCpltCallback()`: 接收完成时的回调函数。
- `HAL_UART_ErrorCallback()`: 发生错误时的回调函数。

### 6. **使用示例**
#### 0.串口重定向
```c
//单串口,记得开mircoLib
#include "string.h"

int fputc(int ch, FILE * str)
{
	HAL_UART_Transmit(&huart1, (uint8_t *)&ch, 1, 10);
	return ch;
}
```

#### 1. 堵塞模式发送数据
```c
uint8_t data[] = "Hello UART!";
HAL_UART_Transmit(&huart1, data, sizeof(data), HAL_MAX_DELAY);
```

#### 2. 中断模式接收数据
```c
uint8_t buffer[10];
HAL_UART_Receive_IT(&huart1, buffer, 10);

// 完成接收后执行回调函数
void HAL_UART_RxCpltCallback(UART_HandleTypeDef *huart) {
    // 处理接收到的数据
}
```

#### 3. DMA 模式发送数据
```c
uint8_t data[] = "DMA Transfer!";
HAL_UART_Transmit_DMA(&huart1, data, sizeof(data));
```

#### 4.中断 + 解析
```c
//打开接收中断
HAL_UART_Receive_IT(&huart1, buffer, 1);

//中断回调函数
void HAL_UART_RxCpltCallback(UART_HandleTypeDef *huart)

{
	if (huart > Instance = USART1)
	{
		//定义超时解析
		uart_rx_ticks = uwTick;
		uart_rx_index++;
		HAL_UART_Receive_IT(&huart1, &uart_rx_buffer[uart_rx_index], 1);
	}
}
/*
* @brief 处理UART接收缓冲区中的数据。
* 如果在100ms内没有接收到新的数据，将清空缓冲区。
* @param None
* @retval None
*/
void uart_proc(void)
{
	//如果接收索引为0，说明没有数据需要处理，直接返回
	if(uart_rx_index = 0) return;
	//如果从最后一次接收到数据到现在已经超过100ms
	if(uwTick - uart_rx_ticks > 100) // 100ms内没有收到新的数据
	{
		/*
			...解析部分
		*/
		//清空接收缓冲区，将接收索引置零
		memset(uart_rx_buffer, 0, uart_rx_index);
		uart_rx_index = 0;
		//将UART接收缓冲区指针重置为接收缓冲区的起始位置
		huart1.pRxBuffPtr = uart_rx_buffer;
	}
}
```

#### 5.DMA + 空闲中断
- **添加DMA通道** --> **使能中断**
- 在 main 函数中，使用 `HAL_UARTEx_ReceiveToIdle_DMA` 函数开启不定长数据DMA接收
```c
//首先开启DMA空闲中断模式，关闭传输过半中断
HAL_UARTEx_ReceiveToIdle_DMA(&huart1, rx_data, sizeof(rx_data));  
__HAL_DMA_DISABLE_IT(huart1.hdmarx, DMA_IT_HT);
//编写回调函数
void HAL_UARTEx_RxEventCallback(UART_HandleTypeDef *huart, uint16_t Size)  
{  
	if (huart->Instance == USART3)  
	{  
		// 使用DMA将接收到的数据发送回去  
		HAL_UART_Transmit_DMA(&huart1, rx_data, Size);  
		// 重新启动接收，使用Ex函数，接收不定长数据  
		HAL_UARTEx_ReceiveToIdle_DMA(&huart1, rx_data, sizeof(rx_data));  
		// 关闭DMA传输过半中断（HAL库默认开启，但我们只需要接收完成中断）  
		__HAL_DMA_DISABLE_IT(huart1.hdmarx, DMA_IT_HT);  
	}  
}
```
## 定时器
看这篇文章：[[定时器]]
### API
## 输入捕获

### 编码器模式
编码器模式 在` Cubemx`配置中纯在两个坑人的点
1. **编码器配置** ![image.png](https://murmur-1329567155.cos.ap-guangzhou.myqcloud.com/picture/20250325164807852.png)
	- 这里配置成`Encoder Mode`时，虽然下面配置是*上升沿计数*，但是实际上这里的**上升沿开始计数**， 故产生的脉冲个数需要四分频！！！才是真实的减速比* *n线*
2. **编码器读取值**我们都知道，有可能正转和反转，但是有一点很重要，就是`stm32f103c8t6`的`输入捕获寄存值`为16位即 `uint16_t`
```c
Encoder_count[0] = (short)__HAL_TIM_GET_COUNTER(&htim2); // TIM2 --> L
Encoder_count[1] = -(short)__HAL_TIM_GET_COUNTER(&htim1); // TIM1  --> R
```
>通过这种`uint16_t`向`short`类型的转化，实现反向计数！！！











## PWM
### **波形**
PWM波形是一种方波信号，是高/低电平不断切换的结果，其波形如图所示，这是3种**占空比**不同的波形：

![PWM波形](https://docs.keysking.com/assets/images/PWM%E6%B3%A2%E5%BD%A2-af2e911d28e4b5054be99adde7fa55ae.jpg)
计算：
**占空比计算**: 占空比 = `Pulse / (ARR + 1)`。
- 例如，`ARR = 200`，`Pulse = 100`，占空比为 50%。
PWM的几个关键参数为：
- **频率（Frequency）**：即高低电平切换的速度，切换的速度越快则频率越高，1000Hz的PWM波意味着1秒钟有1000个脉冲
- **占空比（Duty Cycle）**：即每个周期内，高电平所占的宽度
    
    - 例如图中 `50% duty cycle`，即**高/低电平的时间各占50%**；
        
    - 图中的`75% duty cycle`，**高电平占75%，低电平占25%**；
        
    - 图中的`25% duty cycle`，**高电平占25%，低电平占75%**；

![image.png](https://murmur-1329567155.cos.ap-guangzhou.myqcloud.com/picture/20250324131546900.png)

### Cubemx
#### **Counter Settings（计数器设置）**

- **Prescaler (PSC)**: 预分频器，设置时钟分频。当前值 `7199`，即 `72MHz / (7199 + 1) = 10kHz` 计数频率。
- **Counter Mode**: 计数模式，`Up` 表示向上计数。
- **Counter Period (ARR)**: 自动重载值，当前设置为 `200`，意味着 20ms（`200 / 10kHz`）。
- **Internal Clock Division (CKD)**: 内部时钟分频，`No Division` 表示不分频。

#### **PWM Generation（PWM 生成）**
- **Mode**: PWM 模式，`PWM Mode 1` 表示在计数器小于比较值时输出高电平。
- **Pulse**: 控制 PWM 占空比的值，`0` 表示 0% 占空比。
# 基础外设(HAL)
![image.png](https://murmur-1329567155.cos.ap-guangzhou.myqcloud.com/picture/20250324131650160.png)

一些常用的外设**使用**,基础协议部分看**简单外设**
## 旋转编码器
什么是旋转编码器:
1. 增量式:在转动时输出脉冲，转动角度和脉冲数是线性对应的，例如每转1°产生1个脉冲。**它只知道相对当前位置旋转了多少度，而不知道旋转轴的具体位置**。
2. 绝对式:会输出转轴的位置（**绝对角度**），可以视为一种角度传感器。
主流有两种方法:
1. A相下降沿时检测B相的电位,**若为低电平--> B相超前,顺时针**;**若为高电平 --> B相落后,逆时针**
2. 通过STM32的***编码器模式***直接进行检测,hal库的配置方法比较简单
### 配置过程
- - Mode -> Combined Channels设为***Encoder Mode***，使TIM1进入“编码器模式”
- Configuration -> Encoder -> ***Input Filter*** 设为 15，最大程度滤波，可以获得更稳定的效果
随后启动定时器`HAL_TIM_Encoder_Start(&htim1, TIM_CHANNEL_ALL);`
### API
```c
__HAL_TIM_GET_COUNTER(&htim1)
//return a uint16_t 
```
>EC11旋转编码使用20个脉冲表示360°

## OLED(IIC)
使用的是自己之前保存的Oled底层模块,只需要在字库网站上获取字库即可
**配置:**
- Connectivity --> IIC1 --> 打开I2C
- Configuration --> Mster Features --> Mode 设置为 ***Fast Mode***

### 流程
首先把我们的oled所需要的.c文件和.h文件复制进我们的工程,并整理成下面(个人习惯)
![image.png](https://murmur-1329567155.cos.ap-guangzhou.myqcloud.com/picture/20250324131745923.png)

然后就是:
初始化OLED`OLED_Init()` --> **新建空白缓冲区 --> 显示想要显示的 -->刷新缓冲区**
### API
看 [[Oled]]
