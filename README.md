# 迷你游戏机-SNAKE

基于 Seeed Studio XIAO ESP32-S3 Plus 和 1.47 英寸触摸屏的霓虹风格 Snake 小游戏。

## Features

- Seeed_GFX2 v1.0.0
- 172×320 JD9853A 彩色屏幕
- AXS5106L 电容触摸 D-pad
- IMU 倾斜控制
- XIAO 实体按键控制
- 霓虹网格、蛇身高亮、食物发光效果
- 标题页和 Game Over 视觉效果
- 墙壁穿越、分数和速度递增

## Hardware

- XIAO ESP32-S3 Plus
- XIAO 1.47 Inch Touch Display
- Arduino ESP32 core 3.3.11
- Seeed_GFX2 1.0.0

## Build

Open `xiao_esp32s3_147_snake/xiao_esp32s3_147_snake.ino` with Arduino IDE.

Board: `XIAO_ESP32S3_Plus`

The project uses the GFX2 panel configuration:

- `Board_XIAO_1inch47_Touch_Display<D17, D18>`
- `Config_Seeed_1inch47_Touch_JD9853A`

## Controls

- USR1 / D19: pause
- USR2 / D15: start, restart, or rotate direction when IMU is unavailable
- Touch D-pad: steer
- Tilt: steer with the onboard IMU