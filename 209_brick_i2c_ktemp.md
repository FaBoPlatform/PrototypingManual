# #209 Ktemp I2C Brick

<center>![](/img/200_i2c/product/209.jpg)
<!--COLORME-->

## Overview
K型熱電対を接続できるBrickです。
I2Cでデータを取得できます。

## Connecting
I2Cコネクタへ接続します。

![](/img/200_i2c/connect/209_ktemp_connect.jpg)

## Support
|Arduino|RaspberryPi|IchigoJam|Edison|
|:--:|:--:|:--:|:--:|
|◯|◯|◯|◯|

## MCP3421 Datasheet
| Document |
| -- |
| [MCP3421 Datasheet](http://ww1.microchip.com/downloads/en/DeviceDoc/22003e.pdf) |

## Register
| Slave Address |
| -- |
| 0x68 - 0x6F |
MCP3421のSlave Addressは0x68〜0x6Fのものが存在し、その値は工場出荷時に決まっており、後から変更することはできません。
FaBoBrickでは、0x68、または0x69の２種類を使用しています。

## Schematic
![](/img/200_i2c/schematic/209_ktemp.png)

## Library
### for Arduino
- [Arduino IDEからインストール](http://fabo.io/library_install.html)

  ライブラリ名：「FaBo 209 KTemp MCP3421」

- [Library GitHub](https://github.com/FaBoPlatform/FaBoKTemp-MCP3421-Library)
- [Library Document](http://fabo.io/doxygen/FaBoKTemp-MCP3421-Library/)

### for RaspberryPi
- pipからインストール
```
pip install FaBoKTemp_MCP3421
```
- [Library GitHub](https://github.com/FaBoPlatform/FaBoKTemp-MCP3421-Python)
- [Library Document](http://fabo.io/doxygen/FaBoKTemp-MCP3421-Python/)

## Sample Code
### for Arduino
I2Cコネクタに接続したKtemp BrickにK型熱電対を接続し、熱電対から取得した値を温度に変換してシリアルモニタに出力します。
```c
//
// FaBo Brick Sample
//
// #209 Ktemp I2C Brick
//

#include <Wire.h>

// スレーブデバイスのアドレス
int device_addr;

void setup() {
  Wire.begin();
  Serial.begin(9600);

  Serial.println("Device Check");
  device_addr = address_check();

  if (device_addr == 0)
  {
    Serial.print("device not found");
    while(1);
  }

  Wire.beginTransmission(device_addr);
  Wire.write(0x9f); // 初期設定
  Wire.endTransmission();
}

byte address_check(){
  byte addr;
  byte error;

  // MCP3421のアドレスチェック 0x68-0x6F
  for(addr = 0x68; addr < 0x70; addr++ )
  {
    Wire.beginTransmission(addr);
    error = Wire.endTransmission();

    if (error == 0)
    {
      Serial.print("I2C device address 0x");
      Serial.println(addr, HEX);
      return addr;
    }
  }
  return 0;

}

void loop() {
  int32_t data;
  uint8_t *p = (uint8_t *)&data;
  uint8_t stat;
  double mv;
  int32_t uv;
  uint16_t mvuv = 1 << (3+2*3);
  int cp = 407;  // プローブ補正値
  double temp;

  Wire.requestFrom(device_addr, 4);
  if (Wire.available() != 4) {
    Serial.println("read failed");
    delay(1000);
  }
  for (int8_t i = 2; i >= 0; i--) {
    p[i] = Wire.read();
  }
  p[3] = p[2] & 0x80 ? 0xff : 0;
  stat = Wire.read();
  Wire.endTransmission();

  if ((stat & 0x80) == 0) {
    mv = (double)data/mvuv;
    uv = (data*1000L)/mvuv;
    temp = (uv + cp) / 40.7;
    Serial.print(mv);Serial.print(" mv, ");
    Serial.print(uv);Serial.print(" uv, ");
    Serial.print(temp);Serial.println(" C");
  }

  delay(500);
}
```

### for Raspberry Pi

KTemp Brickはデバイスアドレスはサンプルプログラムと異なることがあります。
(0x68〜0x6F)

i2cのセンサーを接続後、下記コマンドにて確認して下さい。

サンプルでは0x69となっていますので、異なる場合は対象のアドレスに変更してご使用下さい。

```
sudo i2cdetect -y 1
```

このサンプルは、I2Cコネクタに接続したKtemp BrickにK型熱電対を接続し、熱電対から取得した値を温度に変換してコンソールに出力します。

```python
# coding: utf-8
#
# FaBo Brick Sample
#
# #209 Ktemp I2C Brick
#

import smbus
import time

ADDRESS = 0x69 #MCP3421 device address 環境に合ったデバイスアドレス
CHANNEL = 1
CTLREG = 0x9f
mvuv = 1 << (3+2*3)
cp = 407  #プローブ補正値

class MCP3421:
    def __init__(self, bus, addr):
        self.bus = smbus.SMBus(bus)
        self.addr = addr

    def writebyte(self, cmd, data):
        self.bus.write_byte_data(self.addr, cmd, data)

    def readblock(self, cmd, len):
        return self.bus.read_i2c_block_data(self.addr, cmd, len)

if __name__ == '__main__':

    dev = MCP3421(CHANNEL, ADDRESS)

     #初期化
    dev.writebyte(CTLREG,0)

    while True:
          #データ取得
        read_data = dev.readblock(CTLREG, 4)

          #取得データを結合
        data = (read_data[0] << 16) + (read_data[1] << 8) + read_data[2]

          #取得データを温度に変換
        temp = (data *1000/mvuv + cp) / 40.7

          #温度出力
        print "temp:%3.2f C" % (temp)
        print
        time.sleep(1)
```
### for IchigoJam
このサンプルは、I2Cコネクタに接続したKtemp BrickにK型熱電対を接続し、熱電対から取得した値を温度に変換し画面上に出力します。
```
10 'FaBo Brick Sample
20 '#209 Ktemp I2C Brick
30 CLS
100 'slave address #68-#6F
110 D=#68

200 'address set
210 POKE #800,#9f,0,4

300 'init
310 A=I2CW(D,#800,1,#801,1)

400 'read
410 A=I2CR(D,#800,1,#810,4)
420 IF PEEK(#810) & 0xFF THEN S=1 ELSE S=0
430 T=PEEK(#811)<<8 | PEEK(#812)
440 T=T/128*125+T%128*125/128 +500:'(T*1000)/1024+500
450 T=T/3

500 'output
510 LOCATE 0,3
520 ?"KTEMP:";
530 IF T<0 THEN T=T*-1:?"-";
540 ?T/10;".";T%10;"  ":'T/10

600 'loop
610 WAIT 5
620 GOTO 410
```

### for Edison
このサンプルは、I2Cコネクタに接続したKtemp BrickにK型熱電対を接続し、熱電対から取得した値を温度に変換してコンソールに出力します。
```javascript
//
// FaBo Brick Sample
//
// #209 Ktemp I2C Brick
//

var m = require('mraa');
var i2c = new m.I2c(0);

i2c.address(0x68);

var CTLREG = 0x9f;
var mvuv = 1 << (3+2*3);
var cp = 407;

var read_data = new Buffer(4);

// init
i2c.writeReg(CTLREG, 0);

loop();

function loop()
{
    // data read
    read_data = i2c.readBytesReg(CTLREG, 4);

    var data = (read_data[0] << 16) + (read_data[1] << 8) + read_data[2];

    var temp = Math.floor((data * 1000 / mvuv + cp) / 40.7*100)/100;

    console.log("temp:" + temp);
    console.log("");
    setTimeout(loop, 1000);
}
```

## Parts
- Microchip Technology MCP3421

## GitHub
- https://github.com/FaBoPlatform/FaBo/tree/master/209_ktemp
