# #204 Barometer I2C Brick

<center>![](/img/200_i2c/product/204.jpg)
<!--COLORME-->

## Overview
大気圧センサを使用したBrickです。

I2Cでデータを取得できます。

## Connecting
I2Cコネクタへ接続します。

![](/img/200_i2c/connect/204_barometer_connect.jpg)

## Support
|Arduino|RaspberryPi|
|:--:|:--:|
|◯|◯|

## MPL115A2 Datasheet
| Document |
| -- |
| [MPL115A2 Datasheet](http://cache.freescale.com/files/sensors/doc/data_sheet/MPL115A2.pdf) |

## Register
| Slave Address |
| -- |
| 0x60 |

## Schematic
![](/img/200_i2c/schematic/204_barometer.png)

## Library
### for Arduino
- [Arduino IDEからインストール](http://fabo.io/library_install.html)

  ライブラリ名：「FaBo 204 Baromter MPL115A2」

- [Library GitHub](https://github.com/FaBoPlatform/FaBoBarometer-MPL115-Library)
- [Library Document](http://fabo.io/doxygen/FaBoBarometer-MPL115-Library/)

### for RaspberryPi
- pipからインストール
```
pip install FaBoBarometer_MPL115
```
- [Library GitHub](https://github.com/FaBoPlatform/FaBoBarometer-MPL115-Python)
- [Library Document](http://fabo.io/doxygen/FaBoBarometer-MPL115-Python/)

## Sample Code
I2Cコネクタに接続したBarometer Brickより、気圧、温度、標高212mの気圧を取得し、シリアルモニタに出力します。
### for Arduino
```c
//
// FaBo Brick Sample
//
// 204_barometer
//

#include <Wire.h>
#define DEVICE_ADDR (0x60)

float a0, b1, b2, c12, c11, c22;

void setup() {
  Serial.begin(9600); // シリアルの開始デバック用
  Wire.begin(); // I2Cの開始

  Wire.beginTransmission(DEVICE_ADDR);
  Wire.write(0x04); // 校正データの要求
  Wire.endTransmission();

  Wire.requestFrom(DEVICE_ADDR, 12);
  a0  = read_coef(16,  3, 0);
  b1  = read_coef(16, 13, 0);
  b2  = read_coef(16, 14, 0);
  c12 = read_coef(14, 13, 9);
  c11 = read_coef(11, 10, 11);
  c22 = read_coef(11, 10, 15);

}

void loop() {
  // 気圧
  Serial.print(read_hpa());
  Serial.println(" hPa");
  // 温度
  Serial.print(read_temp());
  Serial.println(" C");

  // 会津若松の標高:212mの気圧
  Serial.println("");
  Serial.print(read_hpa_alt(212.0));
  Serial.println(" hPa");
  Serial.println();

  delay(1000);
}



float read_coef(int total, int fractional, int zero) {
  unsigned char msb, lsb;
  msb = Wire.read();
  lsb = Wire.read();
  return ((float) ((msb << 8) + lsb) / ((long)1 << 16 - total + fractional + zero));
}

unsigned int read_adc() {
  unsigned char msb, lsb;
  msb = Wire.read();
  lsb = Wire.read();
  return (((unsigned int)msb << 8) + lsb) >> 6;;
}

float read_hpa_alt(float altitude) {
  float hpa,temp;
  get_data(&hpa, &temp);
  return hpa / pow(1-( altitude / 44330.0 ), 5.255);

}

float read_hpa() {
  float hpa,temp;
  get_data(&hpa, &temp);
  return hpa;
}

float read_temp() {
  float hpa,temp;
  get_data(&hpa, &temp);
  return temp;
}

void get_data(float *hpa, float *temp) {
  Wire.beginTransmission(DEVICE_ADDR);
  Wire.write(0x12); // 計測開始を指示
  Wire.write(0x01);
  Wire.endTransmission();
  delay(3);
  Wire.beginTransmission(DEVICE_ADDR);
  Wire.write(0x00); // 計測データの要求
  Wire.endTransmission();
  Wire.requestFrom(DEVICE_ADDR, 4);
  unsigned int Padc = read_adc();
  unsigned int Tadc = read_adc();
  float Pcomp = a0 + (b1 + c11 * Padc + c12 * Tadc) * Padc + (b2 + c22 * Tadc) * Tadc;
  *hpa = Pcomp * 650 / 1023 + 500;
  *temp = 25 - (Tadc - 472) / 5.35;
}

```

### for Raspberry Pi
I2Cコネクタに接続したBarometer Brickより、気圧、温度を取得し、シリアルモニタに出力します。

```python
# coding: utf-8
#
# FaBo Brick Sample
#
# #204 Barometer I2C Brick
#

import smbus
import time

ADDRESS = 0x60
CHANNEL = 1

COEF_REQ = 0x04
COEF_DATA = 0x04
CONV_REQ = 0x12
HPA_DATA = 0x00

bus = smbus.SMBus(CHANNEL)

class MPL115A2:
	def __init__(self, address):
		self.address = address

	def conv_coef(self, msb, lsb, total, fractional, zero):
		data = (msb << 8) | lsb
		rate = float(1 << 16 - total + fractional + zero)

		if (msb >> 7) == 0:
			result = float(data / rate)
		else:
			result = -float(((data ^ 0xFFFF) + 1) / rate)

		return result

	def conv_adc(self, msb, lsb):
		data = ( (msb << 8) | lsb ) >> 6
		return data

	def read_coef(self):
		bus.write_byte_data(self.address, COEF_REQ, 0x01)
		data = bus.read_i2c_block_data(self.address, COEF_DATA, 12)

		a0  = self.conv_coef(data[0],  data[1],  16,  3, 0)
		b1  = self.conv_coef(data[2],  data[3],  16, 13, 0)
		b2  = self.conv_coef(data[4],  data[5],  16, 14, 0)
		c12 = self.conv_coef(data[6],  data[7],  14, 13, 9)
		c11 = self.conv_coef(data[8],  data[9],  11, 10, 11)
		c22 = self.conv_coef(data[10], data[11], 11, 10, 15)

		return {"a0": a0, "b1": b1, "b2": b2, "c12": c12, "c11": c11, "c22": c22}

	def read_hpa(self,coef):
		bus.write_byte_data(self.address, CONV_REQ, 0x01)

		time.sleep(0.003)

		bus.write_byte_data(self.address, HPA_DATA, 0x00)
		adata = bus.read_i2c_block_data(self.address, HPA_DATA, 4)

		padc = self.conv_adc(adata[0], adata[1])
		tadc = self.conv_adc(adata[2], adata[3])

		pcomp = coef['a0'] + ( coef['b1'] + coef['c11'] * padc + coef['c12'] * tadc ) \
			* padc + ( coef['b2'] + coef['c22'] * tadc ) * tadc
		hpa = pcomp * 650 / 1023 + 500
		temp = 25 - (tadc - 472) / 5.35;

		return {"hpa": hpa, "temp": temp}

if __name__ == "__main__":
	mpl = MPL115A2(ADDRESS)

	coef = mpl.read_coef()

	while True:
		data = mpl.read_hpa(coef)
		print " hpa = " , ( data['hpa'] )
		print " temp = " , ( data['temp'] )
		print
		time.sleep(1)

```


## Parts
- Freescale(NXP) MPL115A2

## GitHub
- https://github.com/FaBoPlatform/FaBo/tree/master/204_barometer
