# #107 LimitSwitch Brick

<center>![](/img/100_analog/product/107.jpg)
<!--COLORME-->

## Overview
リミットスイッチを使ったBrickです。

I/OピンよりスイッチのON/OFFの状態を取得することができます。

機械の自動停止や位置検出の際に使用します。

## Connecting

### Arduino
アナログコネクタ(A0〜A5)、またはデジタルコネクタ(2〜13)のいずれかに接続します。

![](/img/100_analog/connect/107_limitswitch_connect.jpg)

###Raspberry PI
GPIOコネクタのいずれかに接続します。

### IchigoJam
OUTコネクタのいずれかに接続します。


## Support
|Arduino|RaspberryPi|IchigoJam|
|:--:|:--:|:--:|
|◯|◯|◯|

## Schematic
![](/img/100_analog/schematic/107_limitswitch.png)

## Sample Code
### for Arduino
A0コネクタにLimitSwitch Brickを接続し、D2コネクタに接続したLED Brickの点灯/消灯を制御しています。
```c
//
// FaBo Brick Sample
//
// #107 LimitSwitch Brick
//

#define buttonPin A0 // リミットスイッチピン
#define ledPin 2     // LEDピン

// リミットスイッチの状況取得用
int buttonState = 0;

void setup() {
  // リミットスイッチピンを入力用に設定
  pinMode(buttonPin, INPUT);
  // LEDピンを出力用に設定
  pinMode(ledPin, OUTPUT);
}

void loop(){
  // リミットスイッチの押下状況を取得
  buttonState = digitalRead(buttonPin);

  // リミットスイッチ判定
  if (buttonState == LOW) {
    // LED点灯
    digitalWrite(ledPin, HIGH);
  }
  else {
    // LED消灯
    digitalWrite(ledPin, LOW);
  }
}
```

### for RaspberryPi
GPIO5コネクタにLimitSwitch Brickを接続し、GPIO4コネクタに接続したLED Brickの点灯/消灯を制御しています。

```python
# coding: utf-8
#
# FaBo Brick Sample
#
# brick_analog_limitswitch
#

import RPi.GPIO as GPIO
import time

LEDPIN = 4
LSPIN = 5   #LimitSwitch pin

led_state = 0

GPIO.setwarnings(False)
GPIO.setmode( GPIO.BCM )
GPIO.setup( LEDPIN, GPIO.OUT )
GPIO.setup( LSPIN, GPIO.IN)

while True:
    if( GPIO.input( LSPIN ) ):
         led_state = 1 - led_state
    GPIO.output( LEDPIN, led_state )
    print "led_state: %d " % led_state
    time.sleep(0.2)
```

### for Ichigojam
IN1コネクタにLimitSwitch Brickを接続し、LEDコネクタに接続したLED Brickの点灯/消灯を制御しています。

```
100 ' IN(n) sample program
110 B=IN(1)
120 IF B=1 LED 1 ELSE LED 0
130 GOTO 110
```

### for Edison
A0コネクタにLimitSwitch Brickを接続し、D2コネクタに接続したLED Brickの点灯/消灯を制御しています。

```js
//
// FaBo Brick Sample
//
// #107 LimitSwitch Brick
//

//library
var m = require('mraa');

//pin setup
var myButton = new m.Gpio(14); //Button A0
var myLed    = new m.Gpio(2);  //LED D2

myButton.dir(m.DIR_IN);     //Button input
myLed.dir(m.DIR_OUT);       //LED output

//call loop function
loop();


function loop()
{

  if (myButton.read()){
    myLed.write(1);
  }
  else {
    myLed.write(0);
  }

  setTimeout(loop,100);
}
```

## Parts
- リミットスイッチ

## GitHub
- https://github.com/FaBoPlatform/FaBo/tree/master/107_limitswitch
