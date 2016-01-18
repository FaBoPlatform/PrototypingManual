# #204 Barometer I2C Brick

<center>![](/img/200_i2c/product/204_barometer_product.jpg)
<!--COLORME-->

## Overview
大気圧センサMPL115A2を使用したBrickです。

I2Cでデータを取得できます。

## Connecting
I2Cコネクタへ接続します。

![](/img/200_i2c/connect/204_barometer_connect.jpg)

## Support
|Arduino|RaspberryPI|IchigoJam|
|:--:|:--:|:--:|
|◯|◯|◯|

## Registor
| Slave Address |
| -- |
| 0x60 |

## Datasheet
| Document |
| -- |
| [Datasheet](http://cache.freescale.com/files/sensors/doc/data_sheet/MPL115A2.pdf) |

## Schematic
![](/img/200_i2c/schematic/204_barometer_schematic.png)

## Sample Code
### Arduino
```c
//
// FaBo Brick Sample
//
// brick_i2c_berometer
//

#include <Wire.h>
#define DEVICE_ADDR (0x60) 

void setup()
{
  Serial.begin(9600); // シリアルの開始デバック用
  Wire.begin();       // I2Cの開始
}

void loop()
{ 

  writeI2c(0x12,0x01);
  
  delay(100);
  byte atom_buff[4];
  readI2c(0x00,4, atom_buff);
  int ph = atom_buff[0];
  int pl = atom_buff[1];
  int pressure = (( ph * 256) + pl)/64;
  int th = atom_buff[2];
  int tl = atom_buff[3];
  int temp = ((th * 256 ) + tl)/64 ;
  
  Serial.print("pressure");
  Serial.println(pressure);
  Serial.print("temp");
  Serial.println(temp);

   delay(1000);
}

// I2Cへの書き込み
void writeI2c(byte register_addr, byte value) {
  Wire.beginTransmission(DEVICE_ADDR);  
  Wire.write(register_addr);         
  Wire.write(value);                 
  Wire.endTransmission();        
}

// I2Cへの読み込み
void readI2c(byte register_addr, int num, byte *buf) {
  Wire.beginTransmission(DEVICE_ADDR); 
  Wire.write(register_addr);           
  Wire.endTransmission(false);         

  //Wire.beginTransmission(DEVICE_ADDR); 
  Wire.requestFrom(DEVICE_ADDR, num);  

  int i = 0;
  while (Wire.available())
  {
    byte n = 0x00;
    n = Wire.read();
    *(buf + i) = n;
 
    i++;   
  }
  //Wire.endTransmission();         
}
```

### RaspberryPI

```python
# coding: utf-8
#
# FaBo Brick Sample
#
# brick_i2c_berometer
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

### IchigoJam

```basic
ここにコード書く
```

## Parts
- MPL115A2
