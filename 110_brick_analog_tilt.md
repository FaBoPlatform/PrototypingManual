# #110 Tilt Brick

<center>![](/img/100_analog/product/110.jpg)
<!--COLORME-->

## Overview
傾斜センサーを使用したBrickです。

I/Oピンより傾斜センサーの状態をデジタル値(0〜1)取得することができます。

黒い部分の中に玉が入っていて傾くとデジタル値が変化します。

LED Brickを点灯/消灯させる際などに使用します。


## Connecting
### Arduino
アナログコネクタ(A0〜A5)、またはデジタルコネクタ(2〜13)のいずれかに接続します。
![](/img/100_analog/connect/110_tilt_connect.jpg)

### Raspberry Pi
GPIOコネクタのいずれかに接続します。

### IchigoJam
OUTコネクタのいずれかに接続します。

## Support
|Arduino|RaspberryPI|IchigoJam|
|:--:|:--:|:--:|
|◯|◯|◯|

## Schematic
![](/img/100_analog/schematic/110_tilt.png)

## Sample Code
### for Arduino

A0コネクタに接続したTilt Brickの傾きによって、D2コネクタに接続したLED Brickを点灯/消灯させています。

```c
//
// FaBo Brick Sample
//
// #110 Tilt Brick
//

#define buttonPin A0
#define ledPin 2

int buttonState = 0;

void setup() {
  pinMode(buttonPin, INPUT);
  pinMode(ledPin, OUTPUT);
}

void loop(){

  buttonState = digitalRead(buttonPin);

  if (buttonState == HIGH) {
    digitalWrite(ledPin, HIGH);
  }
  else {
    digitalWrite(ledPin, LOW);
  }
}
```

### for Raspberry Pi

GPIO5コネクタに接続したTilt Brickの傾きによって、GPIO4コネクタに接続したLED Brickを点灯/消灯させています。

```python
#!/usr/bin/env python
# coding: utf-8

#
# FaBo Brick Sample
#
# #110 Tilt  Brick
#

import RPi.GPIO as GPIO

LED_PIN = 4
TILT_PIN = 5

GPIO.setwarnings(False)
GPIO.setmode(GPIO.BCM)
GPIO.setup(LED_PIN, GPIO.OUT)
GPIO.setup(TILT_PIN, GPIO.IN)

if __name__ == '__main__':
    try:
        while True:
            if(GPIO.input(TILT_PIN)):
                GPIO.output(LED_PIN, True)
            else:
                GPIO.output(LED_PIN, False)

    except KeyboardInterrupt:
        GPIO.cleanup()
```

### for Ichigojam
IN1コネクタに接続したTilt Brickの傾きによって、LEDコネクタに接続したLED Brickの点灯/消灯を制御しています。

```
100 ' IN(n) sample program
110 B=IN(1)
120 IF B=1 LED 1 ELSE LED 0
130 GOTO 110
```

### for Edison
A0コネクタに接続したTilt Brickの傾きによって、D2コネクタに接続したLED Brickを点灯/消灯させています。

```js
//
// FaBo Brick Sample
//
// #110 Tilt Brick
//

//library
var m = require('mraa');

//pin setup
var myTilt = new m.Gpio(14); //Tilt A0
var myLed  = new m.Gpio(2);  //LED D2

myTilt.dir(m.DIR_IN);     //Tilt input
myLed.dir(m.DIR_OUT);     //LED output

//call loop function
loop();

function loop()
{

  if (myTilt.read()){
    myLed.write(1);
  }
  else {
    myLed.write(0);
  }

  setTimeout(loop,100);
}
```

## Parts
- 傾斜(振動)スイッチ

## GitHub
- https://github.com/FaBoPlatform/FaBo/tree/master/110_tilt
