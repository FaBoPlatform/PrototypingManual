# #203 Color I2C Brick

<center>![](/img/200_i2c/product/203.jpg)
<!--COLORME-->

## Overview
カラーセンサを使用したBrickです。

センサーより読み取った赤、緑、青、赤外線(明るさ)の4つのデータを、I2Cにて取得することができます。

## Connecting
I2Cコネクタへ接続します。

![](/img/200_i2c/connect/203_color_connect.jpg)

## Support
|Arduino|RaspberryPi|IchigoJam|Edison|
|:--:|:--:|:--:|:--:|
|◯|◯|◯|◯|

## S11059 Datasheet
| Document |
| -- |
| [S11059 Datasheet](http://www.hamamatsu.com/resources/pdf/ssd/s11059-02dt_etc_kpic1082j.pdf) |

## Register
| Slave Address |
| -- |
| 0x2A |

## Schematic
![](/img/200_i2c/schematic/203_color.png)

## Library
### for Arduino
- [Arduino IDEからインストール](http://fabo.io/library_install.html)

  ライブラリ名：「FaBo 203 Color S11059」

- [Library GitHub](https://github.com/FaBoPlatform/FaBoColor-s11059-Library)
- [Library Document](http://fabo.io/doxygen/FaBoColor-s11059-Library/)

### for RaspberryPi
- pipからインストール
```
pip install FaBoColor_S11059
```
- [Library GitHub](https://github.com/FaBoPlatform/FaBoColor-S11059-Python)
- [Library Document](http://fabo.io/doxygen/FaBoColor-S11059-Python/)

## Sample Code
### for Arduino
I2Cコネクタに接続したColor Brickにより、赤、緑、青、赤外の値を読み取り、シリアルモニタに出力します。

```c
//
// FaBo Brick Sample
//
// #203 Color Sensor I2C Brick
//
#include <Wire.h>

#define DEVICE_ADDR 0x2A // スレーブデバイスのアドレス
#define CTLREG 0x00

uint16_t red = 0;
uint16_t green = 0;
uint16_t blue = 0;
uint16_t infrared = 0;

void setup()
{
  Serial.begin(9600); //シリアル開始 表示用
  Wire.begin();       //I2C開始
}

void loop()
{
  //色の取得
  getColor();

  //色の出力
  Serial.print("R:[");
  Serial.print(red);
  Serial.print("] G:[");
  Serial.print(green);
  Serial.print("] B:[");
  Serial.print(blue);
  Serial.print("] IR:[");
  Serial.print(infrared);
  Serial.println("]");

  delay(500);
}

void getColor()
{
  uint16_t color= 0;

  // 待機解除
  Wire.beginTransmission(DEVICE_ADDR);
  Wire.write(CTLREG);
  Wire.write(0x83); // ADC reset wakeup
  Wire.endTransmission(false);

  Wire.beginTransmission(DEVICE_ADDR);
  Wire.write(CTLREG);
  Wire.write(0x03);
  Wire.endTransmission(true);

  delay(500);

  Wire.beginTransmission(DEVICE_ADDR);
  Wire.write(0x03);
  Wire.endTransmission(false);

  Wire.requestFrom(0x2A,8,true);

  //色データの取得

  // 赤
  color = Wire.read() << 8;
  color |= Wire.read();
  red = color;

  // 緑
  color =0;
  color = Wire.read() << 8;
  color |= Wire.read();
  green = color;

  // 青
  color =0;
  color = Wire.read() << 8;
  color |= Wire.read();
  blue = color;

  // 赤外
  color =0;
  color = Wire.read() << 8;
  color |= Wire.read();
  infrared = color;

  return;
}
```

### for Raspberry Pi
I2Cコネクタに接続したColor Brickにより、赤、緑、青、赤外の値を読み取り、コンソールに出力します。

```python
# coding: utf-8
#
# FaBo Brick Sample
#
# #203 Color Sensor I2C Brick
#

import smbus
import time

ADDRESS = 0x2A #S11059 device address
CHANNEL = 1
CTLREG = 0x00
OUTREG = 0x03
RESET = 0x83
RESTART = 0x03

class S11059:
    def __init__(self, bus, addr):
        self.bus = smbus.SMBus(bus)
        self.addr = addr

    def writebyte(self, cmd, data):
        self.bus.write_byte_data(self.addr, cmd, data)

    def readblock(self, cmd, len):
        return self.bus.read_i2c_block_data(self.addr, cmd, len)

if __name__ == '__main__':

    dev = S11059(CHANNEL, ADDRESS)

     #初期化
    dev.writebyte(CTLREG,RESET)
    dev.writebyte(CTLREG,RESTART)

    time.sleep(2.5)

    dev.writebyte(OUTREG,CTLREG)

    while True:
        #データ取得
        read_data = dev.readblock(CTLREG, 11)

        #データ設定
        red = read_data[3] << 8 | read_data[4]
        green = read_data[5] << 8 | read_data[6]
        blue = read_data[7] << 8 | read_data[8]
        infrared = read_data[9] << 8 | read_data[10]

        #データ出力
        print "R:%x G:%x B:%x I:%x" % (red,green,blue,infrared)
        print
        time.sleep(0.5)
```

### for IchigoJam
I2Cコネクタに接続したColor Brickにより、赤、緑、青、赤外の値を読み取り、画面上に出力します。

```
10 'FaBo Brick Sample
20 '#203 Color I2C Brick
30 CLS
100 'Slave address
110 D=#2A
200 'Address set
210 POKE #800,#00,#83,#03
220 POKE #810,#00,11
300 'init
310 A=I2CW(D,#800,1,#801,1)
320 A=I2CW(D,#800,1,#802,1)
320 A=I2CW(D,#802,1,#800,1)
400 'Read RGB IR data
410 A=I2CW(D,#810,1,#811,1)
420 A=I2CR(D,#810,1,#820,11)
500 'Output
510 LOCATE 0,3
520 ?"R;";PEEK(#824)/2+PEEK(#823)*128;"    "
530 ?"G;";PEEK(#826)/2+PEEK(#825)*128;"    "
540 ?"B;";PEEK(#828)/2+PEEK(#827)*128;"    "
550 ?"IR;";PEEK(#830)/2+PEEK(#829)*128;"    "
600 'loop
610 WAIT 20
620 GOTO 410
```

### for Edison
I2Cコネクタに接続したColor Brickにより、赤、緑、青、赤外の値を読み取り、コンソールに出力します。

※ACアダプタ接続時に値が正しく取得できない場合があります。

```javascript
//
// FaBo Brick Sample
//
// #203 Color Sensor I2C Brick
//

var m = require('mraa');

var i2c = new m.I2c(0);

var buffer = new Buffer(11);

i2c.address(0x2A);

// Reset
i2c.writeReg(0x00, 0x83);

// Read Start
i2c.writeReg(0x00, 0x03);

// data byte
i2c.writeReg(0x03, 0x00);

loop();

function loop(){
    try{
        buffer = i2c.readBytesReg(0x00, 11);

        var red   = buffer[3]<<8 | buffer[4];
        var green = buffer[5]<<8 | buffer[6];
        var blue  = buffer[7]<<8 | buffer[8];
        var ir    = buffer[9]<<8 | buffer[10];

        console.log("R:" + red + " G:" + green + " B:" + blue + " IR:" + ir);
    }catch(e){
        console.log("read err");
    }
    setTimeout(loop, 1000);
}

```

## Parts
- HAMAMATSU S11059

## GitHub
- https://github.com/FaBoPlatform/FaBo/tree/master/203_color
