# Mini Game Console — SNAKE

[![中文](https://img.shields.io/badge/语言-中文-00A98F?style=for-the-badge)](README.md) [![English](https://img.shields.io/badge/Language-English-4866FF?style=for-the-badge)](README.en.md)

A miniature Snake console built with **Seeed Studio XIAO ESP32-S3 Plus and the 1.47-inch Touch Display**. **Seeed_GFX2 v1.0.0** renders the neon interface, with touch D-pad, tilt, and physical button controls.

## Gameplay and visuals

- 172×320 portrait display: score header, 17×18 playfield, and touch D-pad below.
- Each food adds one point and grows the snake. The movement interval decreases from 500 ms to a minimum of 150 ms.
- Crossing an edge wraps to the opposite side. Hitting the body ends the game; direct reversal is blocked.
- Neon grid, highlighted snake head, pulsing food colors, title and Game Over screens.
- Version 2 adds food particles, floating `+1` text, an expanding ring, and a tail halo drawn during the effect.
- The food-triggered white frame and yellow line over the score header have been removed.

There is currently no persistent high score, audio, Combo system, start countdown, or swipe control. The score applies only to the current game.

## Hardware and dependencies

| Item | Configuration used |
| --- | --- |
| MCU board | Seeed Studio XIAO ESP32-S3 Plus |
| Display board | XIAO 1.47 Inch Touch Display |
| LCD | JD9853A, 172×320, SPI |
| Touch | AXS5106L, I2C; driver included in the sketch |
| Graphics library | **Seeed_GFX2 1.0.0**, header `Seeed_GFX.h` |
| Arduino ESP32 board package | **esp32 3.3.11** |
| Board FQBN | `esp32:esp32:XIAO_ESP32S3_Plus` |

The sketch accesses the IMU directly through Wire and includes QMI/LSM-family detection logic. No additional IMU library is required. Sensor availability depends on the connected hardware and initialization result.

### Pin mapping

| Function | XIAO pin |
| --- | --- |
| LCD CS / DC | D2 / D3 |
| LCD SCK / MOSI | D8 / D10 |
| Shared LCD/touch reset | D17 |
| LCD backlight | D18 |
| I2C SDA / SCL | D4 / D5 |
| Touch interrupt | D7 |
| USR1 / USR2 | D19 / D15 |

Display initialization uses `Board_XIAO_1inch47_Touch_Display<D17, D18>` and `Config_Seeed_1inch47_Touch_JD9853A`. The legacy TFT_eSPI `driver.h` configuration is not required.

## Controls

| Action | Result |
| --- | --- |
| Press USR2 on the title screen | Start a new game |
| Press USR1 during gameplay | Pause; press again to resume |
| Press USR2 after Game Over | Restart immediately; holding the button is also accepted |
| Tap the touch D-pad | Turn up, down, left, or right; holding does not repeatedly trigger turns |
| Tilt the board | Steer along the axis with the greater tilt |
| Press USR2 during gameplay when IMU initialization failed | Turn clockwise |

Touch and tilt are active together. Tilt is processed after touch and can override its direction in the same loop. Keep the board approximately level when primarily using touch controls.

## Install and upload with Arduino IDE

1. Download and extract this repository. Keep all three sketch files together as shown below.
2. Install the `esp32` board package **3.3.11** in Boards Manager and select **XIAO_ESP32S3_Plus**.
3. Install [Seeed_GFX2](https://github.com/Seeed-Studio/Seeed_GFX2) **1.0.0**. Confirm that the build log selects `Seeed_GFX2@1.0.0`, rather than the old `Seeed_GFX` library or another GFX2 installation.
4. Open `xiao_esp32s3_147_snake/xiao_esp32s3_147_snake.ino`.
5. Connect the board with a USB data cable, select its port, then compile and upload. This version was compiled and uploaded with PSRAM disabled.
6. The title screen appears at startup. Press USR2 to play.

```text
xiao_esp32s3_147_snake/
├── xiao_esp32s3_147_snake.ino
├── axs5106l_device.h
└── axs5106l_device.cpp
```

## Arduino CLI

Run these commands from the repository root. Replace `COM33` with the connected board's port reported by `board list`.

```sh
arduino-cli board list
arduino-cli compile --fqbn esp32:esp32:XIAO_ESP32S3_Plus --build-path ./build ./xiao_esp32s3_147_snake
arduino-cli upload --port COM33 --fqbn esp32:esp32:XIAO_ESP32S3_Plus --input-dir ./build
```

If multiple graphics libraries are installed, append `--library "path/to/Seeed_GFX2-1.0.0"` to the compile command to explicitly select the library root.

## Troubleshooting

- **Missing `axs5106l_device.h`:** keep the `.ino`, `.h`, and `.cpp` in the same directory. If Arduino IDE moves the `.ino` into a matching folder, move both touch driver files there too.
- **Immediate Game Over on start or restart:** use the current source. The initial body order has been corrected to match the initial rightward direction.
- **USR2 does not restart:** the current version removes the one-second Game Over cooldown. Check that you are pressing USR2 / D15.
- **Wrong or busy upload port:** run `board list` again and close any monitor occupying the serial port.

## Validation status

The current source was compiled with ESP32 core 3.3.11 and Seeed_GFX2 1.0.0, uploaded through CLI, and passed Flash verification. The latest build used **353,591 bytes** of program storage and **24,836 bytes** for global variables. The user confirmed the latest start/restart fix on physical hardware; this does not constitute testing of every gameplay edge case.
