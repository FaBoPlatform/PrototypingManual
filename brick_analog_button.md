<center>![](/img/analog/product/103_button_product.png)
<!--COLORME-->


# #103 Button Brick
タクトスイッチを使ったBrickです。
<br>
I/OピンよりボタンのON/OFFの状態を取得することができます。
<br>


## Connecting
![](/img/analog/connect/103_button_connect.png)
A0コネクタに接続したButton Brickの入力により、A1コネクタに接続したLED Brick の点灯/消灯を制御しています。


## Support
| Arduino | RaspberryPi | IchigoJam |
| -- | -- | -- |
| <center>○ | <center>○ | <center>○ |

## Schematic
![](/img/analog/schematic/103_button_schematic.png)


## Sample Code
### Arduino
```c
//
// FaBo Brick Sample
//
// brick_analog_button
//

#define buttonPin A0 // ボタンピン
#define ledPin A1    // LEDピン

// ボタンの押下状況取得用
int buttonState = 0;

void setup() {
  // ボタンピンを入力用に設定
  pinMode(buttonPin, INPUT); 
  // LEDピンを出力用に設定
  pinMode(ledPin, OUTPUT);         
}

void loop(){
  // ボタンの押下状況を取得
  buttonState = digitalRead(buttonPin);

  // ボタン押下判定
  if (buttonState == HIGH) {        
    // LED点灯
    digitalWrite(ledPin, HIGH);  
  } 
  else {
    // LED消灯
    digitalWrite(ledPin, LOW); 
  }
}
```
### RaspberryPi
```python
# coding: utf-8
#
# FaBo Brick Sample
#
# brick_analog_button
#

import RPi.GPIO as GPIO

LEDPIN = 4
BUTTONPIN = 7

GPIO.setwarnings(False)
GPIO.setmode( GPIO.BCM )
GPIO.setup( LEDPIN, GPIO.OUT )
GPIO.setup( BUTTONPIN, GPIO.IN )

while True:
    # ボタン押下判定
	if( GPIO.input( BUTTONPIN ) ):
	    # LED点灯
        GPIO.output( LEDPIN, True )
	else:
	    # LED消灯
		GPIO.output( LEDPIN, False )
```
### IchigoJam

IN(n)

n=ポート番号（1~4）

例）<br>
IN(1)<br>
結果）<br>
押されているなら１、押されていないなら０、とIN1に接続しているボタンの状態を返す。
####注意
押したら、ではなく、押されているなら、です。
```basic
100 ' IN(n) sample program
110 B=IN(1)
120 IF B=1 LED 1 ELSE LED 0
130 GOTO 110
```
ボタンが押されるとIchigoJam本体のLEDが点灯します。

110 IN1に接続しているボタンの状態を変数Bに代入する。<br>
120 変数Bの値が１なら本体LEDを点灯させ、それ以外なら本体LEDを消灯させる。<br>
130 110行に移動する。（ループ）

### Cylon.js

```js
var Cylon = require('cylon');

Cylon.robot({
        connections: {
                arduino: { adaptor: 'firmata', port: '/dev/tty.usbmodem1411' }
        },

        devices: {
                led: { driver: 'led', pin: 13},
                button: { driver: 'button', pin: 14}
        },

        work: function(my) {
                my.button.on('push', function(){
                        my.led.toggle()
                });
        }
}).start();
```
## NRF51

main.c
```c
#include <stdbool.h>
#include <string.h>
#include "app_button.h"
#include "app_gpiote.h"
#include "app_timer.h"

#define FABO_BUTTON 16
#define FABO_LED 18
#define APP_TIMER_PRESCALER             0  // Value of the RTC1 PRESCALER register.
#define APP_TIMER_MAX_TIMERS            1  // Maximum number of simultaneously created timers. 
#define APP_TIMER_OP_QUEUE_SIZE         2  // Size of timer operation queues. 
#define BUTTON_DEBOUNCE_DELAY			50 // Delay from a GPIOTE event until a button is reported as pushed. 
#define APP_GPIOTE_MAX_USERS            1  // Maximum number of users of the GPIOTE handler. 

/*
 * Handler to be called when button is pushed.
 * param[in]   pin_no 			The pin number where the event is genereated
 * param[in]   button_action 	Is the button pushed or released
 */
static void button_handler(uint8_t pin_no, uint8_t button_action)
{
    if(button_action == APP_BUTTON_PUSH)
    {
        switch(pin_no)
        {
            case FABO_BUTTON:
                nrf_gpio_pin_toggle(FABO_LED);
                break;
      
        }
    }
}

/**
 * Initialize the clock.
 */
void init_clock()
{
		// 内部クロック
	  NRF_CLOCK->LFCLKSRC             = (CLOCK_LFCLKSRC_SRC_RC << CLOCK_LFCLKSRC_SRC_Pos);
		// 外部クロック
		//NRF_CLOCK->LFCLKSRC            = (CLOCK_LFCLKSRC_SRC_Xtal << CLOCK_LFCLKSRC_SRC_Pos);
    NRF_CLOCK->EVENTS_LFCLKSTARTED = 0;
    NRF_CLOCK->TASKS_LFCLKSTART    = 1;
    while (NRF_CLOCK->EVENTS_LFCLKSTARTED == 0); // Wait for clock to start
}	

/**
 * Initialize all four LEDs on the nRF51 DK.
 */
void init_leds()
{
   	nrf_gpio_cfg_output(FABO_LED);
    nrf_gpio_pin_set(FABO_LED);
}	

/**
 * Function for application main entry.
 */
int main(void)
{	
    init_leds();
	
    init_clock();

    uint32_t err_code;

    // Button configuration structure.
    static app_button_cfg_t p_button[] = {  {FABO_BUTTON, APP_BUTTON_ACTIVE_LOW, NRF_GPIO_PIN_PULLUP, button_handler},
                                            };
        
    // Macro for initializing the application timer module.
    // It will handle dimensioning and allocation of the memory buffer required by the timer, making sure that the buffer is correctly aligned. It will also connect the timer module to the scheduler (if specified).
    APP_TIMER_INIT(APP_TIMER_PRESCALER, APP_TIMER_MAX_TIMERS, APP_TIMER_OP_QUEUE_SIZE, NULL);

    // Macro for initializing the GPIOTE module.
    // It will handle dimensioning and allocation of the memory buffer required by the module, making sure that the buffer is correctly aligned.
    APP_GPIOTE_INIT(APP_GPIOTE_MAX_USERS);

    // Initializing the buttons.
    err_code = app_button_init(p_button, sizeof(p_button) / sizeof(p_button[0]), BUTTON_DEBOUNCE_DELAY);
    APP_ERROR_CHECK(err_code);
                                            
    // b										
    err_code = app_button_enable();
    APP_ERROR_CHECK(err_code);

																						
    while(true) {
    }
		
}
```

Button Handler
* [app_button_init](http://infocenter.nordicsemi.com/index.jsp?topic=%2Fcom.nordic.infocenter.sdk51.v10.0.0%2Fgroup__app__button.html&resultof=%22app_button_init%22%20)
* [app_button_enable](http://infocenter.nordicsemi.com/index.jsp?topic=%2Fcom.nordic.infocenter.sdk51.v10.0.0%2Fgroup__app__button.html&resultof=%22app_button_init%22%20)
* [APP_TIMER_INIT](http://infocenter.nordicsemi.com/index.jsp?topic=%2Fcom.nordic.infocenter.sdk51.v10.0.0%2Flib_timer.html&resultof=%22APP_TIMER_INIT%22%20)
* [APP_GPIOTE_INIT](http://infocenter.nordicsemi.com/index.jsp?topic=%2Fcom.nordic.infocenter.sdk51.v10.0.0%2Fgroup__app__gpiote.html&resultof=%22APP_GPIOTE_INIT%22%20)
* [APP_ERROR_CHECK](http://infocenter.nordicsemi.com/index.jsp?topic=%2Fcom.nordic.infocenter.sdk51.v9.0.0%2Fgroup__app__error.html&resultof=%22Common%22%20%22common%22%20%22application%22%20%22applic%22%20%22error%22%20%22handler%22%20)

Sample
* [nrf51-app-button-sample](https://github.com/NordicSemiconductor/nrf51-app-button-example/blob/master/main.c)

## NRF52

main.c
```c
#include <stdbool.h>
#include "nrf.h"
#include "nrf_drv_gpiote.h"
#include "app_error.h"

#define PIN_OUT 3
#define PIN_IN 4

void in_pin_handler(nrf_drv_gpiote_pin_t pin, nrf_gpiote_polarity_t action)
{
		if(action ==  GPIOTE_CONFIG_POLARITY_Toggle){
				nrf_drv_gpiote_out_toggle(PIN_OUT);
		}
}
/**
 * @brief Function for configuring: PIN_IN pin for input, PIN_OUT pin for output, 
 * and configures GPIOTE to give an interrupt on pin change.
 */
static void gpio_init(void)
{
    ret_code_t err_code;

    err_code = nrf_drv_gpiote_init();
    APP_ERROR_CHECK(err_code);
    
    nrf_drv_gpiote_out_config_t out_config = GPIOTE_CONFIG_OUT_SIMPLE(false);

    err_code = nrf_drv_gpiote_out_init(PIN_OUT, &out_config);
    APP_ERROR_CHECK(err_code);

    nrf_drv_gpiote_in_config_t in_config = GPIOTE_CONFIG_IN_SENSE_TOGGLE(true);
    in_config.pull = NRF_GPIO_PIN_PULLUP;

    err_code = nrf_drv_gpiote_in_init(PIN_IN, &in_config, in_pin_handler);
    APP_ERROR_CHECK(err_code);

    nrf_drv_gpiote_in_event_enable(PIN_IN, true);
}

/**
 * @brief Function for application main entry.
 */
int main(void)
{
    gpio_init();

    while (true)
    {
        // Do nothing.
    }
}
```

* [GPIOTE_CONFIG_IN_SENSE_TOGGLE](http://infocenter.nordicsemi.com/index.jsp?topic=%2Fcom.nordic.infocenter.sdk52.v0.9.2%2Fgroup__nrf__drv__gpiote.html&resultof=%22GPIOTE%22%20%22gpiot%22%20)
* [GPIOTE_CONFIG_OUT_SIMPLE](http://infocenter.nordicsemi.com/index.jsp?topic=%2Fcom.nordic.infocenter.sdk52.v0.9.2%2Fgroup__nrf__drv__gpiote.html&resultof=%22GPIOTE%22%20%22gpiot%22%20)
* [nrf_drv_gpiote_in_init](http://infocenter.nordicsemi.com/index.jsp?topic=%2Fcom.nordic.infocenter.sdk52.v0.9.2%2Fgroup__nrf__drv__gpiote.html&resultof=%22GPIOTE%22%20%22gpiot%22%20)
* [nrf_drv_gpiote_out_init](http://infocenter.nordicsemi.com/index.jsp?topic=%2Fcom.nordic.infocenter.sdk52.v0.9.2%2Fgroup__nrf__drv__gpiote.html&resultof=%22GPIOTE%22%20%22gpiot%22%20)


## Parts
- 12mm角タクトスイッチ
