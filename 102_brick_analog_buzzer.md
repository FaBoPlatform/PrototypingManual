# #102 Buzzer Brick

<center>![](/img/100_analog/product/102.jpg)
<!--COLORME-->

## Overview
圧電ブザーを使ったBrickです。I/Oピンより、鳴らす音や音の長さを制御することができます。

## Connecting
### Arduino
アナログコネクタ(A0〜A5)、またはデジタルコネクタ(2〜13)のいずれかに接続します。

![](/img/100_analog/connect/102_buzzer_connect.jpg)

## Support
|Arduino|RaspberryPI|IchigoJam|
|:--:|:--:|:--:|
|◯|◯|◯|

## Schematic
![](/img/100_analog/schematic/102_buzzer.png)

## Sample Code
### for Arduino
D2コネクタにBuzzer Brickを接続し、ビープ音を鳴らしています。
```c
//
// FaBo Brick Sample
//
// #102 Buzzer Brick
//

#define buzzerPin 2 // ブザーピンの設定

int duration = 500; // 音を鳴らす時間

void setup() {
  // ブザーピンを出力用に設定
  pinMode(buzzerPin,OUTPUT);
}

void loop() {
  tone(buzzerPin,262,duration); // ド
  delay(duration);

  tone(buzzerPin,294,duration); // レ
  delay(duration);

  tone(buzzerPin,330,duration); // ミ
  delay(duration);

  delay(1000);
}
```
tone関数にて出力できる音階と周波数は下記のようになります。
<br>
数値が大きくなるほど音が高くなります。

| | ド | ド♯ | レ | レ♯ | ミ | ファ | ファ♯ | ソ | ソ♯ | ラ | ラ♯ | シ |
|  -- | -- |-- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
|  1 | 131 | 139 | 147 | 156 | 165 | 175 | 185 | 196 | 208 | 220 | 233 | 247 |
| 2 | 262 | 277 | 294 | 311 | 330 | 349 | 370 | 392 | 415 | 440 | 466 | 494 |
 | 3 | 523 | 554 | 587 | 622 | 659 | 698 | 740 | 784 | 831 | 880 | 932 | 988 |

その他の音階については下記をご参照下さい。

https://www.arduino.cc/en/Tutorial/ToneMelody?from=Tutorial.Tone

### for Raspberry Pi
GO4コネクタにBuzzer Brickを接続し、ビープ音を鳴らしています。

```python
# coding: utf-8
# FaBo Brick Sample
# #102 Buzzer Brick

import RPi.GPIO as GPIO
import time

BUZZER_PIN = 4
DURATION = 0.5
DUTY = 50 # 0 - 100

DO = 262
RE = 294
MI = 330

GPIO.setmode(GPIO.BCM)
GPIO.setup(BUZZER_PIN, GPIO.OUT)

buzzer = GPIO.PWM(BUZZER_PIN, 1000)

try:
    while True:
        buzzer.ChangeFrequency(DO)
        buzzer.start(DUTY)
        time.sleep(DURATION)

        buzzer.ChangeFrequency(RE)
        buzzer.start(DUTY)
        time.sleep(DURATION)

        buzzer.ChangeFrequency(MI)
        buzzer.start(DUTY)
        time.sleep(DURATION)

        buzzer.stop()
        time.sleep(DURATION)

except KeyboardInterrupt:

    buzzer.stop()
    GPIO.cleanup()
```

### for IchigoJam
シールドのSOUNDコネクタにBUZZER Brickを接続する。
- ビープ音を鳴らします。

BEEP
```
100 BEEP
```


- MML形式で音を鳴らします。

「ド」は C、「レ」は D　で書きます。※音階は下記のリスト参照。

| C | D | E | F | G | A | B |
| -- | -- | -- | -- | -- | -- | -- | -- |
| ド | レ | ミ | ファ | ソ | ラ | シ |

半音（シャープ）は、C+や、F-　と記述します。<br>

オクターブの変更は　<（下げる）  >（上げる） を使います。

T[値]でテンポを変更できます。

音階の後に数値（1,2,4,8,16,32）をつけると、音の長さを変えられます。（n 分音符。デフォルトは４）
C16 = 16分音符のド

```
100 PLAY"T500CDEFGAB<C2"
```
上記のプログラムを実行すると、<BR>
ドレミファソラシドー<br>
と、最後のドだけ少し長く再生されます。

####注意<br>
一度に再生できるPLAY文は一つだけで、最後に実行したPLAY文のみ再生されます。<br>
```
100 PLAY"CDEFGAB<C"
110 PLAY"F"
```
例えば上記のプログラムを実行すると、「ファ」の１音で終了します。

簡易鍵盤プログラム
```
100 ' BUZZER_sample_program
110 cls
120 PRINT"EASY PIANO"
130 PRINT" SD GHJ"
140 PRINT"ZXCVBNM,"
150 I=INKEY()
160 IF I=ASC("Z") PLAY"C16"
170 IF I=ASC("S") PLAY"C+16"
180 IF I=ASC("X") PLAY"D16"
190 IF I=ASC("D") PLAY"D+16"
200 IF I=ASC("C") PLAY"E16"
210 IF I=ASC("V") PLAY"F16"
220 IF I=ASC("G") PLAY"F+16"
230 IF I=ASC("B") PLAY"G16"
240 IF I=ASC("H") PLAY"G+16"
250 IF I=ASC("N") PLAY"A16"
260 IF I=ASC("J") PLAY"A+16"
270 IF I=ASC("M") PLAY"B16"
280 IF I=ASC(",") PLAY">C16"
290 GOTO 150
```

詳しくは下記のURLを参照してください。<br>
####福野泰介の一日一創 <br>http://fukuno.jig.jp/892

### for Cylon.js

```js
var Cylon = require('cylon');
var melodys = [131,147,165,175,196,220,247];

Cylon.robot({

        connections: {
                arduino: { adaptor: 'firmata', port: '/dev/tty.usbmodem1411' }
        },

        devices: {
                buzzor: { driver: 'direct-pin', pin: 11},
        },

        work: function(my) {
                var i = 0;
                var tones = 1 / (2 * 131) ;
                every((tones).second(), function() {
                        if(i % 2 == 0){
                                my.buzzor.digitalWrite(1024);
                        }else{
                                my.buzzor.digitalWrite(0);
                        }
                        i++;
                });
        }
}).start();
```

## Parts
- 圧電ブザー

## GitHub
- https://github.com/FaBoPlatform/FaBo/tree/master/102_buzzer
