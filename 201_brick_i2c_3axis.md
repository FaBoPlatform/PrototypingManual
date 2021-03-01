# #201 3Axis I2C Brick

<center>![](/img/200_i2c/product/201.jpg)
<!--COLORME-->

## Overview
3軸加速度センサーを使用したBrickです。

I2Cで3軸の加速度データを取得することがきます。

## センサー取得データについて
このBrickでは下の図の3軸の値を取得します。

![](/img/200_i2c/docs/201_3axis_docs_001.jpg)

それぞれ矢印の方向に力がかかるとプラス、逆方向ではマイナスとなります。

なお、このBrickを水平に置いた場合、重力がZ軸にかかっている状態となるので、X軸、Y軸が0に近く、Z軸のみ高い値となります。

## Connecting
4Pinケーブルで、OUT/INシールドのI2Cコネクタへ接続します。

※4Pinケーブル、各種OUT/INシールド、各種ケースは別売です。

![](/img/200_i2c/connect/201_3axis_connect.jpg)

写真はArduinoでの接続例です。

## Support
|Arduino|RaspberryPi|IchigoJam|NRF51|Edison|
|:--:|:--:|:--:|:--:|:--:|
|◯|◯|◯|◯|◯|

## ADXL345 Datasheet
| Document |
|:--|
| [ADXL345 Datasheet](http://www.analog.com/media/en/technical-documentation/data-sheets/ADXL345.pdf) |

## Register
| I2C Slave Address |
|:-- |
| 0x53 |

## Schematic
![](/img/200_i2c/schematic/201_3axis.png)

## Library
### for Arduino
- [Arduino IDEからインストール](http://fabo.io/library_install.html)

  ライブラリ名：「FaBo 201 3Axis ADXL345」

- [Library GitHub](https://github.com/FaBoPlatform/FaBo3Axis-ADXL345-Library)
- [Library Document](http://fabo.io/doxygen/FaBo3Axis-ADXL345-Library/)

### for RaspberryPi
- pipからインストール
```
pip install FaBo3Axis_ADXL345
```
- [Library GitHub](https://github.com/FaBoPlatform/FaBo3Axis-ADXL345-Python)
- [Library Document](http://fabo.io/doxygen/FaBo3Axis-ADXL345-Python/)

## Sample Code
### for Arduino
I2Cコネクタに接続した3Axis I2C Brickより３軸の加速度情報を取得し、シリアルモニタに出力します。
```c
//
// FaBo Brick Sample
//
// #201 3AXIS I2C Brick
//

#include <Wire.h>

#define DEVICE_ADDR (0x53) // スレーブデバイスのアドレス
byte axis_buff[6];

void setup()
{
  Serial.begin(9600); // シリアルの開始デバック用
  Wire.begin();       // I2Cの開始

  // 生存確認
  Serial.println("Checking I2C device...");
  byte who_am_i = 0x00;
  readI2c(0x00, 1, &who_am_i);
  if(who_am_i == 0xe5){
    Serial.println("I am ADXL345");
  }else{
    Serial.println("Not detected");
  }

  // 初期化
  Serial.println("Init...");
  // DATA_FORMAT
  writeI2c(0x31, 0x00);
  // POWER_TCL
  writeI2c(0x2d, 0x08);
}

void loop()
{
  uint8_t length = 6;
  readI2c(0x32, length, axis_buff); //レジスターアドレス 0x32から6バイト読む
  int x = (((int)axis_buff[1]) << 8) | axis_buff[0];
  int y = (((int)axis_buff[3]) << 8) | axis_buff[2];
  int z = (((int)axis_buff[5]) << 8) | axis_buff[4];
  Serial.print("x: ");
  Serial.print( x );
  Serial.print(" y: ");
  Serial.print( y );
  Serial.print(" z: ");
  Serial.println( z );

  delay(100);
}

// I2Cへの書き込み
void writeI2c(byte register_addr, byte value) {
  Wire.beginTransmission(DEVICE_ADDR);
  Wire.write(register_addr);
  Wire.write(value);
  Wire.endTransmission();
}

// I2Cへの読み込み
void readI2c(byte register_addr, int num, byte buffer[]) {
  Wire.beginTransmission(DEVICE_ADDR);
  Wire.write(register_addr);
  Wire.endTransmission();

  Wire.beginTransmission(DEVICE_ADDR);
  Wire.requestFrom(DEVICE_ADDR, num);

  int i = 0;
  while(Wire.available())
  {
    buffer[i] = Wire.read();
    i++;
  }
  Wire.endTransmission();
}
```

### for Raspberry Pi
I2Cコネクタに接続した3Axis I2C Brickより３軸の加速度情報を取得し、コンソールに出力します。

```python
# coding: utf-8

import smbus
import time


ADDRESS = 0x53
CHANNEL = 1

DATA_FORMAT = 0x31
POWER_CTL = 0x2d
DATA_XYZ = 0x32

bus = smbus.SMBus(CHANNEL)


class ADXL345:
	def __init__(self, address):
		self.address = address
		bus.write_byte_data(self.address, DATA_FORMAT, 0x00)
		bus.write_byte_data(self.address, POWER_CTL, 0x08)

	def read(self):
		data = bus.read_i2c_block_data(self.address, DATA_XYZ, 6)

		x = data[0] | (data[1] << 8)
		if(x & (1 << 16 - 1)):
			x = x - (1<<16)

		y = data[2] | (data[3] << 8)
		if(y & (1 << 16 - 1)):
			y = y - (1<<16)

		z = data[4] | (data[5] << 8)
		if(z & (1 << 16 - 1)):
			z = z - (1<<16)

		return {"x": x, "y": y, "z": z}


if __name__ == "__main__":
	adxl345 = ADXL345(ADDRESS)

	while True:
		axes = adxl345.read()
		print " x = " , ( axes['x'] )
		print " y = " , ( axes['y'] )
		print " z = " , ( axes['z'] )
		print
		time.sleep(1)

```

### for Ichigojam
I2Cコネクタに接続した3Axis I2C Brickより３軸の加速度情報を取得し、画面上に出力します。
```
10 'FaBo Brick Sample
20 '#201 3axis I2C Brick
30 CLS
100 'Slave address
110 C=#53
200 'Address set
210 POKE #800,#31,0,#2D,8
220 POKE #810,#32,6
300 'init
310 A=I2CW(C,#800,1,#801,1)
320 A=I2CW(C,#802,1,#803,1)
400 'Read 3axis data
410 A=I2CW(C,#810,1,#811,1)
420 A=I2CR(C,#810,1,#820,6)
500 'Output
510 LOCATE 0,3
520 ?"X:";PEEK(#820)+PEEK(#821)*256;"  "
530 ?"Y:";PEEK(#822)+PEEK(#823)*256;"  "
540 ?"Z:";PEEK(#824)+PEEK(#825)*256;"  "
600 'loop
610 WAIT 5
620 GOTO 410
```


### for NRF51
* PackはnRF_Librariesのapp_twiとapp_traceを読み込む
![](/img/200_i2c/docs/201_3axis_nrf_001.png)
![](/img/200_i2c/docs/201_3axis_nrf_002.png)
![](/img/200_i2c/docs/201_3axis_nrf_003.png)

``` C
#include "app_trace.h"
#include "app_error.h"
#include "app_twi.h"
#include "app_util_platform.h"
#include "nrf_delay.h"

// I2Cのピン番号
#define I2C_SCL_PIN 7
#define I2C_SDA_PIN 30

// 最大トランザクション数
#define MAX_PENDING_TRANSACTIONS    5

// スレーブアドレス
static uint16_t device_address = 0x53;

// Twiインスタンス
static app_twi_t m_app_twi = APP_TWI_INSTANCE(0);

// Twi初期化
void twi_init (void)
{
	ret_code_t err_code;

	const nrf_drv_twi_config_t twi_config = {
		 .scl                = I2C_SCL_PIN,
		 .sda                = I2C_SDA_PIN,
		 .frequency          = NRF_TWI_FREQ_100K,
		 .interrupt_priority = APP_IRQ_PRIORITY_LOW
	};

	APP_TWI_INIT(&m_app_twi, &twi_config, MAX_PENDING_TRANSACTIONS, err_code);
	APP_ERROR_CHECK(err_code);
}

// 指定レジスタに1Byte書き込む
void twi_write_byte(uint8_t register_address, uint8_t data)
{
	uint8_t buff[] = {register_address, data};
	app_twi_transfer_t const transfers[] = {
		APP_TWI_WRITE(device_address, buff, 2, 0),
	};

	APP_ERROR_CHECK(app_twi_perform(&m_app_twi, transfers, 1, NULL));
}

// 指定レジスタを読み込む
void twi_read(uint8_t register_address, char *buff, uint8_t size)
{
	app_twi_transfer_t const transfers[] = {
		APP_TWI_WRITE(device_address, &register_address, 1, APP_TWI_NO_STOP),
		APP_TWI_READ (device_address, buff, size, 0)
	};

	APP_ERROR_CHECK(app_twi_perform(&m_app_twi, transfers, 2, NULL));
}

// 指定レジスタを1Byteだけ読み込む
uint8_t twi_read_byte(uint8_t register_address)
{
	char buff = 0;
	twi_read(register_address, &buff, 1);
	return buff;
}

// メイン
int main() {
	app_trace_init();
	printf("start\n");

	twi_init();

	// デバイスID確認
	uint8_t dev = twi_read_byte(0x00);
	if (dev == 0xe5) {
		printf("I am ADXL345");
	} else {
		printf("device:%d\n", dev);
		return 0;
	}

	// データフォーマットをFULL_RES, r-Justify, +-2gに設定
	twi_write_byte(0x31, 0x0F);
	// 計測モードに設定
	twi_write_byte(0x2d, 0x08);

	// 加速度を計測
	char buff[6] = {0};
	while(true) {
		__WFE();
		nrf_delay_ms(500);
		twi_read(0x32, buff, 6);
		short x = buff[1] << 8 | buff[0];
		short y = buff[3] << 8 | buff[2];
		short z = buff[5] << 8 | buff[4];
		printf("x:%d, y:%d, z:%d\n", x, y, z);
	}

}

```


### for Edison
Node.js用のサンプルです。

I2Cコネクタに接続した3Axis I2C Brickより３軸の加速度情報を取得し、コンソールに出力します。

```js
//
// FaBo Brick Sample
//
// #201 3axis i2c Brick
//

var m = require('mraa');

var i2c = new m.I2c(0);

i2c.address(0x53);

var buff = new Buffer(6);

i2c.writeReg(0x31, 0x00);
i2c.writeReg(0x2d, 0x08);

loop();

function loop()
{
    buff = i2c.readBytesReg(0x32, 6);

    var x = buff[0] + (buff[1]<<8);

    if(x & (1 << 16 - 1)){
        x = x - (1<<16);
    }

    var y = buff[2] + (buff[3]<<8);
    if(y & (1 << 16 - 1)){
        y = y - (1<<16);
    }

    var z = buff[4] + (buff[5]<<8);
    if(z & (1 << 16 - 1)){
        z = z - (1<<16);
    }

    console.log("x:"+x);
    console.log("y:"+y);
    console.log("z:"+z);

    setTimeout(loop,500);
}

```

## Parts
- Analog Devices ADXL345

## GitHub
- https://github.com/FaBoPlatform/FaBo/tree/master/201_3axis
