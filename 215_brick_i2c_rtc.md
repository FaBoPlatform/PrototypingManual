# #215 RTC I2C Brick

<center>![](/img/200_i2c/product/215.jpg)
<!--COLORME-->

## Overview
リアルタイムクロックを使用したBrickです。
I2Cでデータを取得できます。

## Connecting
I2Cコネクタへ接続します。

![](/img/200_i2c/connect/215_rtc_connect.jpg)

## Support
|Arduino|RaspberryPi|IchigoJam|
|:--:|:--:|:--:|
|◯|◯|◯|

## PCF2129 Datasheet
| Document |
| -- |
| [PCF2129 Datasheet](http://cache.nxp.com/documents/data_sheet/PCF2129.pdf) |

## Register
| Slave Address |
| -- |
| 0x51 |

## Schematic
![](/img/200_i2c/schematic/215_rtc.png)

## Library
### for Arduino
- [Arduino IDEからインストール](http://fabo.io/library_install.html)

  ライブラリ名：「FaBo 215 RTC PCF2129」

- [Library GitHub](https://github.com/FaBoPlatform/FaBoRTC-PCF2129-Library)
- [Library Document](http://fabo.io/doxygen/FaBoRTC-PCF2129-Library/)

### for RaspberryPi
- pipからインストール
```
pip install FaBoRTC_PCF2129
```
- [Library GitHub](https://github.com/FaBoPlatform/FaBoRTC-PCF2129-Python)
- [Library Document](http://fabo.io/doxygen/FaBoRTC-PCF2129-Python/)

## Sample Code
### for Arduino

```c
//
// FaBo Brick Sample
//
// #215 RTC I2C Brick
//

#include <Wire.h>

#define DEVICE_ADDR (0x51)

void setup() {
  Serial.begin(9600); // シリアルの開始デバック用
  Wire.begin(); // I2Cの開始

  byte sec = 5;
  byte min = 20;
  byte hour = 15;
  byte day = 26;
  byte wd = 1;
  byte month = 10;
  byte year = 2015;

  // 日付時刻のセット
  setRTC(year, month, day, wd, hour, min, sec);

}

void loop() {
  byte year, month, day, wd, hour, min, sec;

  // 日付時刻の取得
  getRTC(&year, &month, &day, &wd, &hour, &min, &sec);

  Serial.print(year + 2000);
  Serial.print("/");
  Serial.print(month);
  Serial.print("/");
  Serial.print(day);
  Serial.print(" ");
  Serial.print(wd);
  Serial.print(" ");
  Serial.print(hour);
  Serial.print(":");
  Serial.print(min);
  Serial.print(":");
  Serial.print(sec);
  Serial.println();

  delay(1000);
}



void getRTC(byte *year, byte *month, byte *day, byte *wd, byte *hour, byte *min, byte *sec) {
  byte c;

  Wire.beginTransmission(DEVICE_ADDR);
  Wire.write(0x03);
  Wire.endTransmission();
  Wire.requestFrom(DEVICE_ADDR,7);
  while(Wire.available()) {

    // seconds
    c = Wire.read();
    c &= 0x7f;
    *sec = (c >> 4) * 10 + (c & 0xf);

    // minutes
    c = Wire.read();
    c &= 0x7f;
    *min = (c >> 4) * 10 + (c & 0xf);

    // hours
    c = Wire.read();
    c &= 0x3f;
    *hour = (c >> 4) * 10 + (c & 0xf);

    // days
    c = Wire.read();
    c &= 0x3f;
    *day = (c >> 4) * 10 + (c & 0xf);

    // weekdays
    c = Wire.read();
    *wd = (c & 0x3);

    // month
    c = Wire.read();
    *month = (c >> 4) * 10 + (c & 0xf);

    // year
    c = Wire.read();
    *year = (c >> 4) * 10 + (c & 0xf);
  }

}

void setRTC(byte year, byte month, byte day, byte wd, byte hour, byte min, byte sec) {
  byte c;

  Wire.beginTransmission(DEVICE_ADDR);
  Wire.write(0x03);

  // sec
  c = ((sec / 10) << 4) + (sec % 10) + 0x80;
  Wire.write(c);

  // min
  c = ((min / 10) << 4) + (min % 10);
  Wire.write(c);

  // hour
  c = ((hour / 10) << 4) + (hour % 10);
  Wire.write(c);

  // day
  c = ((day / 10) << 4) + (day % 10);
  Wire.write(c);

  // wd
  Wire.write(wd);

  // month
  c = ((month / 10) << 4) + (month % 10);
  Wire.write(c);

  // year
  year = year - 2000;
  c = ((year / 10) << 4) + (year % 10);
  Wire.write(c);

  Wire.endTransmission();
}

```

### for RaspberryPi
上記のRaspberryPi Python Libraryをインストールしてからご使用ください。

```python
# coding: utf-8
## @package FaBoRTC_PCF2129
#  This is a library for the FaBo RTC I2C Brick.
#
#  http://fabo.io/215.html
#
#  Released under APACHE LICENSE, VERSION 2.0
#
#  http://www.apache.org/licenses/
#
#  FaBo <info@fabo.io>

import FaBoRTC_PCF2129
import time
import sys

rtc = FaBoRTC_PCF2129.PCF2129()


try:
    # 日付時刻の設定
    print "set date/time"
    rtc.setDate(
        2016, # Years
        7,    # months
        8,    # days
        12,   # hours
        1,    # minutes
        50)   # seconds

    while True:
        # 日付時刻の取得
        now = rtc.now()

        # 日付時刻の表示
        sys.stdout.write(str(now['year'])  + '/')
        sys.stdout.write(str(now['month']).zfill(2) + '/')
        sys.stdout.write(str(now['day']).zfill(2)   + ' ')

        sys.stdout.write(str(now['hour']).zfill(2)    + ':')
        sys.stdout.write(str(now['minute']).zfill(2)  + ':')
        sys.stdout.write(str(now['second']).zfill(2))
        print
        time.sleep(1)

except KeyboardInterrupt:
    sys.exit()
```

### for IchigoJam
I2CコネクタにRTC Brickを接続し、指定したい日時からの正確な経過時間を計測してシリアルモニタに出力します。
```
10 'FaBo Brick Sample
20 '#215 RTC I2C Brick

30 CLS

100 'Slave address
110 D=#51
200 'Address set
210 POKE #800,#03,#02
220 'sec, min, hor, day, weekday, mon, year(20XX)
230 POKE #820,54,30,12,7,2,6,16

240 POKE #830,(PEEK(#820)/10)<<4+PEEK(#820)%10+#80
241 POKE #831,(PEEK(#821)/10)<<4+PEEK(#821)%10
242 POKE #832,(PEEK(#822)/10)<<4+PEEK(#822)%10
243 POKE #833,(PEEK(#823)/10)<<4+PEEK(#823)%10
244 POKE #834,PEEK(#824)
245 POKE #835,(PEEK(#825)/10)<<4+PEEK(#825)%10
246 POKE #836,(PEEK(#826)/10)<<4+PEEK(#826)%10

300 'time set
310 A=I2CW(D,#800,1,#830,7)

410 'time read
420 A=I2CR(D,#801,1,#840,7)

500 'Output
510 LOCATE 0,3
520 ?"Year";(PEEK(#846)>>4)*10+PEEK(#846)&#F+2000
521 ?"mon ";(PEEK(#845)>>4)*10+PEEK(#845)&#F;" "
522 ?"wd  ";(PEEK(#844)>>4)*10+PEEK(#844)&#F;" "
523 ?"day ";(PEEK(#843)>>4)*10+PEEK(#843)&#F;" "
524 ?"hor ";(PEEK(#842)>>4)*10+PEEK(#842)&#F;" "
525 ?"min ";(PEEK(#841)>>4)*10+PEEK(#841)&#F;" "
526 ?"sec ";(PEEK(#840)>>4)*10+PEEK(#840)&#F;" "
600 'loop
610 WAIT 10
620 GOTO 420
```

## Parts
- NXP PCF2129

## GitHub
- https://github.com/FaBoPlatform/FaBo/tree/master/215_rtc
