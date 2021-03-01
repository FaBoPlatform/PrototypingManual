# #113 IR Receiver Brick

<center>![](/img/100_analog/product/113.jpg)
<!--COLORME-->

## Overview
フォトトランジスタを使った赤外線受信Brickです。

I/Oピンから赤外線受信のON/OFFを取得することができます。

## Connecting
### Arduino
アナログコネクタ(A0〜A5)、またはデジタルコネクタ(2〜13)のいずれかに接続します。
![](/img/100_analog/connect/113_ir_receiver_connect.jpg)

### IchigoJam
OUTコネクタのいずれかに接続します。

## Support
|Arduino|RaspberryPi|IchigoJam|
|:--:|:--:|:--:|
|◯|◯|◯|

## Parts Specification
| Document |
|:--|
| [L-51ROPT1D1](http://akizukidenshi.com/catalog/g/gI-04211/) |
| [2SC1815L-Y](http://akizukidenshi.com/catalog/g/gI-06475/) |

## Schematic
![](/img/100_analog/schematic/113_ir_receive.png)

## Sample Code
### for Arduino
A0コネクタに赤外線受信Brick、A1コネクタにLED Brickを接続し、赤外線を受信したらLEDを発光させます。

```c
//
// FaBo Brick Sample
//
// #113 IR Receiver Brick
//

#define ir_receivePin A0
#define ledPin A1

int irState = 0;

void setup() {
  pinMode(ir_receivePin, INPUT);
  pinMode(ledPin, OUTPUT);
}

void loop() {
  irState = digitalRead(ir_receivePin);

  if (irState == HIGH) {
    digitalWrite(ledPin, HIGH);
  }
  else {
    digitalWrite(ledPin, LOW);
  }

}
```
### for Raspberry Pi
GPIO5コネクタに赤外線受信Brick、GPIO4コネクタにLED Brickを接続し、赤外線を受信したらLEDを発光させます。

```python
# coding: utf-8
#
# FaBo Brick Sample
#
# #113 IR Receiver Brick
#

import RPi.GPIO as GPIO

LED_PIN = 4
IR_RECEIVER_PIN = 5

GPIO.setwarnings(False)
GPIO.setmode(GPIO.BCM)
GPIO.setup(LED_PIN, GPIO.OUT)
GPIO.setup(IR_RECEIVER_PIN, GPIO.IN)

if __name__ == '__main__':
    try:
        while True:
            if(GPIO.input(IR_RECEIVER_PIN)):
                GPIO.output(LED_PIN, True)
            else:
                GPIO.output(LED_PIN, False)

    except KeyboardInterrupt:
        GPIO.cleanup()

```

### for IchigoJam

IN1コネクタに赤外線受信Brick、LEDコネクタにLED Brickを接続し、赤外線を受信したらLEDを発光させます。

```
100 ' IN(n) sample program
110 B=IN(1)
120 IF B=1 LED 1 ELSE LED 0
130 GOTO 110
```

## Parts
- 赤外線フォトトランジスタ

## GitHub
- https://github.com/FaBoPlatform/FaBo/tree/master/113_ir_receive
