# #210 GPIO I2C Brick

<center>![](/img/200_i2c/product/210.jpg)
<!--COLORME-->

## Overview
汎用I/O拡張チップを使用したBrickです。

I2Cで8個のLEDを制御できます。

## Connecting
I2Cコネクタへ接続します。

![](/img/200_i2c/connect/210_gpio_connect.jpg)

## Support
|Arduino|RaspberryPi|IchigoJam|Edison|
|:--:|:--:|:--:|:--:|
|◯|◯|◯|◯|

## PCAL6408 Datasheet
| Document |
| -- |
| [PCAL6408 Datasheet](http://www.nxp.com/documents/data_sheet/PCAL6408A.pdf) |

## Register
| Slave Address |
| -- |
| 0x20 |

## Schematic
![](/img/200_i2c/schematic/210_gpio.png)

## Library
### for Arduino
- [Arduino IDEからインストール](http://fabo.io/library_install.html)

  ライブラリ名：「FaBo 210 GPIO PCAL6408A」

- [Library GitHub](https://github.com/FaBoPlatform/FaBoGPIO-PCAL6408-Library)
- [Library Document](http://fabo.io/doxygen/FaBoGPIO-PCAL6408-Library/)

### for RaspberryPi
- pipからインストール
```
pip install FaBoGPIO_PCAL6408
```
- [Library GitHub](https://github.com/FaBoPlatform/FaBoGPIO-PCAL6408-Python)
- [Library Document](http://fabo.io/doxygen/FaBoGPIO-PCAL6408-Python/)

## Sample Code
### for Arduino
I2CコネクタにGPIO Brickを接続し、GPIO Brickについている8つのLEDを左上から右下に向かって順番に点灯させます。
```c
//
// FaBo Brick Sample
//
// #210 GPIO I2C Brick
//

#include <Wire.h>

#define DEVICE_ADDR 0x20 // スレーブデバイスのアドレス

void setup() {
  Wire.begin(); // I2C開始

  Serial.begin(9600);
  Serial.println();
  Serial.println("RESET");
  Serial.println();

  Wire.beginTransmission(DEVICE_ADDR); // 初期化
  Wire.write(0x03);
  Wire.write(0x00);
  Wire.endTransmission();
}

void loop() {
  int c = 1;

  for (int i=0; i<8; i++) {
    Serial.println(c);
    Wire.beginTransmission(DEVICE_ADDR); // 順番に点滅させる
    Wire.write(0x01);
    Wire.write(c);
    Wire.endTransmission();
    c = c << 1;
    delay(100);
  }

}
```

### for RaspberryPi
```python
# coding: utf-8
## @package FaBoGPIO_PCAL6408.py
#  This is a library for the FaBo GPIO I2C Brick.
#
#  http://fabo.io/210.html
#
#  Released under APACHE LICENSE, VERSION 2.0
#
#  http://www.apache.org/licenses/
#
#  FaBo <info@fabo.io>

import FaBoGPIO_PCAL6408
import time
import sys

pcal6408 = FaBoGPIO_PCAL6408.PCAL6408()

try:
    while True:
        for i in xrange(8):
            pcal6408.setDigital(1<<i, 1)
            time.sleep(1)

        pcal6408.setAllClear()
        time.sleep(1)

except KeyboardInterrupt:
    pcal6408.setAllClear()
    sys.exit()
```

### for IchigoJam
I2CコネクタにGPIO Brickを接続し、GPIO Brickについている8つのLEDを左上から右下に向かって順番に点灯させます。

```
10 'FaBo Brick Sample
20 '#210 GPIO I2C Brick
30 CLS
100 'slave address
110 D=#20
200 'address set
210 POKE #800,#03,0,#01
220 POKE #810,#0,#1,#2,#4,#8,#10,#20,#40,#80
300 'init
310 A=I2CW(D,#800,1,#801,1)
320 I=0
400 'write
410 A=I2CW(D,#802,1,#810+I,1)
420 LOCATE 0,3
430 IF I>7 I=0:GOTO 510
440 I=I+1
500 'loop
510 WAIT 20
520 GOTO 410
```

### for Edison
I2CコネクタにGPIO Brickを接続し、GPIO Brickについている8つのLEDを左上から右下に向かって順番に点灯させます。
```javascript
//
// FaBo Brick Sample
//
// #210 GPIO I2C Brick
//

var m = require('mraa');
var i2c = new m.I2c(0);
var c = 1;
var i = 0;

i2c.address(0x20);
i2c.writeReg(0x03, 0x00);

loop();

function loop()
{

  console.log(c);
  i2c.writeReg(0x01, c);

  if(i < 7){
    i++;
    c = c << 1;
  } else {
    i = 0;
    c = 1
  }
  setTimeout(loop, 100);
}
```

## Parts
- NXP PCAL6408

## GitHub
- https://github.com/FaBoPlatform/FaBo/tree/master/210_gpio
