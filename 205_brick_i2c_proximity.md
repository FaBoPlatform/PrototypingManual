# #205 Proximity I2C Brick

<center>![](/img/200_i2c/product/205.jpg)
<!--COLORME-->

## Overview
近接センサーを使ったBrickです。

I2Cでデータを取得できます。

## Connecting
I2Cコネクタへ接続します。

![](/img/200_i2c/connect/205_proximity_connect.jpg)

## Support
|Arduino|RaspberryPi|IchigoJam|Edison|
|:--:|:--:|:--:|:--:|
|◯|◯|◯|◯|

## VCNL4010 Datasheet
| Document |
|:--|
| [VCNL4010 Datasheet](https://www.adafruit.com/images/product-files/466/vcnl4010.pdf) |

## Register
| I2C Slave Address |
|:-- |
| 0x13 |

## Schematic
![](/img/200_i2c/schematic/205_proximity.png)

## Library
### for Arduino
- [Arduino IDEからインストール](http://fabo.io/library_install.html)

  ライブラリ名：「FaBo 205 Proximity VCNL4010」

- [Library GitHub](https://github.com/FaBoPlatform/FaBoProximity-VCNL4010-Library)
- [Library Document](http://fabo.io/doxygen/FaBoProximity-VCNL4010-Library/)

### for RaspberryPi
- pipからインストール
```
pip install FaBoProximity_VCNL4010
```
- [Library GitHub](https://github.com/FaBoPlatform/FaBoProximity-VCNL4010-Python)
- [Library Document](http://fabo.io/doxygen/FaBoProximity-VCNL4010-Python/)

## Sample Code
### for Arduino
上記のArduino Libraryをインストールし、スケッチの例から、「FaBo 205 Proximity VCNL4010」→「proximity」を選択してください。

### for Raspberry Pi
上記のRaspberryPi Python Libraryをインストールしてからご使用ください。

```python
# coding: utf-8
## @package FaBoProximity_VCNL4010
#  This is a library for the FaBo Proximity I2C Brick.
#
#  http://fabo.io/205.html
#
#  Released under APACHE LICENSE, VERSION 2.0
#
#  http://www.apache.org/licenses/
#
#  FaBo <info@fabo.io>

import FaBoProximity_VCNL4010
import time
import sys

vcnl4010 = FaBoProximity_VCNL4010.VCNL4010()

try:
    while True:
        prox = vcnl4010.readProx()
        ambi = vcnl4010.readAmbi()

        print "Prox = ", prox,
        print "Ambi = ", ambi

        time.sleep(1)

except KeyboardInterrupt:
    sys.exit()
```

### for IchigoJam
I2Cコネクタに接続したProximity I2C Brickより、センサーから物体までの距離と明るさを取得し、画面上に出力します。
```
10 'FaBo Brick Sample
20 '#205 Proximity I2C Brick
30 CLS
100 'Slave address
110 D=#13
200 'Address set
210 POKE #800,#31,#2d,#80,#82,#83,#84,#85,#87
220 POKE #810,#00,#08,#07,20,#7f,2
300 'Init
310 A=I2CW(D,#800,1,#810,1)
320 A=I2CW(D,#801,1,#811,1)
330 A=I2CW(D,#802,1,#812,1)
340 A=I2CW(D,#803,1,#812,1)
350 A=I2CW(D,#804,1,#813,1)
360 A=I2CW(D,#805,1,#814,1)
400 'Read proximity data
410 LOCATE 0,3
420 A=I2CR(D,#802,1,#820,1)
430 if (PEEK(#820)& #20) != #20 PRINT"         ":GOTO 500
440 A=I2CW(D,#807,1,#815,1)
450 A=I2CR(D,#807,1,#821,2)
460 ?"proxi:";PEEK(#822)/2+PEEK(#821)*128;"    "
500 'Read ambient data
510 if (PEEK(#820)& #40) != #40 PRINT"        ":GOTO 600
520 A=I2CW(D,#806,1,#815,1)
530 A=I2CR(D,#806,1,#823,2)
540 ?"ambi :";PEEK(#824)/2+PEEK(#823)*128;"    "
600 'loop
610 WAIT 50
620 GOTO 410
```

### for Edison
I2Cコネクタに接続したProximity I2C Brickより、センサーから物体までの距離と明るさを取得し、コンソールに出力します。
```js
//
// FaBo Brick Sample
//
// #205 Proximity i2c Brick
//

var m = require('mraa');

var i2c = new m.I2c(0);

i2c.address(0x13);

i2c.writeReg(0x31, 0x00);
i2c.writeReg(0x2d, 0x08);

// Set Enable
i2c.writeReg(0x80, 0x07)
// PROX_RATE
i2c.writeReg(0x82, 0x07);
// LED_CRNT
i2c.writeReg(0x83, 20);
// AMBI_PARM
i2c.writeReg(0x84, 0x7F);

loop();

function loop()
{
    // proximity
    if ( i2c.readReg(0x80) & 0x20 ) {
        var prox_buff = i2c.readBytesReg(0x87, 2);
        var prox = prox_buff[0] << 8 | prox_buff[1];

        console.log("Prox:" + prox);
    }

    // ambient
    if ( i2c.readReg(0x80) & 0x40 ) {
        var ambi_buff = i2c.readBytesReg(0x85, 2);
        var ambi = ambi_buff[0] << 8 | ambi_buff[1];

        console.log("ambi:" + ambi);
    }
    console.log("");
    setTimeout(loop,500);
}
```

## Parts
- Vishay VCNL4010

## GitHub
- https://github.com/FaBoPlatform/FaBo/tree/master/205_proximity
