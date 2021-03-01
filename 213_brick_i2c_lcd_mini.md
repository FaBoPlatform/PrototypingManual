# #213 LCD mini I2C Brick

<center>![](/img/200_i2c/product/213.jpg)
<!--COLORME-->

## Overview
8桁×2行の小さいLCDを使用したBrickです。

I2Cで表示データを制御できます。

## Connecting
I2Cコネクタへ接続します。

![](/img/200_i2c/connect/213_lcdmini_connect.jpg)

## Support
|Arduino|IchigoJam|
|:--:|:--:|
|◯|◯|

## AQM0802A Datasheet
| Document |
| -- |
| [AQM0802A Datasheet](http://akizukidenshi.com/catalog/g/gP-06669/) |

## Register
| I2C Slave Address |
|:-- |
| 0x3E |

## Schematic
![](/img/200_i2c/schematic/213_lcd_mini.png)

## Library
### for Arduino
- [Arduino IDEからインストール](http://fabo.io/library_install.html)

  ライブラリ名：「FaBo 213 LCD mini AQM0802A」

- [Library GitHub](https://github.com/FaBoPlatform/FaBoLCDmini-AQM0802A-Library)
- [Library Document](http://fabo.io/doxygen/FaBoLCDmini-AQM0802A-Library)

## Sample Code
### for Arduino
上記のArduino Libraryをインストールし、スケッチの例、「FaBo 213 LCD mini AQM0802A」からお選びください。

### for IchigoJam
I2CコネクタにLCD mini I2C Brickを接続し、LCD上に文字を表示させます。
```
10 'LCD INIT
20 CLS

100 D=#3E

200 POKE #800,#40,0,2,#C0,#39,#11,#70,#56,#6C,#38,#0C
210 POKE #810,ASC("F"),ASC("a"),ASC("B"),ASC("o"),ASC("T"),ASC("e"),ASC("s"),ASC("t")

300 'init
310 A=i2cw(D,#801,1,#804,5)
320 wait 10
330 A=i2cw(D,#801,1,#809,2)
340 GOSUB 600

400 'loop
410 ?"Input Key"
420 I=INKEY()
430 POKE #820,I
440 IF I!=0 GOSUB 700
450 GOTO 420

600 'LCD OUT Row 1
610 A=i2cw(D,#801,1,#802,1):'set cursor
620 A=i2cw(D,#800,1,#810,8):'set FaBoTest
630 return

700 'LCD OUT Row 2
710 A=i2cw(D,#801,1,#803,1):'set cursor
720 A=i2cw(D,#800,1,#820,1):'set Input key
730 return
```

## Parts
- AQM0802A

## GitHub
- https://github.com/FaBoPlatform/FaBo/tree/master/213_lcd_mini
