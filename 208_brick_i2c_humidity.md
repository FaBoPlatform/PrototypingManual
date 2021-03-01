# #208 Humidity I2C Brick

<center>![](/img/200_i2c/product/208.jpg)
<!--COLORME-->

## Overview
湿度センサを使用したBrickです。
I2Cでデータを取得できます。

## Connecting
I2Cコネクタへ接続します。

![](/img/200_i2c/connect/208_humidity_connect.jpg)

## Support
|Arduino|RaspberryPi|
|:--:|:--:|
|◯|◯|

## HTS221 Datasheet
| Document |
| -- |
| [HTS221 Datasheet](http://www2.st.com/content/ccc/resource/technical/document/datasheet/4d/9a/9c/ad/25/07/42/34/DM00116291.pdf/files/DM00116291.pdf/jcr:content/translations/en.DM00116291.pdf) |

## Register
| Slave Address |
| -- |
| 0x5F |

## Schematic
![](/img/200_i2c/schematic/208_humidity_hts221.png)

## Library
### for Arduino
- [Arduino IDEからインストール](http://fabo.io/library_install.html)

  ライブラリ名：「FaBo 208 Humidity HTS221」

- [Library GitHub](https://github.com/FaBoPlatform/FaBoHumidity-HTS221-Library)
- [Library Document](http://fabo.io/doxygen/FaBoHumidity-HTS221-Library/)

### for RaspberryPi
- pipからインストール
```
pip install FaBoHumidity_HTS221
```
- [Library GitHub](https://github.com/FaBoPlatform/FaBoHumidity-HTS221-Python)
- [Library Document](http://fabo.io/doxygen/FaBoHumidity-HTS221-Python/)

## Sample Code
### for Arduino
上記のArduino Libraryをインストールし、スケッチの例から、「FaBo 208 Humidity HTS221」→「humidity」を選択してください。

### for RaspberryPi
上記のRaspberryPi Python Libraryをインストールしてからご使用ください。
```python
# coding: utf-8
## @package FaBoHumidity_HTS221
#  This is a library for the FaBo Humidity I2C Brick.
#
#  http://fabo.io/208.html
#
#  Released under APACHE LICENSE, VERSION 2.0
#
#  http://www.apache.org/licenses/
#
#  FaBo <info@fabo.io>

import FaBoHumidity_HTS221
import time
import sys

hts221 = FaBoHumidity_HTS221.HTS221()

try:
    while True:
        humi = hts221.readHumi()
        temp = hts221.readTemp()
        print "Humidity = ", humi
        print "Temp     = ", temp
        print

        time.sleep(1)

except KeyboardInterrupt:
    sys.exit()
```

## Parts
- STMicroelectronics HTS221

## GitHub
- https://github.com/FaBoPlatform/FaBo/tree/master/208_humidity
