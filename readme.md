# HAL_15_UART_TxRx

## 项目简介

本项目基于 STM32F103 系列 MCU 和 STM32 HAL 库，演示了一个典型的串口收发应用：

- 通过 USART1 接收外部串口输入数据
- 将收到的数据回传到串口终端
- 同时把接收到的数据显示在 OLED 屏幕上

工程使用 STM32CubeMX 生成的初始化代码，并通过 CMake 进行构建。

## 主要功能

- 初始化 STM32F103 的系统时钟与外设
- 配置 USART1 为 115200 bps、8 位数据位、1 位停止位、无校验位
- 使用 HAL UART 接口进行串口发送与接收
- 使用软件 I2C 驱动 0.96 英寸 OLED 显示屏
- 在 OLED 上显示提示信息和接收到的串口数据

## 硬件说明

- 目标 MCU：STM32F103xB
- 串口：USART1
  - TX：PA9
  - RX：PA10
- OLED 驱动引脚（软件 I2C）
  - SCL：PB8
  - SDA：PB9

## 代码结构

- `Core/Src/main.c`：主程序入口，完成系统初始化、串口收发、OLED 显示流程
- `Core/Src/usart.c` / `Core/Inc/usart.h`：USART1 配置与 HAL 初始化
- `Core/Src/OLED.c` / `Core/Inc/OLED.h`：OLED 驱动与显示函数
- `Core/Inc/OLED_Font.h`：OLED 字库数据
- `config.ioc`：STM32CubeMX 工程配置文件
- `CMakeLists.txt`：根目录 CMake 构建脚本
- `CMakePresets.json`：CMake 预设配置
- `cmake/user_sources.cmake`：自定义源码与头文件路径注册
- `Drivers/`：STM32 HAL 与 CMSIS 相关驱动文件

## 构建环境

建议使用以下工具链：

- CMake 3.22 及以上
- Ninja
- ARM GCC 工具链（如 `arm-none-eabi-gcc`）

## 构建步骤

在项目根目录执行：

```bash
cmake --preset Debug
cmake --build --preset Debug
```

生成的固件文件可通过 ST-Link 或其他烧录工具下载到开发板。

## 运行效果

上电后，程序会：

1. 初始化 OLED 并清屏
2. 向串口输出提示信息 `UART Standby.`
3. 循环等待串口输入
4. 每收到一段数据，就将其回显到串口，并显示在 OLED 上

## 注意事项

- 当前程序默认使用 USART1 的波特率为 115200
- 接收数据长度为固定数组长度，建议通过串口终端按实际需要发送内容
- 如果使用不同的硬件连接方式，需要同步修改对应引脚配置
