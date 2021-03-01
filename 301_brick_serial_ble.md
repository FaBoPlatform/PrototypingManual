# #301 BLE SiliconLabs Serial Brick

<center>![](/img/300_serial/product/301.jpg)
<!--COLORME-->

## Overview
SiliconLabs BLE113を使用したBLE（Bluetooth Low Energy）のBrickです。
シリアルにて制御できるFirmwareが書き込まれているため、Arduino等からシリアル通信にてBLEを制御することができます。

BLEの転送レートは9600bpsに設定してあります。

## Connecting
Serialコネクタへ接続します。

Serialコネクタは、Arduino用、RaspberryPI用、Ichigojam用のOUT/INシールドでは１箇所のみとなります。

![](/img/300_serial/connect/301_ble_connect.jpg)
写真はArduinoの接続例です。

## Support
|Arduino|RaspberryPi|IchigoJam|
|:--:|:--:|:--:|
|◯|◯|×|

## BLE113 Datasheet
|Document|
|--|
|[BLE113 Datasheet](http://www.mouser.com/catalog/specsheets/Bluegiga_Technologies_BLE113_Datasheet.pdf)|


## Schematic
![](/img/300_serial/schematic/301_ble_siliconlabs.png)

## Library
### for Arduino

- [Arduino IDEからインストール](http://fabo.io/library_install.html)

  ライブラリ名：「FaBoBLE-BLE113」

- [GitHub Repository](https://github.com/FaBoPlatform/FaBoBLE-BLE113-Library)
- [Document](http://fabo.io/doxygen/FaBoBLE-BLE113-Library/)

### for RaspberryPi
- [pipからインストール](https://fabo.gitbooks.io/module/content/dev/pi/install_library.html)

  ライブラリ名：「FaBoBLE_BLE113」
 
- [PyPI](https://pypi.python.org/pypi/FaBoBLE_BLE113)

- [Library GitHub](https://github.com/FaBoPlatform/FaBoBLE-BLE113-Python)
- [Library Document](http://fabo.io/doxygen/FaBoBLE-BLE113-Python/)


## Sample Code
### for Arduino(Advertise)
SerialコネクタにBLE Brickを接続し、BLEを他の端末から接続できる状態（Advertise)にします。
```c
//
// FaBo Brick Sample
//
// brick_serial_ble
//
#include <SoftwareSerial.h>

SoftwareSerial bleShield(12, 13);

long previousMillis = 0;
long interval = 1000;

void setup()
{
  // BLEとの通信用
  bleShield.begin(9600);
  // ログ出力用
  Serial.begin(9600);
  Serial.write("start!");
}

void loop()
{
  // 一定時間ごとにコマンド実行
  unsigned long currentMillis = millis();
  if(currentMillis - previousMillis > interval) {
    Serial.write("*\n");

    previousMillis = currentMillis;

    // アドバタイズ開始 (026100が返って来れば成功、BLEを検索すると見つかります)

//    bleShield.write((byte)0x06); // パケットモードのみ
    bleShield.write((byte)0x00);
    bleShield.write((byte)0x02);
    bleShield.write((byte)0x06);
    bleShield.write((byte)0x01);
    bleShield.write((byte)0x02);
    bleShield.write((byte)0x02);

  }
  // 返答を出力
  while (bleShield.available()) {
    Serial.print(bleShield.read(), HEX);
  }
}
```
### for Arduino(Scan)
SerialコネクタにBLE Brickを接続し、他のBLE機器をスキャンしてシリアルモニタに出力します。
```c
//
// FaBo Brick Sample
//
// brick_serial_ble_scan
//
#include <SoftwareSerial.h>

SoftwareSerial bleShield(12, 13);

long previousMillis = 0;
long interval = 1000;

void setup()
{
  // BLEとの通信用
  bleShield.begin(9600);
  // ログ出力用
  Serial.begin(9600);
  Serial.write("start!");
}

void loop()
{
  // 一定時間ごとにコマンド実行
  unsigned long currentMillis = millis();
  if(currentMillis - previousMillis > interval) {
    Serial.write("*\n");

    previousMillis = currentMillis;

    // 検索開始 (02620が返って来れば成功、BLEを検索します。)
//    bleShield.write((byte)0x05); //パケットモードのみ
    bleShield.write((byte)0x00);
    bleShield.write((byte)0x01);
    bleShield.write((byte)0x06);
    bleShield.write((byte)0x02);
    bleShield.write((byte)0x02);
    delay(1000);
  }
  int count = 0;
  // 返答を出力
  while (bleShield.available()) {
    if (count==5){
      Serial.write('\n');
    }
    Serial.print(bleShield.read(), HEX);
    count++;

  }
  delay(100);

  // 検索終了 (02640が返って来れば成功、検索を終了します)
//  bleShield.write((byte)0x04); // パケットモードのみ
  bleShield.write((byte)0x00);
  bleShield.write((byte)0x00);
  bleShield.write((byte)0x06);
  bleShield.write((byte)0x04);
  delay(100);
  // 返答を出力
  Serial.write('\n');
  while (bleShield.available()) {
    Serial.print(bleShield.read(), HEX);
  }
  Serial.write('\n');
}
```

## Parts
- SiliconLabs BLE113 BluetoothLE ModuleIC

## GitHub
- https://github.com/FaBoPlatform/FaBo/tree/master/301_ble_siliconlabs
