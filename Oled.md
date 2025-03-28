下面是API,以及使用案例
## (1) 初始化过程

- **拷贝库文件：将oled.c、font.c文件拷贝到Core -> Src目录下，将oled.h、font.h文件拷贝到Core -> Inc目录下。
- **在main.c中添加include：**

```
#include "string.h"#include "oled.h"#include "stdio.h"
```

- **初始化OLED：**

```
HAL_Delay(20); // 单片机启动比OLED上电快,需要延迟等待一下OLED_Init(); // 初始化OLED
```

## (2) 显示函数

- **将缓存内容更新到屏幕显示：**

> [!IMPORTANT]
> **任何操作都需要调用此函数才能显示到屏幕上，否则只是改变显示缓冲区**

```
OLED_ShowFrame();
```

- **新建空白缓冲区：**

> 注意：**只存在一个Frame**，NewFrame后之前的内容将被清空

```c
OLED_NewFrame();
```

- **显示一个像素点：**
    - `uint8_t x` 横坐标
    - `uint8_t y` 纵坐标
    - `OLED_ColorMode mode` 颜色模式
        - `OLED_COLOR_NORMAL` 正常
        - `OLED_COLOR_REVERSE` 反色

```c
void OLED_SetPixel(uint8_t x, uint8_t y, OLED_ColorMode color);
```
示例
```c
OLED_NewFrame(); 
// 新建一个空白缓冲区
OLED_SetPixel(0, 0, OLED_COLOR_NORMAL); 
// 在(0, 0)处显示一个黑色的像素点
OLED_ShowFrame(); 
// 将缓冲区内容显示到屏幕上
```

- **显示字符串**
    - `uint8_t x` 纵坐标 [0, 127]
    - `uint8_t y` 纵坐标 [0, 63]
    - `char *str` 要显示的字符串
    - `const Font *font` 字体
    - `OLED_ColorMode color` 颜色模式

```c
void OLED_PrintString(uint8_t x, uint8_t y, char *str, const Font *font, OLED_ColorMode color);
```

```c
// 示例
OLED_NewFrame(); 
// 新建一个空白缓冲区
OLED_PrintString(0, 22, "B站-KEYSKING", &font16x16, OLED_COLOR_NORMAL); 
// 中文、英文、符号混合显示
OLED_ShowFrame(); 
// 将缓冲区内容显示到屏幕上
```

- **绘制直线：**
    - `uint8_t x1` 直线起点横坐标 [0, 127]
    - `uint8_t y1` 直线起点纵坐标 [0, 63]
    - `uint8_t x2` 直线终点横坐标 [0, 127]
    - `uint8_t y2` 直线终点纵坐标 [0, 63]
    - `OLED_ColorMode color` 颜色模式

```c
void OLED_DrawLine(uint8_t x1, uint8_t y1, uint8_t x2, uint8_t y2, OLED_ColorMode color);
```

```c
// 示例
OLED_NewFrame(); 
// 新建一个空白缓冲区
OLED_DrawLine(0, 0, 127, 63, OLED_COLOR_NORMAL); 
// 从左上角到右下角绘制一条直线
OLED_ShowFrame(); // 将缓冲区内容显示到屏幕上
```

- **绘制（空心）矩形：**
    - `uint8_t x` 起始点横坐标 [0, 127]
    - `uint8_t y` 起始点纵坐标 [0, 63]
    - `uint8_t w` 矩形宽度 [0, 127]
    - `uint8_t h` 矩形高度 [0, 63]
    - `OLED_ColorMode color` 颜色模式

```
void OLED_DrawRectangle(uint8_t x, uint8_t y, uint8_t w, uint8_t h, OLED_ColorMode color);
```

```
// 示例OLED_NewFrame(); // 新建一个空白缓冲区OLED_DrawRectangle(32, 16, 63, 31, OLED_COLOR_NORMAL); // 绘制一个空心矩形OLED_ShowFrame(); // 将缓冲区内容显示到屏幕上
```

- **绘制（填充）矩形：**
    - `uint8_t x` 起始点横坐标 [0, 127]
    - `uint8_t y` 起始点纵坐标 [0, 63]
    - `uint8_t w` 矩形宽度 [0, 127]
    - `uint8_t h` 矩形高度 [0, 63]
    - `OLED_ColorMode color` 颜色模式

```
void OLED_DrawFilledRectangle(uint8_t x, uint8_t y, uint8_t w, uint8_t h, OLED_ColorMode color);
```

```
// 示例OLED_NewFrame(); // 新建一个空白缓冲区OLED_DrawFilledRectangle(32, 16, 63, 31, OLED_COLOR_NORMAL); // 绘制一个填充矩形OLED_ShowFrame(); // 将缓冲区内容显示到屏幕上
```

- **绘制（空心）圆形：**
    
    > 此函数使用Bresenham算法绘制圆
    
    - `uint8_t x` 圆心横坐标 [0, 127]
    - `uint8_t y` 圆心纵坐标 [0, 63]
    - `uint8_t r` 圆的半径 [0, 63]
    - `OLED_ColorMode color` 颜色模式

```
void OLED_DrawCircle(uint8_t x, uint8_t y, uint8_t r, OLED_ColorMode color);
```

```
// 示例OLED_NewFrame(); // 新建一个空白缓冲区OLED_DrawCircle(64, 32, 16, OLED_COLOR_NORMAL); // 绘制一个空心圆形OLED_ShowFrame(); // 将缓冲区内容显示到屏幕上
```

- **绘制（填充）圆形：**
    
    > 此函数使用Bresenham算法绘制圆
    
    - `uint8_t x` 圆心横坐标 [0, 127]
    - `uint8_t y` 圆心纵坐标 [0, 63]
    - `uint8_t r` 圆的半径 [0, 63]
    - `OLED_ColorMode color` 颜色模式

```
void OLED_DrawFilledCircle(uint8_t x, uint8_t y, uint8_t r, OLED_ColorMode color);
```

```
// 示例OLED_NewFrame(); // 新建一个空白缓冲区OLED_DrawFilledCircle(64, 32, 16, OLED_COLOR_NORMAL); // 绘制一个填充圆形OLED_ShowFrame(); // 将缓冲区内容显示到屏幕上
```

- **绘制（空心）三角形：**
    - `uint8_t x1` 第一个点横坐标 [0, 127]
    - `uint8_t y1` 第一个点纵坐标 [0, 63]
    - `uint8_t x2` 第二个点横坐标 [0, 127]
    - `uint8_t y2` 第二个点纵坐标 [0, 63]
    - `uint8_t x3` 第三个点横坐标 [0, 127]
    - `uint8_t y3` 第三个点纵坐标 [0, 63]
    - `OLED_ColorMode color` 颜色模式

```
void OLED_DrawTriangle(uint8_t x1, uint8_t y1, uint8_t x2, uint8_t y2, uint8_t x3, uint8_t y3, OLED_ColorMode color);
```

```
// 示例OLED_NewFrame(); // 新建一个空白缓冲区OLED_DrawTriangle(64, 0, 0, 63, 127, 63, OLED_COLOR_NORMAL); // 在屏幕中心绘制一个空心三角形OLED_ShowFrame(); // 将缓冲区内容显示到屏幕上
```

- **绘制（填充）三角形：**
    - `uint8_t x1` 第一个点横坐标 [0, 127]
    - `uint8_t y1` 第一个点纵坐标 [0, 63]
    - `uint8_t x2` 第二个点横坐标 [0, 127]
    - `uint8_t y2` 第二个点纵坐标 [0, 63]
    - `uint8_t x3` 第三个点横坐标 [0, 127]
    - `uint8_t y3` 第三个点纵坐标 [0, 63]
    - `OLED_ColorMode color` 颜色模式

```
void OLED_DrawFilledTriangle(uint8_t x1, uint8_t y1, uint8_t x2, uint8_t y2, uint8_t x3, uint8_t y3, OLED_ColorMode color);
```

```
// 示例OLED_NewFrame(); // 新建一个空白缓冲区OLED_DrawFilledTriangle(64, 0, 0, 63, 127, 63, OLED_COLOR_NORMAL); // 在屏幕中心绘制一个填充三角形OLED_ShowFrame(); // 将缓冲区内容显示到屏幕上
```

- **绘制（空心）椭圆：**
    - `uint8_t x` 圆心横坐标 [0, 127]
    - `uint8_t y` 圆心纵坐标 [0, 63]
    - `uint8_t a` 长轴长度
    - `uint8_t b` 短轴长度
    - `OLED_ColorMode color` 颜色模式

```
void OLED_DrawEllipse(uint8_t x, uint8_t y, uint8_t a, uint8_t b, OLED_ColorMode color)
```

```
// 示例OLED_NewFrame(); // 新建一个空白缓冲区OLED_DrawEllipse(64, 32, 30, 15, OLED_COLOR_NORMAL); // 绘制一个空心椭圆OLED_ShowFrame(); // 将缓冲区内容显示到屏幕上
```

- **显示BMP图像：**
    - `uint8_t x` 起始点横坐标 [0, 127]
    - `uint8_t y` 起始点纵坐标 [0, 63]
    - `const Image *img` 图像数据
    - `OLED_ColorMode color` 颜色模式

```
void OLED_DrawImage(uint8_t x, uint8_t y, const Image *img, OLED_ColorMode color);
```

```
// 示例OLED_NewFrame(); // 新建一个空白缓冲区OLED_DrawImage((128 - (bilibiliImg.w)) / 2, 0, &bilibiliImg, OLED_COLOR_NORMAL); // 显示名为bilibiliImg的BMP图像OLED_ShowFrame(); // 将缓冲区内容显示到屏幕上
```

## (3) 取字模

**文字：**分为两类：ASCII字符、中文字符。

- ASCII字符：font.c 库中已包含，可以直接调用
    
    - 包含 8、12、16、24 四种大小的字体
        
    - 每种字体均有95个可打印字符
        
        > 具体请参考 [波特律动 串口助手 (baud-dance.com)](https://serial.baud-dance.com/) 中的ASCII码表
        
- 中文字符：中文字符数量多，STM32内部Flash无法全部存储，因此需要根据使用的字符来取字模。
    
    - 在线取字模工具：[https://led.baud-dance.com/](https://led.baud-dance.com/)
    - 使用方法
        - 输入所有需要用到的中文字符后，点击右下角复制按钮，复制字模
        - 将**字模代码**粘贴到`font.c`中，并在`font.h`中添加对应的声明
        - 使用OLED_PrintString函数, 传入对应的字体结构体即可显示中文字符

**图片：**支持BMP格式单色位图

- 在线取图模工具：[https://led.baud-dance.com/](https://led.baud-dance.com/)