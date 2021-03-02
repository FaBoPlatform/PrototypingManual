#3Pin LED Brick


RGB Color LEDを使ったBrickです。

## Connecting
A0コネクタに接続します。

## Support
| Arduino | RaspberryPi | IchigoJam |
| <center>○ | <center>× | <center>× |

## Schematic
![](brick_3pin_led_1x1_sch.png)
![](brick_3pin_led_ring_sch.png)

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
int numPixels = 12;
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

