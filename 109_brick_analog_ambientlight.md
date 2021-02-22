# #109 Light Brick

<center>![](/img/100_analog/product/109.jpg)
<!--COLORME-->

## Overview
CDSセルを使用した光センサーBrickです。

周囲の明るさの変化をアナログ値として取得することができます。

## Connecting
### Arduino
アナログコネクタ(A0〜A5)のいずれかに接続します。

![](/img/100_analog/connect/109_ambientlight_connect.jpg)

### Raspberry Pi
アナログコネクタ(A0〜A7)のいずれかに接続します。

### IchigoJam
アナログ用コネクタ(IN2またはANA()で設定したコネクタ)のどれかに接続します。

## Support
|Arduino|RaspberryPi|IchigoJam|
|:--:|:--:|:--:|
|◯|◯|◯|

## Parts Specification
| Document |
|:--|
| [MI527](http://akizukidenshi.com/catalog/g/gI-00110/) |

## Schematic
![](/img/100_analog/schematic/109_light.png)

## Sample Code
### for Arduino
A0コネクタにLight Brickを接続して、明るさに応じたアナログ値をシリアルモニタへ出力します。

```c
//
// FaBo Brick Sample
//
// #109 Light Brick
//

#define lightPin A0

int lightValue = 0;

void setup() {
  pinMode(lightPin,INPUT);
  Serial.begin(9600);
}

void loop() {
  lightValue = analogRead(lightPin);
  Serial.println(lightValue);
  delay(100);
}
```

### for Raspberry Pi
A0コネクタにLight Brickを接続して、GPIO4コネクタに接続したLED Brickの明るさ調節に使用しています。
```python
#!/usr/bin/env python
# coding: utf-8

#
# FaBo Brick Sample
#
# #109 Light Brick
#

import RPi.GPIO as GPIO
import spidev
import time
import sys

# A0コネクタにLightを接続
LIGHTPIN = 0

# GPIO4コネクタにLEDを接続
LEDPIN = 4

# GPIOポートを設定
GPIO.setwarnings(False)
GPIO.setmode( GPIO.BCM )
GPIO.setup( LEDPIN, GPIO.OUT )

# PWM/100Hzに設定
LED = GPIO.PWM(LEDPIN, 100)

# 初期化
LED.start(0)
spi = spidev.SpiDev()
spi.open(0,0)

def readadc(channel):
	adc = spi.xfer2([1,(8+channel)<<4,0])
	data = ((adc[1]&3) << 8) + adc[2]
	return data

def arduino_map(x, in_min, in_max, out_min, out_max):
	return (x - in_min) * (out_max - out_min) // (in_max - in_min) + out_min

if __name__ == '__main__':
	try:
		while True:
			data = readadc(LIGHTPIN)
			print("adc : {:8} ".format(data))
			value = arduino_map(data, 0, 1023, 0, 100)
			LED.ChangeDutyCycle(value)
			time.sleep( 0.01 )
	except KeyboardInterrupt:
		LED.stop()
		GPIO.cleanup()
		spi.close()
		sys.exit(0)
```

### for IchigoJam
#####注意<br>アナログはIN2のみで数値取得可能です。
デジタルの場合はIN(2)、アナログの場合がANA(2)とします。

- デジタル<br>
光の変化によって0か1を返します。<br>
- アナログ<br>
光の変化によって0から1023を返します。<br>

```
100 'LIGHT_sample_program
110 CLS
120 LOCATE 10,8:PRINT "Digital =";IN(2)
130 LOCATE 10,9:PRINT "Analog  =";ANA(2);"  "
140 GOTO 120
```

画面に数字が2つ表示されます。<br>
それぞれリアルタイムで光の変化で数値が変化します。
デジタル数値は明るいと０、暗いと１に変化し、アナログ数値は明るいと小さい値（0に近づく）に、暗いと大きい値（1023に近づく）に変化します。


### for Edison
A0コネクタにLight Brickを接続して、明るさに応じたアナログ値をコンソールへ出力します。

```js
//
// FaBo Brick Sample
//
// #109 Light Brick
//

//library
var m = require('mraa');

//pin setup
var light_pin = new m.Aio(0); //light sensor pin A0

//call loop function
loop();

function loop()
{

  var value = light_pin.read()
  console.log('light: ' + value);

  //500 milliseconds
  setTimeout(loop, 500);
}
```

## Parts
- CDSセル(5mm)

## GitHub
- https://github.com/FaBoPlatform/FaBo/tree/master/109_light
