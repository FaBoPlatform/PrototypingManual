# #112 IR LED Brick

<center>![](/img/100_analog/product/112.jpg)
<!--COLORME-->

## Overview
赤外線LEDを使ったBrickです。

I/Oピンから赤外線LEDをON/OFFを制御することができます。

## Connecting
### Arduino
アナログコネクタ(A0〜A5)、またはデジタルコネクタ(2〜13)のいずれかに接続します。

![](/img/100_analog/connect/112_ir_connect.jpg)

### IchigoJam
OUTコネクタのいずれかに接続します。

## Support
|Arduino|RaspberryPi|IchigoJam|
|:--:|:--:|:--:|
|◯|◯|◯|

## Parts Specification
| Document |
|:--|
| [OSI5LA5113A](http://akizukidenshi.com/catalog/g/gI-04311/) |

## Schematic
![](/img/100_analog/schematic/112_ir_led.png)

## Sample Code
### for Arduino
A0コネクタに赤外線LED Brick、A1コネクタにボタンBrickを接続し、ボタンが押されたら赤外線LEDを発光させます。
```c
//
// FaBo Brick Sample
//
// #112 IR LED Brick
//

#define ir_ledPin A0
#define buttonPin A1

int buttonState = 0;

void setup() {
  pinMode(ir_ledPin, OUTPUT);
  pinMode(buttonPin, INPUT);
}

void loop() {
  buttonState = digitalRead(buttonPin);

  if (buttonState == HIGH) {
    digitalWrite(ir_ledPin, HIGH);
  }
  else {
    digitalWrite(ir_ledPin, LOW);
  }

}
```

## Arduinoでの使用例
### Sample Connecting
- A0 コネクタにIR LED Brick
- A1 コネクタにIR Reciver Brick
- A2 コネクタにLED Brick

A0からA2に上記のBrickを接続し,IR LED Brickから出る赤外線をIR Reciver Brickで受信します。

![](/img/100_analog/connect/112_ir_connect_2.jpg)

IR LEDより発信した赤外線をIR Receiverにて受信した場合にLEDが点灯します。

![](/img/100_analog/connect/112_ir_connect_lecture_2.jpg)

### Sample Code
```c
//
// FaBo Brick Sample
//
// brick_analog_ir_led and brick_analog_ir_receive Operation check.
//

int ir_ledPin = A0;
int ir_receivePin = A1;
int ledPin = A2;

int irState = 0;

void setup() {
  pinMode(ir_ledPin, OUTPUT);
  pinMode(ir_receivePin, INPUT);
  pinMode(ledPin, OUTPUT);
}

void loop() {
  irState = digitalRead(ir_receivePin);
    digitalWrite(ir_ledPin, HIGH);
  if (irState == HIGH) {
    digitalWrite(ledPin, HIGH);
  }
  else {
    digitalWrite(ledPin, LOW);
  }

}
```

### for Raspberry Pi
A0コネクタに赤外線LED Brickを接続し、１秒間隔で赤外線LEDを発光させます。

```python
# coding: utf-8
#
# FaBo Brick Sample
#
# #112 IR LED Brick
#

import RPi.GPIO as GPIO
import time

IR_LED_PIN = 4

GPIO.setwarnings(False)
GPIO.setmode(GPIO.BCM)
GPIO.setup(IR_LED_PIN, GPIO.OUT)

if __name__ == '__main__':
    try:
        while True:
            GPIO.output(IR_LED_PIN, True)
            time.sleep(1.0)
            GPIO.output(IR_LED_PIN, False)
            time.sleep(1.0)

    except KeyboardInterrupt:
        GPIO.cleanup()

```

### for Ichigojam

IN1コネクタにButton Brickを接続し、LEDコネクタに接続したIR LED Brickの点灯/消灯を制御しています。
```
100 ' IN(n) sample program
110 B=IN(1)
120 IF B=1 LED 1 ELSE LED 0
130 GOTO 110
```
## Parts
- 5mm 赤外線LED OSI5LA5113A

## GitHub
- https://github.com/FaBoPlatform/FaBo/tree/master/112_ir_led
