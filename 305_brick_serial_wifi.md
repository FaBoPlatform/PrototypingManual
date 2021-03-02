# #305 WiFi Serial Brick

<center>![](/img/300_serial/product/305.jpg)
<!--COLORME-->

## Overview
WiFi通信ができるBrickです。TCPクライアントやアクセスポイント、HTTPサーバになることができます。他にもWiFiBrick間で１対１で接続ができます。また、マイコン搭載によりWiFiBrickだけでUNOより高性能なArduinoとして単体で活用いただけます。使用できるWifi規格はIEEE 802.11 b/g/nとなっております。

技適マークがありますので安心して日本国内で使用できます。

## Connecting
Serialコネクタに接続します。
![](/img/300_serial/connect/305_wifi_connect.jpg)

## Support
|Arduino|
|:--:|
|◯|

## ESP-WROOM-02 Datasheet
|Document|
|--|
|[ESP-WROOM-02 Datasheet Page](https://espressif.com/en/products/hardware/esp-wroom-02/resources)|

## Schematic
![](/img/300_serial/schematic/305_wifi.png)

##ATコマンドとは？
ATコマンドは、電話回線（モデム）に対して制御するのに開発され、現在でも使われております。WiFiBrickには、あらかじめファームウェアがインストール済みで、ATコマンドの文字列の信号をこのBrickに送信することにより命令ができるようになっています。

## ATコマンド（構文）

|  |コマンド名  |
| -- | -- |
| コマンドを実行する  |AT+コマンド  |
| コマンドの内容表示 | AT+コマンド? |
| コマンド実行引数 | AT+コマンド=引数 |

## ATコマンド（基本コマンド）

|  |コマンド名  | レスポンス |
| -- | -- | -- |
| テスト | AT | OK |
| リセット | AT+RST | Ready |
| ファームウェアのバーション表示 | AT+GMR | バーション表示 |
| コマンドエコー設定 | ATE1 | OK |
| UART通信設定 | AT+UART_DEF=通信速度（９６００bps）,データビット（８ビット）,ストップビット（１）,パリティビット(０),フロー制御（０） | -- |
| TX Power設定 | AT+RFPOWER | -- |
| スリープモード設定 | AT+SLEEP | -- |
| 工場設定 | AT+RESTORE | -- |

## ATコマンド(WiFiコマンド）

|  |コマンド名  | 正常レスポンス |
| -- | -- | -- |
| Wifiの設定 | AT+CWMODE | 1:ステーション(STA) 子機2:アクセスポイント親機3:ステーション、アクセスポイント  例 AT+CWMODE=1|
| アクセスポイント一覧 | AT+CWLAP | SSID名,電波強度,Macアドレス,0:キーなし 2:WPA 3:WPA2  4:WPA_WPA2  |
| アクセスポイントに接続| AT+CWJAP="SSID名","パスワード" | WIFI CONNECTED |
| アクセスポイントの切断| AT+CWQAP |WIFI DISCONNECTED|
| アクセスポイントの設定| AT+CWSAP |AT+CWSAP=SSID名,６４バイトまでのパスワード,チャンネル,暗号(0:オープン2:WPA_PSK 3:WPA2_PSK 4:WPA_WPA2_PSK)|
| アクセスポイントの確認| AT+CWSAP? |---|

## ATコマンド(IPコマンド）

|  |コマンド名  | 正常レスポンス |
| -- | -- | -- |
| IPアドレスの確認 | AT+CIFSR | +CIFSR:STAIP,"IPアドレス"　+CIFSR:STAMAC,"Macアドレス"|
| TCPまたは、UDP接続開始 | AT+CIPSTART="プロトコル名","URL",ポート数 | CONNECT OK|
| 転送設定 | AT+CIPMODE=数字 |AT+CIPMODE=0　非透過モード　AT+CIPMODE=1 透過モード（トランスペアレントモード）|
| データ送信（１）　| AT+CIPSEND=転送接続ID,バイト数(2048バイトまで)|---|
| データ送信（２）　| AT+CIPSEND=バイト数(2048バイトまで)|---|
| データ送信（３）　| AT+CIPSEND |OK　＞　が表示される　参考：データ送信後、+++入力で終了|
| ファームウェア更新 | AT+CIUPDATE |---|
| IP,IPDの表示 | AT+CIPDINFO |---|
| コネクション設定 | AT+CIPMUX=数字 |0:シングル接続 1:多重接続（最大４まで）|
| コネクション設定確認 | AT+CIPMUX? |----|
| サーバの設定 | AT+CIPSERVER=モード数字、ポート数字 |モード 0:サーバー削除、1:サーバー作成　ポート　デフォルト３３３|

## Arduino接続

WiFiBrickを4pinケーブルを使い、ソフトウェアシリアルとハードウェアシリアルのいずれかに接続します。

## WiFiモジュールの動作確認

WiFiBrickをハードウェアシリアルに接続し、ArduinoIDEを起動。シリアルモニタを開き設定は、改行コードはCR+LF,通信速度を115200bpsに設定します。AT+CWMODE=1（子機）を入力し、送信、次に、AT+CWJAPAT="SSID","パスワード"を送信し、LANに接続する。AT+CIFSRコマンドを送信。IPアドレス(例：192.168.10.8)を確認する。次に同じLANネットワークに属するパソコンのターミナルからPingコマンドを実行する。
ping 192.168.10.8
64 bytes from 192.168.10.8: icmp_seq=3 ttl=255 time=6.434 ms
などのレスポンスが返ってきたら成功です。
WiFiBrickのファームウェアの不具合などに備えて、SSID,Macアドレスを控えておきましょう。

```
AT+CWSAP?
AT+CIPSTAMAC?
AT+CIPAPMAC?
```

## Sample Code

### for Arduino サンプル1

サンプル１は、Arduino IDEでコマンド入力できる。9600bpsでの通信。サンプル１は、Arduino IDEでコマンド入力できる。9600bpsでの通信。WiFiモジュールはArduino（SoftSerial使用）においてデフォルトの通信速度は115200bpsなのでを9600bpsにする。サンプル２（9600bpsに設定）を実効後、サンプル１（コマンド入力可能）を実効してください。サンプル３でIoTプラットフォームであるIFTTTを使います。サンプル１を実行後、ArduinoIDEのシリアルモニタを開き、CL及びLF,9600bpsに設定する。シリアルモニタのテキストボックスにATコマンドを入力し送信ボタンかエンターキーでATコマンドが入力できます。ATと入力し、OKが返ってきたらシリアル通信は正常です。

```
//
// FaBo Brick Sample 1
// 2016/2/23
// WiFi Brick #305
#include <SoftwareSerial.h>

int bluetoothRx = 12;
int bluetoothTx = 13;

SoftwareSerial mySerial(bluetoothRx, bluetoothTx); // RX, TX

void setup()
{
  // Open serial communications and wait for port to open:
  Serial.begin(9600);
  while (!Serial) {
    ; // wait for serial port to connect. Needed for Leonardo only
  }

  Serial.println("Goodnight moon!");

  // set the data rate for the SoftwareSerial port
  mySerial.begin(9600);
}
int c = 0;
void loop() // run over and over
{
  if (mySerial.available()){
    char c = mySerial.read();
    Serial.write(c);

  }
  if (Serial.available())
    mySerial.write(Serial.read());
}
```

### for Arduino サンプル2

ArduinoUNOとWiFiBruckとの通信はソフトウェアシリアル通信を行っています。115200bps（デフォルト）では文字化けしやすいので、＃305 WiFi BrickとArduinoShield間の通信速度を9600bpsに変更し、Ardunoとパソコン間のハードウェアシリアル通信は9600pbsとします。

```
//
// FaBo Brick Sample 2
// 2016/2/23
// WiFi Brick #305
// 115200bps→9600bps
#include <SoftwareSerial.h>
#define TimeInterval 2000
#define ComminucationSpeed_Arduino 9600
#define ComminucationSpeed_bleShield 115200

int bluetoothRx = 12;
int bluetoothTx = 13;

SoftwareSerial bleShield(bluetoothRx, bluetoothTx);

void setup()
{
  Serial.begin(ComminucationSpeed_Arduino);
  while (!Serial) {
  }

  Serial.println("bps changed!");

  // set the data rate for the SoftwareSerial port
  bleShield.begin(ComminucationSpeed_bleShield);
}

void ResposeCatch(){
  while(bleShield.available()){
  Serial.write(bleShield.read());
  bleShield.write(Serial.read());
   }
   delay(TimeInterval);
}

void loop()
{
  ResposeCatch();
   delay(TimeInterval);

   bleShield.println("AT+UART_DEF=9600,8,1,0,0");
   ResposeCatch();
   Serial.println("Excute");
   while(1){
    }

}
```

### for Arduino サンプル3

IFTTTを使い、Fabo#115Humidity Brickセンサーで取得したデータをMakerで受け、Googleドライブのスプレッドシートにレコードを追加します。#115Humidity BrickをA0に接続します。
####IFTTTのアカウント(SingUP)を作る。
IFTTTのWebサイトにアクセス。右上の画面にあるSingUPをクリックしてアカウントを作成。Welcome to IFTTTというメールが送られてくるので、Get Startedをクリックして登録完了。
####IFTTTのレシピを作成。
IFTTTに再びアクセスし、Thisを選択。Search ChannnelボックスにMakerで検索。create trigger画面が出る。Receive a web requestをクリックします。Event Nameを入力　例：115_Humidityと入力します。thatを選択。Choose Action Channel画面が出る。GoogleDriveで検索する。GoogleDriveの許可を与える。(Googleアカウントがない方はアカウントを作ります。)Choose an Actionの画面が出たら、”Add row to spredsheet"を選択。(１ファイル2000行まで記録できる。2000行を超えると別ファイルが生成される)次に、Complete Action Fields画面が表示される。ファイル名、列の設定、パスの設定ができる。しかし、今回の例の場合は、何も変更しないでCreate Actionボタンを押す。すると、Create and connect画面が現れ、レシピのタイトルが確認できる。確認ができたら、Create Recipeボタンを押す。次に画面右上にあるChannelをクリックします。Makerで検索します。Maker Channnel画面が出たら、クリックしkeyをコピーしてサンプルコードに貼り付けます。サンプルコードに接続したいSSIDとWiFiのパスワードを入力、Event NameとKeyを入力しコンパイルします。GoogleDriveでIFTTTというフォルダができているので、クリックし任意のスプレッシートにデータが書き込まれているのか確認します。無事に書き込まれているなら成功です。

必要なライブラリは[#115Humidity Brick](http://www.fabo.io/115.html)を参考にしてください。

```
//
// FaBo Brick Sample 3(To transmit data to Google Drive at IFTTT.)
// 2016/7/21
// WiFi Brick #305
//Rev 0.0.0
#include <SoftwareSerial.h>
#include <ArduinoJson.h>
#include "DHT.h"

#define bluetoothRx   12
#define bluetoothTx   13

const String ssid     = "ssid";
const String password = "password";
const String Serverhost     = "maker.ifttt.com";
const int httpPort   = 80;
const String key = "key";
const String event = "event name";
const String uri     = "/trigger/" + event + "/with/key/" + key;
struct dht_Data {
  float h;
  float t;
  };

SoftwareSerial bleSheild(bluetoothRx, bluetoothTx);
DHT dht(A0, DHT11);
dht_Data dht_data;


void wifiConnect(){
  delay(100);
  bleSheild.begin(9600);
  delay(1000);
   //テストコマンド
  bleSheild.println("AT");
  delay(5000);
   if (!bleSheild.find("OK")) {
  Serial.println("ATisBad");
  return;
  }else{
    Serial.println("ATisOK");
    }
  //アクセスポイントに接続
  String cwjap = "AT+CWJAP=\"" + ssid + "\",\"" + password + "\"";
  Serial.println("AccessPointConnection.");
  bleSheild.println(cwjap);
  Serial.println(cwjap);
  delay(10000);
  if (!bleSheild.find("OK")) {
    }else{
      Serial.println("AT+CWJAPisOK");
      }
  //TCPプロコトルで接続
  String cipst = "AT+CIPSTART=\"TCP\",\"" + Serverhost + "\"," + httpPort;
  bleSheild.println(cipst);
  Serial.println(cipst);
  delay(50);
  if (!bleSheild.find("OK")) {
      Serial.println("AT+CIPSTARTisBad");
    }else{
      Serial.println("AT+CIPSTARTisOK");
      Serial.println("Wifi Connected");
      }
  }
//湿度、温度測る
void tmpMesurement(){
  dht_data.h = dht.readHumidity();
  dht_data.t = dht.readTemperature();
}

void setup()  {
  //ESP8266とシリアル通信
  bleSheild.begin(9600);
  //Arduinoのシリアル通信
  Serial.begin(9600);
  dht.begin();
  wifiConnect();

}
void loop() {
  String value;
  //２００文字分でバッファを用意。
  StaticJsonBuffer<200> jsBuffer;
  JsonObject& data = jsBuffer.createObject();
  if (!data.success()){
    Serial.println("createObject failed!");
    }
  tmpMesurement();
  data["value1"] =  dht_data.h;
  data["value2"] =  dht_data.t;
  data.printTo(value);
  value += "\r\n";
  //HTTP POST
  String jsonPacket = "POST " + uri + " HTTP/1.1\r\nHost: " + Serverhost + "\r\nContent-Length: " + value.length() + " \r\nContent-Type: application/json\r\n\r\n" + value +"\r\n";
  Serial.println(jsonPacket);
  //トランスペアレントモードで送信
  bleSheild.print("AT+CIPSEND=");
  bleSheild.println(jsonPacket.length());
  delay(10);
  if (!bleSheild.find(">")){
    Serial.println("AT+CIPSENDisBad");
  }
  //HTTPリクエスト送信
  Serial.println("Request.");
  bleSheild.print(jsonPacket);
  delay(10);
  if (!bleSheild.find("SEND OK\r\n")) {
    Serial.println("SEND FAILED");
    //失敗した時は再接続する。
    wifiConnect();
    Serial.println("Attmpt reconnection.");
  }else{
    Serial.println("send completely.");
    Serial.print("");
    }
  delay(5000);
}
```

## WiFiBrickにスケッチを書き込む

これまではArduinoからWiFiBrickをATコマンドを使ってWiFiの通信をしていましたが、WiFiBrickをArduinoのようにすることもできます。WiFiBrickにスケッチを書き込むので、以後ATコマンドは使えなくなるのでご注意ください。WiFiBrickと#304USB Brickを接続します。#304USB Brickのスイッチは電圧3.3Vにしてください。Arduino IDE(Arduino1.6.11の場合。)を起動します。Arduno->Preference->追加のボードマネージャのボックスにhttp://arduino.esp8266.com/stable/package_esp8266com_index.jsonを代入しOKします。ツール->ボード->ボードマネージャからesp8622 by ESP8266 Communityを選択してインストールします。**参照先以外のファームウェアは、電波法に抵触する可能性があります。絶対に参照または、使用しないでください。**次にツール->ボード->Generic ESP8266 Moduleを選択します。ツール->ボード->Flash Size:"4M(3M SPIFFS)"を選択します。繋がっている任意のポートを選択して、/dev/usbserial*******(Macの場合)、COM**(Windowsの場合)を選択し、WiFiBrickのRESETボタンとIO0ボタンを同時に押して、RESETボタンを離します。ArduinoIDEを使ってマイコンボードに書き込みをします。ArduinoIDEの１００％の表示が出たら完了です。IO0ボタンを離します。

以下のサンプルは、WiFiBrickがWebサーバーになるサンプルプログラムです。１０秒ごとにサーバーから
メッセージが表示されるようにしました。

```
//Fabo Sample WebServerTest
#include <ESP8266WiFi.h>
#include <WiFiClient.h>
#include <ESP8266WebServer.h>
#include <ESP8266mDNS.h>
//所属するネットワークSSIDとパスワードを上書きする。
const char *ssid = "ssid";
const char *password = "password";

char count = 0;

ESP8266WebServer server ( 80 );

//メッセージを用意　１０秒ごとに変わる。
char *g[] = {"Good morning!","Good afternoon!","Good evening!"};

void handleRoot() {
  char temp[400];
  
  snprintf ( temp, 400,

"<html>\
  <head>\
    <meta http-equiv='refresh' content='10'/>\
    <title>WifiBrick_Test</title>\
    <style>\
      body { background-color: #ff0000; font-family: Arial, Helvetica, Sans-Serif; Color: #ffffff; }\
    </style>\
  </head>\
  <body>\
    <h1>FROM #305WiFiBrick WebServer!</h1>\
    <h1>Sucess!</h1>\
    <h1>%s</h1>\
  </body>\
</html>",g[count % 3]
  );
  server.send ( 200, "text/html", temp );
  count++;
}

void setup ( void ) {
//指定されたSSIDのネットワークに接続する。
  Serial.begin ( 9600 );
  WiFi.begin ( ssid, password );
  Serial.println ( "" );
//接続を待機する。
  while ( WiFi.status() != WL_CONNECTED ) {
    delay ( 500 );
    Serial.print ( "." );
  }
//接続先の情報を表示する。
  Serial.println ( "" );
  Serial.print ( "SSID=" );
  Serial.println ( ssid );
  Serial.print ( "IP=" );
  Serial.println ( WiFi.localIP() );

  if ( MDNS.begin ( "esp8266" ) ) {
    Serial.println ( "MDNS responder started" );
  }
//サーバー始動
  server.on ( "/", handleRoot );
  server.begin();
  Serial.println ( "HTTP server started" );
}

void loop ( void ) {
  server.handleClient();
}
```
## Parts
- ESP-WROOM-02
