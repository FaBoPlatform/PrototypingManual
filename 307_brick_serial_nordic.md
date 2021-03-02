# #307 BLE Nordic Serial Brick

<center>
![](/img/300_serial/product/307.jpg)
<!--COLORME-->

## Overview
NRF51モジュールを使用したBLE（Bluetooth Low Energy）のBrickです。

シリアルにて制御できるFirmwareが書き込まれているため、Arduino等からシリアル通信にてBLEを制御することができます。

BLEの転送レートは115200bpsに設定してあります。

## Connecting
Serialコネクタへ接続します。

![](/img/300_serial/connect/307_ble_nordic_connect.jpg)

## Support
|Arduino|RaspberryPi|
|:--:|:--:|
|◯|◯|

## MDBT40 Datasheet

|Document|
|--|
|[MDBT40 Datasheet](http://www.raytac.com/download/MDBT40/MDBT40%20spec-Version%20A4.pdf)|

## Schematic
![](/img/300_serial/schematic/307_ble_nordic.png)

## Library

### for Arduino
- [Arduino IDEからインストール](http://fabo.io/library_install.html)

  ライブラリ名：「FaBo 307 BLE Nordic」

- [Library GitHub](https://github.com/FaBoPlatform/FaBoBLE-Nordic-Library)
- [Library Document](http://fabo.io/doxygen/FaBoBLE-Nordic-Library/)

### for RaspberryPi
- pipからインストール
```
pip install FaBoBLE_Nordic
```
- [Library GitHub](https://github.com/FaBoPlatform/FaBoBLE-Nordic-Python)
- [Library Document](http://fabo.io/doxygen/FaBoBLE-Nordic-Python/)

## Sample Code
### for Arduino
上記のArduino Libraryをインストールし、スケッチの例、「FaBo 307 BLE Nordic」からお選びください。

### for RaspberryPi
上記のRaspberryPi Python Libraryをインストールしてからご使用ください。

```python
# coding: utf-8
## @package FaBoBLE_Nordic
#  This is a library for the FaBo BLE_Nordic Brick.
#
#  http://fabo.io/307.html
#
#  Released under APACHE LICENSE, VERSION 2.0
#
#  http://www.apache.org/licenses/
#
#  FaBo <info@fabo.io>

import FaBoBLE_Nordic
import sys

port = '/dev/ttyAMA0'
rate = 115200
print "BLE Nordic SCAN sample"
print "BLE Enable"
ble = FaBoBLE_Nordic.Nordic(port, rate)

#ble.setDebug()

ble.startScan()

while True:
    # BLE内部処理のためloop内で呼び出してください
    ble.tick()

    buff =  ble.getScanData()
    if buff["rssi"]!=0:
        print "Handle:%04x" % long(buff["handle"]),

        print " AddrType:%1x" % buff["addrtype"],

        print " Address:",
        for i in range(6):
            sys.stdout.write('%02x' % buff["address"][i])

        print ' RSSI:%02d' % buff["rssi"],
        if buff["rssi"] > -100:
            sys.stdout.write("  ")

        print " Data:",
        for i in range(buff["data_len"]):
            sys.stdout.write('%02x' % buff["data"][i])
        print
```

## Parts
- raytac MDBT40

## GitHub
- https://github.com/FaBoPlatform/FaBo/tree/master/307_ble_nordic
