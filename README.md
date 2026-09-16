# 迷你游戏机-SNAKE

[![中文](https://img.shields.io/badge/语言-中文-00A98F?style=for-the-badge)](README.md) [![English](https://img.shields.io/badge/Language-English-4866FF?style=for-the-badge)](README.en.md)

一台基于 **Seeed Studio XIAO ESP32-S3 Plus + 1.47 英寸触摸显示板**的迷你贪吃蛇游戏机。使用 **Seeed_GFX2 v1.0.0** 绘制霓虹风格界面，支持触摸方向键、倾斜操控和实体按键。

## 游戏与视觉效果

- 172×320 竖屏；顶部积分、17×18 游戏网格、底部触摸方向键。
- 吃食物加 1 分、蛇身增长，移动间隔从 500 ms 逐步降至 150 ms。
- 穿越屏幕边界会从另一侧出现；撞到身体则游戏结束；禁止直接反向。
- 霓虹网格、蛇头高亮、食物呼吸变色、标题及结束画面。
- 第二版加入吃食物粒子、`+1` 飘字和扩散光环，特效期间绘制蛇尾光晕。
- 已移除吃食物时的白色边框和积分区域黄色横线。

当前没有最高分持久保存、音效、Combo、开场倒计时或滑动手势控制。顶部积分只属于当前这一局。

## 硬件与依赖

| 项目 | 本项目使用的配置 |
| --- | --- |
| 主控 | Seeed Studio XIAO ESP32-S3 Plus |
| 显示板 | XIAO 1.47 Inch Touch Display |
| 屏幕 | JD9853A，172×320，SPI |
| 触摸 | AXS5106L，I2C，项目内附驱动 |
| 图形库 | **Seeed_GFX2 1.0.0**，头文件 `Seeed_GFX.h` |
| Arduino ESP32 开发板包 | **esp32 3.3.11** |
| 开发板 FQBN | `esp32:esp32:XIAO_ESP32S3_Plus` |

程序通过 Wire 直接访问 IMU，包含 QMI/LSM 系列探测逻辑，不需要额外安装 IMU 库。具体传感器是否可用取决于所接硬件和初始化结果。

### 引脚

| 功能 | XIAO 引脚 |
| --- | --- |
| LCD CS / DC | D2 / D3 |
| LCD SCK / MOSI | D8 / D10 |
| LCD 与触摸共用复位 | D17 |
| LCD 背光 | D18 |
| I2C SDA / SCL | D4 / D5 |
| 触摸中断 | D7 |
| USR1 / USR2 | D19 / D15 |

显示初始化使用 `Board_XIAO_1inch47_Touch_Display<D17, D18>` 和 `Config_Seeed_1inch47_Touch_JD9853A`，无需旧版 TFT_eSPI 的 `driver.h` 配置。

## 操作方法

| 操作 | 功能 |
| --- | --- |
| 标题页按 USR2 | 开始新游戏 |
| 游戏中按 USR1 | 暂停；再次按下继续 |
| Game Over 后按 USR2 | 立即重开，也接受按住状态 |
| 点击底部方向键 | 向上、下、左、右转向；持续按住不会连续触发 |
| 倾斜板子 | 根据倾斜幅度较大的轴转向 |
| IMU 初始化失败时，游戏中按 USR2 | 顺时针转向 |

触摸和倾斜同时启用；倾斜输入在触摸之后处理，同一轮可能覆盖触摸方向。想主要使用触摸时，请将板子尽量放平。

## Arduino IDE 安装与烧录

1. 下载本仓库并解压，保留以下文件在同一个 sketch 文件夹中。
2. 在开发板管理器安装 `esp32` **3.3.11**，选择 **XIAO_ESP32S3_Plus**。
3. 安装 [Seeed_GFX2](https://github.com/Seeed-Studio/Seeed_GFX2) **1.0.0**。在编译日志确认实际选中的是 `Seeed_GFX2@1.0.0`；不要误用旧版 `Seeed_GFX` 或另一份 GFX2。
4. 打开 `xiao_esp32s3_147_snake/xiao_esp32s3_147_snake.ino`。
5. 用 USB 数据线连接板卡，选择对应串口，点击编译、上传。当前版本已在 PSRAM 关闭配置下编译和烧录。
6. 上电进入标题页，按 USR2 开始。

```text
xiao_esp32s3_147_snake/
├── xiao_esp32s3_147_snake.ino
├── axs5106l_device.h
└── axs5106l_device.cpp
```

## Arduino CLI

以下命令从仓库根目录执行。将 `COM33` 替换为 `board list` 中实际连接板卡的串口。

```sh
arduino-cli board list
arduino-cli compile --fqbn esp32:esp32:XIAO_ESP32S3_Plus --build-path ./build ./xiao_esp32s3_147_snake
arduino-cli upload --port COM33 --fqbn esp32:esp32:XIAO_ESP32S3_Plus --input-dir ./build
```

如果安装了多份图形库，编译时可追加 `--library "Seeed_GFX2 1.0.0 的库根目录"` 明确指定依赖。

## 常见问题

- **找不到 `axs5106l_device.h`**：确认 `.ino`、`.h` 和 `.cpp` 在同一层目录。Arduino IDE 将 `.ino` 移入同名文件夹后，需把两个触摸驱动文件一并放进去。
- **开局或重开后立即 Game Over**：请使用当前代码。已修复初始蛇身排列与向右移动方向相反的问题。
- **USR2 重开无响应**：当前版已取消 Game Over 的一秒冷却；核对按钮为 USR2 / D15。
- **上传串口错误或被占用**：重新运行 `board list`，并关闭占用串口的监视器。

## 验证状态

当前代码已使用 ESP32 core 3.3.11 与 Seeed_GFX2 1.0.0 编译，并通过 CLI 烧录、Flash 校验。最近一次编译程序占用 **353,591 bytes**，全局变量占用 **24,836 bytes**。用户已在实物上确认最近的开局/重开修复可用；这些记录不代表所有游戏边界情况均已测试。
