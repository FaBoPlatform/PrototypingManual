# #211 7Segment LED I2C Brick

<center>![](/img/200_i2c/product/211.jpg)
<!--COLORME-->

## Overview
７セグメントLEDを使ったBrickです。

I2Cで表示パターンを制御できます。

## Connecting
I2Cコネクタへ接続します。

<center>![](/img/200_i2c/connect/211_7seg_connect.jpg)

## Support
|Arduino|RaspberryPi|IchigoJam|
|:--:|:--:|:--:|
|◯|◯|◯|

## TLC59208F Datasheet
| Document |
| -- |
| [TLC59208F Datasheet](http://www.ti.com/jp/lit/gpn/tlc59208f) |

## Register
| A0 | A1 | A2 | Slave Address |
| -- | -- | -- | -- |
| LOW | LOW | LOW | 0x20 |

FaBo Brickでは、初期値に0x20が設定されています。Brick裏面のソルダージャンパーで設定を変更できます。

## Schematic
![](/img/200_i2c/schematic/211_7seg.png)

## Library
### for Arduino
- [Arduino IDEからインストール](http://fabo.io/library_install.html)

  ライブラリ名：「FaBo 211 7Segment LED TLC59208F」

- [Library GitHub](https://github.com/FaBoPlatform/FaBo7Seg-TLC59208-Library)
- [Library Document](http://fabo.io/doxygen/FaBo7Seg-TLC59208-Library/)

### for RaspberryPi
- pipからインストール
```
pip install FaBo7Seg_TLC59208
```
- [Library GitHub](https://github.com/FaBoPlatform/FaBo7Segment-TLC59208-Python)
- [Library Document](http://fabo.io/doxygen/FaBo7Segment-TLC59208-Python/)

## Sample Code
PWM出力値は、"0x02"でほぼ視認できる明るさで点灯されます。あまり高い数値にすると、点灯しなくなるおそれがあります。

### for Arduino
I2Cコネクタに7Segment LED Brickを接続し、「0〜9」、「.」を順番に表示させます。
```c
//
// FaBo Brick Sample
//
// #211 7Segment LED I2C Brick
//

#include <Wire.h>

#define ADDR0 0x20

void setup() {
  Wire.begin();
  Serial.begin(9600);
  Serial.println();
  Serial.println("RESET");
  ini(ADDR0);
}

void loop() {
  for (int i = 0; i<12; i++) {
    Serial.println(i) ;
    show(ADDR0, i);
    delay(500);
  }
}

void show(byte addr, int num){
  unsigned char PWM_Value = 0x02;
  switch (num) {
    case 0:
      // 0:ABCDEF
      Wire.beginTransmission(addr);
      Wire.write(0xA2);
      Wire.write(PWM_Value); // PWM0 E
      Wire.write(PWM_Value); // PWM1 D
      Wire.write(PWM_Value); // PWM2 C
      Wire.write(0x00); // PWM3 DP
      Wire.write(PWM_Value); // PWM4 B
      Wire.write(PWM_Value); // PWM5 A
      Wire.write(PWM_Value); // PWM6 F
      Wire.write(0x00); // PWM7 G
      Wire.endTransmission();
      break;
    case 1:
      // 1:BC
      Wire.beginTransmission(addr);
      Wire.write(0xA2);
      Wire.write(0x00); // PWM0 E
      Wire.write(0x00); // PWM1 D
      Wire.write(PWM_Value); // PWM2 C
      Wire.write(0x00); // PWM3 DP
      Wire.write(PWM_Value); // PWM4 B
      Wire.write(0x00); // PWM5 A
      Wire.write(0x00); // PWM6 F
      Wire.write(0x00); // PWM7 G
      Wire.endTransmission();
      break;
    case 2:
      // 2:ABDEG
      Wire.beginTransmission(addr);
      Wire.write(0xA2);
      Wire.write(PWM_Value); // PWM0 E
      Wire.write(PWM_Value); // PWM1 D
      Wire.write(0x00); // PWM2 C
      Wire.write(0x00); // PWM3 DP
      Wire.write(PWM_Value); // PWM4 B
      Wire.write(PWM_Value); // PWM5 A
      Wire.write(0x00); // PWM6 F
      Wire.write(PWM_Value); // PWM7 G
      Wire.endTransmission();
      break;
    case 3:
      // 3:ABCDG
      Wire.beginTransmission(addr);
      Wire.write(0xA2);
      Wire.write(0x00); // PWM0 E
      Wire.write(PWM_Value); // PWM1 D
      Wire.write(PWM_Value); // PWM2 C
      Wire.write(0x00); // PWM3 DP
      Wire.write(PWM_Value); // PWM4 B
      Wire.write(PWM_Value); // PWM5 A
      Wire.write(0x00); // PWM6 F
      Wire.write(PWM_Value); // PWM7 G
      Wire.endTransmission();
      break;
    case 4:
      // 4:BCFG
      Wire.beginTransmission(addr);
      Wire.write(0xA2);
      Wire.write(0x00); // PWM0 E
      Wire.write(0x00); // PWM1 D
      Wire.write(PWM_Value); // PWM2 C
      Wire.write(0x00); // PWM3 DP
      Wire.write(PWM_Value); // PWM4 B
      Wire.write(0x00); // PWM5 A
      Wire.write(PWM_Value); // PWM6 F
      Wire.write(PWM_Value); // PWM7 G
      Wire.endTransmission();
      break;
    case 5:
      // 5:ACDFG
      Wire.beginTransmission(addr);
      Wire.write(0xA2);
      Wire.write(0x00); // PWM0 E
      Wire.write(PWM_Value); // PWM1 D
      Wire.write(PWM_Value); // PWM2 C
      Wire.write(0x00); // PWM3 DP
      Wire.write(0x00); // PWM4 B
      Wire.write(PWM_Value); // PWM5 A
      Wire.write(PWM_Value); // PWM6 F
      Wire.write(PWM_Value); // PWM7 G
      Wire.endTransmission();
      break;
    case 6:
      // 6:ACDEFG
      Wire.beginTransmission(addr);
      Wire.write(0xA2);
      Wire.write(PWM_Value); // PWM0 E
      Wire.write(PWM_Value); // PWM1 D
      Wire.write(PWM_Value); // PWM2 C
      Wire.write(0x00); // PWM3 DP
      Wire.write(0x00); // PWM4 B
      Wire.write(PWM_Value); // PWM5 A
      Wire.write(PWM_Value); // PWM6 F
      Wire.write(PWM_Value); // PWM7 G
      Wire.endTransmission();
      break;
    case 7:
      // 7:ABCF
      Wire.beginTransmission(addr);
      Wire.write(0xA2);
      Wire.write(0x00); // PWM0 E
      Wire.write(0x00); // PWM1 D
      Wire.write(PWM_Value); // PWM2 C
      Wire.write(0x00); // PWM3 DP
      Wire.write(PWM_Value); // PWM4 B
      Wire.write(PWM_Value); // PWM5 A
      Wire.write(PWM_Value); // PWM6 F
      Wire.write(0x00); // PWM7 G
      Wire.endTransmission();
      break;
    case 8:
      // 8:ABCDEFG
      Wire.beginTransmission(addr);
      Wire.write(0xA2);
      Wire.write(PWM_Value); // PWM0 E
      Wire.write(PWM_Value); // PWM1 D
      Wire.write(PWM_Value); // PWM2 C
      Wire.write(0x00); // PWM3 DP
      Wire.write(PWM_Value); // PWM4 B
      Wire.write(PWM_Value); // PWM5 A
      Wire.write(PWM_Value); // PWM6 F
      Wire.write(PWM_Value); // PWM7 G
      Wire.endTransmission();
      break;
    case 9:
      // 9:ABCDFG
      Wire.beginTransmission(addr);
       Wire.write(0xA2);
      Wire.write(0x00); // PWM0 E
      Wire.write(PWM_Value); // PWM1 D
      Wire.write(PWM_Value); // PWM2 C
      Wire.write(0x00); // PWM3 DP
      Wire.write(PWM_Value); // PWM4 B
      Wire.write(PWM_Value); // PWM5 A
      Wire.write(PWM_Value); // PWM6 F
      Wire.write(PWM_Value); // PWM7 G
      Wire.endTransmission();
      break;
    case 10:
      // Dot
      Wire.beginTransmission(addr);
      Wire.write(0xA2);
      Wire.write(0x00); // PWM0 E
      Wire.write(0x00); // PWM1 D
      Wire.write(0x00); // PWM2 C
      Wire.write(PWM_Value); // PWM3 DP
      Wire.write(0x00); // PWM4 B
      Wire.write(0x00); // PWM5 A
      Wire.write(0x00); // PWM6 F
      Wire.write(0x00); // PWM7 G
      Wire.endTransmission();
      break;
    default:
      // off
      Wire.beginTransmission(addr);
      Wire.write(0xA2);
      Wire.write(0x00); // PWM0 E
      Wire.write(0x00); // PWM1 D
      Wire.write(0x00); // PWM2 C
      Wire.write(0x00); // PWM3 DP
      Wire.write(0x00); // PWM4 B
      Wire.write(0x00); // PWM5 A
      Wire.write(0x00); // PWM6 F
      Wire.write(0x00); // PWM7 G
      Wire.endTransmission();
      break;
  }

}

void ini(byte addr){
  Wire.beginTransmission(addr);
  Wire.write(0x80); //
  Wire.write(0x81); // MODE1
  Wire.write(0x03); // MODE2
  Wire.write(0x00); // PWM0
  Wire.write(0x00); // PWM1
  Wire.write(0x00); // PWM2
  Wire.write(0x00); // PWM3
  Wire.write(0x00); // PWM4
  Wire.write(0x00); // PWM5
  Wire.write(0x00); // PWM6
  Wire.write(0x00); // PWM7
  Wire.write(0xFF); // GRPPWM
  Wire.write(0x00); // GRPFREQ
  Wire.write(0xAA); // LEDOUT0
  Wire.write(0xAA); // LEDOUT1
  Wire.write(0x92); // SUBADR1
  Wire.write(0x94); // SUBADR2
  Wire.write(0x98); // SUBADR3
  Wire.write(0xD0); // ALLCALLADR
  Wire.endTransmission();
}
```

### for Raspberry Pi

I2Cコネクタに7seg Brickを接続し、「0〜９」、「.」を順番に表示させます。

```python
# coding: utf-8
#
# FaBo Brick Sample
#
# #211 7Segment LED I2C Brick
#

import smbus
import time

ADDRESS = 0x20 #TLC59208F device address
CHANNEL = 1

INIT   = 0x80    #設定用
SEGSET = 0xA2    #7segLEDへ出力設定
VALUE  = 0x01    #LED点灯設定
ZERO   = 0x00    #LED消灯設定

#初期設定用
init_set = [0x81, #MODE1
            0x03, #MODE2
            0x00, #PWM0
            0x00, #PWM1
            0x00, #PWM2
            0x00, #PWM3
            0x00, #PWM4
            0x00, #PWM5
            0x00, #PWM6
            0x00, #PWM7
            0xFF, #GRPPWM
            0x00, #GRPREQ
            0xAA, #LEDOUT0
            0xAA, #LEDOUT1
            0x92, #SUBADR1
            0x94, #SUBADR2
            0x98, #SUBADR3
            0xD0] #ALLCALLADR

#数値出力用
set = [[VALUE,  #PWM0  [0]出力用の設定値 (set[0][0:])
        VALUE,  #PWM1
        VALUE,  #PWM2
        ZERO,   #PWM3
        VALUE,  #PWM4
        VALUE,  #PWM5
        VALUE,  #PWM6
        ZERO]   #PWM7
         ,
       [ZERO,   #PWM0  [1]出力用の設定値 (set[1][0:])
        ZERO,   #PWM1
        VALUE,  #PWM2
        ZERO,   #PWM3
        VALUE,  #PWM4
        ZERO,   #PWM5
        ZERO,   #PWM6
        ZERO]   #PWM7
         ,
       [VALUE,  #PWM0  [2]出力用の設定値 (set[2][0:])
        VALUE,  #PWM1
        ZERO,   #PWM2
        ZERO,   #PWM3
        VALUE,  #PWM4
        VALUE,  #PWM5
        ZERO,   #PWM6
        VALUE]  #PWM7
         ,
       [ZERO,   #PWM0  [3]出力用の設定値 (set[3][0:])
        VALUE,  #PWM1
        VALUE,  #PWM2
        ZERO,   #PWM3
        VALUE,  #PWM4
        VALUE,  #PWM5
        ZERO,   #PWM6
        VALUE]  #PWM7
         ,
       [ZERO,   #PWM0  [4]出力用の設定値 (set[4][0:])
        ZERO,   #PWM1
        VALUE,  #PWM2
        ZERO,   #PWM3
        VALUE,  #PWM4
        ZERO,   #PWM5
        VALUE,  #PWM6
        VALUE]  #PWM7
         ,
       [ZERO,   #PWM0  [5]出力用の設定値 (set[5][0:])
        VALUE,  #PWM1
        VALUE,  #PWM2
        ZERO,   #PWM3
        ZERO,   #PWM4
        VALUE,  #PWM5
        VALUE,  #PWM6
        VALUE]  #PWM7
         ,
       [VALUE,  #PWM0  [6]出力用の設定値 (set[6][0:])
        VALUE,  #PWM1
        VALUE,  #PWM2
        ZERO,   #PWM3
        ZERO,   #PWM4
        VALUE,  #PWM5
        VALUE,  #PWM6
        VALUE]  #PWM7
         ,
       [ZERO,   #PWM0  [7]出力用の設定値 (set[7][0:])
        ZERO,   #PWM1
        VALUE,  #PWM2
        ZERO,   #PWM3
        VALUE,  #PWM4
        VALUE,  #PWM5
        VALUE,  #PWM6
        ZERO]   #PWM7
         ,
       [VALUE,  #PWM0  [8]出力用の設定値 (set[8][0:])
        VALUE,  #PWM1
        VALUE,  #PWM2
        ZERO,   #PWM3
        VALUE,  #PWM4
        VALUE,  #PWM5
        VALUE,  #PWM6
        VALUE]  #PWM7
         ,
       [ZERO,   #PWM0  [9]出力用の設定値 (set[9][0:])
        VALUE,  #PWM1
        VALUE,  #PWM2
        ZERO,   #PWM3
        VALUE,  #PWM4
        VALUE,  #PWM5
        VALUE,  #PWM6
        VALUE]  #PWM7
         ,
       [ZERO,   #PWM0  [.]出力用の設定値 (set[10][0:])
        ZERO,   #PWM1
        ZERO,   #PWM2
        VALUE,  #PWM3
        ZERO,   #PWM4
        ZERO,   #PWM5
        ZERO,   #PWM6
        ZERO]   #PWM7
         ]

if __name__ == '__main__':

    bus = smbus.SMBus(CHANNEL)

     #初期設定
    bus.write_i2c_block_data(ADDRESS,INIT,init_set)

    time.sleep(0.5)

    while True:
        for num in range(0 , 11):
           #ログ出力
           print "output:%d" % (num)
           #7segLEDへの表示
           bus.write_i2c_block_data(ADDRESS,SEGSET,set[num][0:])

           time.sleep(1)
```

### for IchigoJam

I2Cコネクタに7seg Brickを接続し、「0〜９」、「.」を順番に表示させます。

```
10 '#211 7Segment I2C Brick
20 CLS
110 D=#20
210 POKE #800,#80,#A2
220 POKE #810,#81,#3,0,0,0,0,0,0,0,0,#FF,0,#AA,#AA,#92,#94,#98,#D0
225 POKE #830,#2,#2,#2,0,#2,#2,#2,0
230 POKE #838,0,0,#2,0,#2,0,0,0
235 POKE #840,#2,#2,0,0,#2,#2,0,#2
240 POKE #848,0,#2,#2,0,#2,#2,0,#2
245 POKE #850,0,0,#2,0,#2,#0,#2,#2
250 POKE #858,0,#2,#2,0,0,#2,#2,#2
255 POKE #860,#2,#2,#2,0,0,#2,#2,#2
260 POKE #868,0,0,#2,0,#2,#2,#2,0
265 POKE #870,#2,#2,#2,0,#2,#2,#2,#2
270 POKE #878,0,#2,#2,0,#2,#2,#2,#2
275 POKE #880,0,0,0,#2,0,0,0,0

310 A=I2CW(D,#800,1,#810,18)
320 C=0

410 LOCATE 0,3
415 PRINT "Number:";C;" "
420 IF C=0 A=I2CW(D,#801,1,#830,8)
425 IF C=1 A=I2CW(D,#801,1,#838,8)
430 IF C=2 A=I2CW(D,#801,1,#840,8)
435 IF C=3 A=I2CW(D,#801,1,#848,8)
440 IF C=4 A=I2CW(D,#801,1,#850,8)
445 IF C=5 A=I2CW(D,#801,1,#858,8)
450 IF C=6 A=I2CW(D,#801,1,#860,8)
455 IF C=7 A=I2CW(D,#801,1,#868,8)
460 IF C=8 A=I2CW(D,#801,1,#870,8)
465 IF C=9 A=I2CW(D,#801,1,#878,8)
470 IF C=10 A=I2CW(D,#801,1,#880,8)
480 C=C+1
490 IF C>10 C=0

510 WAIT 50
520 GOTO 410
```

## Parts
- 7セグメントLED
- Texas Instruments TLC59208F

## GitHub
- https://github.com/FaBoPlatform/FaBo/tree/master/211_7seg
