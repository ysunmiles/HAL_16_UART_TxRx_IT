# HAL_16_UART_TxRx_IT

## 项目简介

本项目是一个基于 STM32F103 的串口收发演示工程，使用 STM32 HAL 库实现了：

- 通过 USART1 接收串口数据
- 在接收中断回调中回显收到的数据
- 将收到内容实时显示在 0.96 寸 OLED 屏幕上

工程同时使用 STM32CubeMX 生成的初始化代码和 CMake 进行构建，适合学习 HAL 驱动、串口中断和 OLED 驱动控制。

## 主要功能

- 初始化 STM32F103 的系统时钟与外设
- 配置 USART1 为 115200 bps、8 位数据位、1 位停止位、无校验位
- 使用 HAL UART 的中断接收方式（IRQ）处理串口输入
- 使用软件 I2C 驱动 OLED 显示屏
- 上电后输出提示信息，并将收到的数据回显到串口终端

## 硬件说明

- 目标 MCU：STM32F103xB
- 串口：USART1
  - TX：PA9
  - RX：PA10
- OLED 驱动引脚（软件 I2C）
  - SCL：PB8
  - SDA：PB9

## 当前实现说明

项目当前的主流程如下：

1. 初始化系统时钟和外设
2. 初始化 OLED，并清屏显示提示信息
3. 通过串口输出提示字符串：`UART Standby.`
4. 启动 UART 中断接收
5. 每次接收到数据后，程序会将内容回显到串口，并更新 OLED 的显示内容

当前代码中接收缓冲区长度为 5 字节，适合测试短串口数据。

## 代码结构

- Core/Src/main.c：主程序入口，完成系统初始化、串口收发和 OLED 显示流程
- Core/Src/usart.c / Core/Inc/usart.h：USART1 配置与 HAL 初始化
- Core/Src/OLED.c / Core/Inc/OLED.h：OLED 驱动与显示函数
- Core/Inc/OLED_Font.h：OLED 字库数据
- config.ioc：STM32CubeMX 工程配置文件
- CMakeLists.txt：根目录 CMake 构建脚本
- CMakePresets.json：CMake 预设配置
- cmake/user_sources.cmake：自定义源码与头文件路径注册
- Drivers/：STM32 HAL 和 CMSIS 相关驱动文件

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
3. 等待串口输入
4. 每收到一段数据，就将其回显到串口，并显示在 OLED 上

## 注意事项

- 当前默认波特率为 115200
- 接收缓冲区长度为固定 5 字节，适合测试较短文本
- 如使用不同的硬件接线方式，需要同步修改对应引脚配置
