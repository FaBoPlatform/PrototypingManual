# #207 Temperature I2C Brick

<center>![](/img/200_i2c/product/207.jpg)
<!--COLORME-->

## Overview
温度センサを使用したBrickです。
I2Cでデータを取得できます。

計測できる範囲は−55度〜150度です。

## Connecting
I2Cコネクタへ接続します。

![](/img/200_i2c/connect/207_temperature_connect.jpg)

## Support
|Arduino|RaspberryPi|IchigoJam|Edison|
|:--:|:--:|:--:|:--:|
|◯|◯|◯|◯|

## ADT7410 Datasheet
| Document |
| -- |
| [ADT7410 Datasheet](http://www.analog.com/media/en/technical-documentation/data-sheets/ADT7410.pdf) |

## Register
| Slave Address |
| -- |
| 0x48 |

## Schematic
![](/img/200_i2c/schematic/207_temperature.png)

## Library
### for Arduino
- [Arduino IDEからインストール](http://fabo.io/library_install.html)

  ライブラリ名：「FaBo 207 Temperature ADT7410」

- [Library GitHub](https://github.com/FaBoPlatform/FaBoTemperature-ADT7410-Library)
- [Library Document](http://fabo.io/doxygen/FaBoTemperature-ADT7410-Library/)

### for RaspberryPi
- pipからインストール
```
pip install FaBoTemperature_ADT7410
```
- [Library GitHub](https://github.com/FaBoPlatform/FaBoTemperature-ADT7410-Python)
- [Library Document](http://fabo.io/doxygen/FaBoTemperature-ADT7410-Python/)

## Sample Code
### for Arduino
I2CコネクタにTemperature Brick(I2C)を接続し、取得した温度をシリアルモニタに出力します。
```c
//
// FaBo Brick Sample
//
// #207 Temperature I2C Brick
//

#include <Wire.h>
#define DEVICE_ADDR (0x48)

void setup() {
  Serial.begin(9600);
  Wire.begin();

  Wire.beginTransmission(DEVICE_ADDR);
  Wire.write(0x03);
  Wire.write(0x80);
  Wire.endTransmission();

}

void loop() {
  uint16_t val;
  float tmp;
  long  l_val;

  Wire.requestFrom(DEVICE_ADDR, 2);

  val = (uint16_t)Wire.read() << 8;   // read(上位8bit)
  val |= Wire.read();                 // read(下位8bit)

  l_val = (long)val;
  if(val & 0x8000) {         // 符号の判定
    l_val = l_val - 65536;   // マイナスの場合
  }

  tmp = (float)l_val * 0.0078125;   // ival / 128

  Serial.print("tmp:");
  Serial.println(tmp);
  delay(500);
}

```

### for RaspberryPi
I2CコネクタにTemperature Brick(I2C)を接続し、取得した温度をコンソールに出力します。

```python
# coding: utf-8
#
# FaBo Brick Sample
#
# #207 Temperature I2C Brick
#

import smbus
import time

ADDRESS = 0x48 #ADT7410 device address
CHANNEL  = 1

class ADT7410:
    def __init__(self, bus, addr):
        self.bus = smbus.SMBus(bus)
        self.addr = addr

    def readblock(self, cmd, len):
        return self.bus.read_i2c_block_data(self.addr, cmd, len)

if __name__ == '__main__':

    dev = ADT7410(CHANNEL, ADDRESS)

    while True:
        #データ取得
        read_data = dev.readblock(0x00, 12)
        #上位2バイトのみ取得し、温度データに加工
        temp = (read_data[0] << 8 | read_data[1]) >> 3

        #マイナスの場合の処理
        if(temp >= 4096):
            temp -= 8192;

        #温度出力
        print "temp:%4.2f" % (temp / 16.0)
        print
        time.sleep(1)
```
### for IchigoJam
I2CコネクタにTemperature Brick(I2C)を接続し、取得した温度を画面上に出力します。
```
10 'FaBo Brick Sample
20 '#207 Temperature I2C Brick
30 CLS
100 'slave address
110 D=#48
200 'address set
210 POKE #800,#03,0,#80,0
220 POKE #810,#00,2
300 'init
310 A=I2CW(D,#800,1,#801,1)
400 'read
410 A=I2CW(D,#802,1,#803,1)
420 A=I2CW(D,#810,1,#811,1)
430 A=I2CR(D,#810,1,#820,2)
500 'output
510 LOCATE 0,3
520 T=(PEEK(#820)*256+PEEK(#821))
530 T=T*5/64
540 ?"TEMP:";
550 IF T<0 THEN T=T*-1:?"-";
560 ?T/10;".";T%5;" "
600 'loop
610 WAIT 5
620 GOTO 410
```


### for Edison
I2CコネクタにTemperature Brick(I2C)を接続し、取得した温度をコンソールに出力します。
```javascript
//
// FaBo Brick Sample
//
// #207 Temperature I2C Brick
//

var m = require('mraa');
var i2c = new m.I2c(0);

var read_data = new Buffer(2);

// slave address
i2c.address(0x48);

loop();

function loop()
{
    i2c.writeReg(0x03, 0x80);
    read_data = i2c.read(2);

    var temp = ((read_data[0] << 8) | read_data[1]) >> 3;

    if(temp >= 4096){
        temp -= 8192;
    }

    console.log("temp:" + (temp / 16.0));

    setTimeout(loop, 1000);

}

```

## Parts
- Analog Devices ADT7410

## GitHub
- https://github.com/FaBoPlatform/FaBo/tree/master/207_temperature
