# #403 ColorLED Bar

<center>![](/img/400_led/product/403.jpg)
<!--COLORME-->

## Overview
RGB Color LEDをBar状に5個配置したBrickです。

## Connecting
アナログコネクタ(A0〜A5)、またはデジタルコネクタ(2〜13)のどれかに接続します。

![](/img/400_led/connect/403_ledbar_connect.jpg)

## Support
|Arduino|RaspberryPi|
|:--:|:--:|
|◯|◯|

## WS2812B Datasheet
|Document|
|--|
|[WS2812B Datasheet](http://www.adafruit.com/datasheets/WS2812B.pdf)|

## Schematic
![](/img/400_led/schematic/403_led_bar.png)

## Sample Code
### for Arduino
このサンプルコードでは外部ライブラリを使用します。

https://github.com/adafruit/Adafruit_NeoPixel よりAdafruit_NeoPixelライブラリをインストールしてください。

```c
//
// FaBo Brick Sample
//
// brick_3pin_led
//
// Adafruit_NeoPixel Library Downloads
// https://github.com/adafruit/Adafruit_NeoPixel

#include <Adafruit_NeoPixel.h>

int ledPin = A0;
int numPixels = 5;
Adafruit_NeoPixel pixels = Adafruit_NeoPixel(numPixels, ledPin, NEO_GRB + NEO_KHZ800);

void setup() {
  pixels.begin();
  pixels.show();
}

void loop() {
  int i;
  for(i=0; i<pixels.numPixels(); i++) {
    pixels.setPixelColor(i, pixels.Color(128, 0, 0));
  }
  pixels.show();
  delay(500);

  for(i=0; i<pixels.numPixels(); i++) {
    pixels.setPixelColor(i, pixels.Color(0, 128, 0));
  }
  pixels.show();
  delay(500);

  for(i=0; i<pixels.numPixels(); i++) {
    pixels.setPixelColor(i, pixels.Color(0, 0, 128));
  }
  pixels.show();
  delay(500);

}
```

## Parts
- RGB LED WS2812B

## GitHub
- https://github.com/FaBoPlatform/FaBo/tree/master/403_led_bar
