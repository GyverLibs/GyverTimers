This is an automatic translation and may be incorrect in some places. See the source README and examples for authoritative information.

[![latest](https://img.shields.io/github/v/release/GyverLibs/GyverTimers.svg?color=brightgreen)](https://github.com/GyverLibs/GyverTimers/releases/latest/download/GyverTimers.zip)
[![PIO](https://badges.registry.platformio.org/packages/gyverlibs/library/GyverTimers.svg)](https://registry.platformio.org/libraries/gyverlibs/GyverTimers)
[![Foo](https://img.shields.io/badge/Website-AlexGyver.ru-blue.svg?style=flat-square)](https://alexgyver.ru/)
[![Foo](https://img.shields.io/badge/%E2%82%BD%24%E2%82%AC%20%D0%9F%D0%BE%D0%B4%D0%B4%D0%B5%D1%80%D0%B6%D0%B0%D1%82%D1%8C-%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B0-orange.svg?style=flat-square)](https://alexgyver.ru/support_alex/)
[![Foo](https://img.shields.io/badge/README-ENGLISH-blueviolet.svg?style=flat-square)](https://github-com.translate.goog/GyverLibs/GyverTimers?_x_tr_sl=ru&_x_tr_tl=en)  

[![Foo](https://img.shields.io/badge/ПОДПИСАТЬСЯ-НА%20ОБНОВЛЕНИЯ-brightgreen.svg?style=social&logo=telegram&color=blue)](https://t.me/GyverLibs)

# GyverTimers
Configure and control interrupts on hardware timers ATmega328p, ATmega2560
- All three ATmega328 timers and six ATmega2560 timers are supported.
- Setting the period (mx) and frequency (Hz) of interruptions:
    - 8 bit timers: 61 Hz - 1 MHz (16,384 μs.. 1 μs).
    - 16 bit timers: 0.24 Hz - 1 MHz (4,200,000 μs.. 1 μs).
- Automatic adjustment of period setting from tactile frequency (F CPU)
- The function returns the exact fixed period/frequency for debugging (frequency is limited by the timer resolution).
- Multi-channel support: One timer calls 2 (ATmega328) or
3 (ATmega2560, timers 1, 3, 4, 5) interrupts with a customizable 0-360 degree phase shift;
- Customizable action of hardware timer output on interruption: high signal, low signal, switching.
Allows you to generate a meander (one- and two-stroke);
- Timer control: start/stop/pause/continue/initiation

### Compatibility
ATmega328p, ATmega2560

### Documentation.
There's a library[extended documentation](https://alexgyver.ru/GyverTimers/)

## Contents
- [Installation](#install)
- [Initialization](#init)
- [Use of use](#usage)
- [Example](#example)
- [Versions](#versions)
- [Bugs and feedback](#feedback)

<a id="install"></a>
## Installation
- The library can be found under the name **GyverTimers** and installed through the library manager in:
    - Arduino IDE
    - Arduino IDE v2
    - PlatformIO
- [Download the library](https://github.com/GyverLibs/GyverTimers/archive/refs/heads/main.zip).zip archive for manual installation:
    - Unpack and put in *C:\Program Files (x86)\Arduino\libraries* (Windows x64)
    - Unpack and put in *C:\Program Files\Arduino\libraries* (Windows x32)
    - Unpack and put in *Documents/Arduino/libraries/ *
    - (Arduino IDE) Automatic installation from .zip: *Sketch/Connect library/Add .ZIP library...* and specify downloaded archive
- Read more detailed instructions for installing libraries[here](https://alexgyver.ru/arduino-first/#%D0%A3%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0_%D0%B1%D0%B8%D0%B1%D0%BB%D0%B8%D0%BE%D1%82%D0%B5%D0%BA)
### Update
- I recommend always updating the library: new versions fix errors and bugs, as well as optimize and add new features.
- Through the library manager IDE: find the library as when installing and click "Update"
- Manually: **Delete the folder with the old version** and then put the new one in its place. “Replacement” can not be done: sometimes new versions delete files that will remain when replaced and can lead to errors!

<a id="init"></a>
## Initialization
No.

<a id="usage"></a>
## Use of use
```cpp
Методы для Timer0, Timer1, Timer2...
uint32_t setPeriod(период);              // set the period in microseconds and start the timer. Returns the real period (accuracy is limited by timer resolution).
uint32_t setFrequency(частота);          // set the frequency in Hertz and start the timer. Returns the actual frequency (accuracy is limited by timer resolution).
float setFrequencyFloat(частота float);  // setting the frequency in Hertz and running the timer, decimal fractions are allowed. Returns the actual frequency (accuracy is limited by timer resolution).
void enableISR(источник);                // Enable interrupts, CHANNEL A or CHANNEL B (+CHANNEL C in Mega2560)
void disableISR(источник);               // Turn off interrupts, channel CHANNEL A or channel CHANNEL B. The timer count does not stop (without specifying the parameters, channel A will be turned off).
void pause();                            // suspend the timer account without resetting the counter
void resume();                           // follow up
void stop();                             // stop counting and reset the meter
void restart();                          // Reset the timer (reset the counter)
void setDefault();                       // Set the default timer settings (“Arduino Silences”)
void outputEnable(канал, режим);         // channel: turn on the CHANNEL A or CHANNEL B timer output (+CHANNEL C for Mega2560). Mode: TOGGLE PIN, CLEAR PIN, SET PIN (switch/switch off/switch on interrupt pin)
void outputDisable(канал);               // turn off the CHANNEL A or CHANNEL B timer output (+ CHANNEL C for Mega2560, see the timer tab)
void outputState(канал, состояние);      // Change the channel state: HIGH/LOW
void phaseShift(источник, фаза);         // shift the channel phase by 0-360 degrees (only channel B moves in 8 bit timers)
bool ready(uint8_t channel);             // returns true if there is an interruption on the channel.
bool ready();                            // returns true if there is an interruption on channel A

/*
  Библиотека даёт прямой доступ к прерыванию без “Ардуиновских” attachInterrupt, что позволяет 
  сократить время вызова функции-обработчика прерывания. Прерывание с настроенной частотой 
  будет обрабатываться в блоке вида ISR(канал) {}, пример:
*/
ISR(TIMER1_A) {
  // code
}

ISR(TIMER1_B) {
  // code
}

ISR(TIMER2_B) {
  // code
}

ISR(TIMER0_A) {
  // code
}

/*
-------------------------------- Arduino NANO 16 МГц (ATmega328) ------------------------------------
Таймер	| Разрядность	| Частоты			| Периоды			| Выходы	| Пин Arduino	| Пин МК|
--------|---------------|-------------------|-------------------|-----------|---------------|-------|
Timer0	| 8 бит			| 61 Гц - 1 МГц		| 16 384.. 1 мкс	| CHANNEL_A	| D6			| PD6	|
        | 				| 					| 					| CHANNEL_B	| D5			| PD5	|
--------|---------------|-------------------|-------------------|-----------|---------------|-------|
Timer1	| 16 бит		| 0.24 Гц - 1 МГц	| 4 200 000.. 1 мкс	| CHANNEL_A	| D9			| PB1	|
        | 				| 					| 					| CHANNEL_B	| D10			| PB2	|
--------|---------------|-------------------|-------------------|-----------|---------------|-------|
Timer2	| 8 бит			| 61 Гц - 1 МГц		| 16 384.. 1 мкс	| CHANNEL_A	| D11			| PB3	|
        | 				| 					| 					| CHANNEL_B	| D3			| PD3	|
----------------------------------------------------------------------------------------------------
                        
------------------------------ Arduino MEGA 16 МГц (ATmega2560) -------------------------------------
Таймер	| Разрядность	| Частоты			| Периоды			| Выходы	| Пин Arduino	| Пин МК|
--------|---------------|-------------------|-------------------|-----------|---------------|-------|
Timer0	| 8 бит			| 61 Гц - 1 МГц		| 16 384.. 1 мкс	| CHANNEL_A	| 13			| PB7	|
        | 				| 					| 					| CHANNEL_B	| 4				| PG5	|
--------|---------------|-------------------|-------------------|-----------|---------------|-------|
Timer1	| 16 бит		| 0.24 Гц - 1 МГц	| 4 200 000.. 1 мкс	| CHANNEL_A	| 11			| PB5	|
        | 				| 					| 					| CHANNEL_B	| 12			| PB6	|
        | 				| 					| 					| CHANNEL_C	| 13			| PB7	|
--------|---------------|-------------------|-------------------|-----------|---------------|-------|
Timer2	| 8 бит			| 61 Гц - 1 МГц		| 16 384.. 1 мкс	| CHANNEL_A	| 10			| PB4	|
        | 				| 					| 					| CHANNEL_B	| 9				| PH6	|
--------|---------------|-------------------|-------------------|-----------|---------------|-------|
Timer3	| 16 бит		| 0.24 Гц - 1 МГц	| 4 200 000.. 1 мкс	| CHANNEL_A	| 5				| PE3	|
        | 				| 					| 					| CHANNEL_B	| 2				| PE4	|
        | 				| 					| 					| CHANNEL_C	| 3				| PE5	|
--------|---------------|-------------------|-------------------|-----------|---------------|-------|
Timer4	| 16 бит		| 0.24 Гц - 1 МГц	| 4 200 000.. 1 мкс	| CHANNEL_A	| 6				| PH3	|
        | 				| 					| 					| CHANNEL_B	| 7				| PH4	|
        | 				| 					| 					| CHANNEL_C	| 8				| PH5	|
--------|---------------|-------------------|-------------------|-----------|---------------|-------|
Timer5	| 16 бит		| 0.24 Гц - 1 МГц	| 4 200 000.. 1 мкс	| CHANNEL_A	| 46			| PL3	|
        | 				| 					| 					| CHANNEL_B	| 45			| PL4	|
        | 				| 					| 					| CHANNEL_C	| 44			| PL5	|
----------------------------------------------------------------------------------------------------
*/
```

<a id="example"></a>
## Example
For more examples see **examples**!
```cpp
// Demonstrate all functions of the library

#include <GyverTimers.h>

void setup() {
  // Reset the timer and give it a period or frequency
  // All functions return a real period/frequency, which may differ from those entered.
  Timer2.setPeriod(1000);           // Set a specific period of 1000 μs (~ 1000 Hz), returns the real period to μs
  Timer0.setFrequency(250);         // Set the timer interrupt frequency in Hz, return the real frequency in hertz
  Timer1.setFrequencyFloat(50.20);  // Determine the frequency more precisely, in fractional numbers, relevant for low frequencies and timer 1
  // From this point on, the timer has already been reconfigured and races with an ejected frequency/period.

  // Connect the timer interrupt, from this moment interrupts will be called
  Timer0.enableISR();               // Connect the standard interrupt, channel A, without stig phases
  Timer2.enableISR(CHANNEL_B); // Connect timer interrupt 2, channel B
  Timer1.enableISR(CHANNEL_A);  // Connect the interruption of channel A
  Timer1.enableISR(CHANNEL_B); // Connect the second interrupt timer 1
  // The interruption will start calling.

  // If you suddenly need to turn off the interruption without stopping the timer
  Timer1.disableISR(CHANNEL_B);
  // From now on, interruption B will no longer be called.

  // If you need to stop the timer completely, hardware
  Timer2.pause();
  // From this moment the timer is in place, the contents of the counter remain intact. n

  // The timer can now be put back into operation.
  Timer2.resume();
  // Timer continued to count from the same place.

  // If you need to completely stop the timer and reset the contents of the counter
  Timer1.stop();
  // Timer's standing, meter dropped.

  // Get the timer back on track.
  Timer1.restart();
  // Timer restarted, started counting from the beginning

  // If you need to return the standard Arduino - timer settings
  Timer0.setDefault();
  // Now the timer is running in the standard mode.
}

// interrupter
ISR(TIMER1_A) {
}

ISR(TIMER1_B) {
}

ISR(TIMER2_B) {
}

ISR(TIMER0_A) {
}

void loop() {

}
```

<a id="versions"></a>
## Versions
- v1.1 - an error in the calculation of periods is corrected
- v1.2 - the code is divided into h and cpp
- v1.3 - minor bug corrected
- v1.4 - Frequency and period table corrected
- v1.5 - Restart and resume corrected
- v1.6 - phase shift by separate method
- v1.7 - documentation corrected
- v1.8 - fix the max period bug
- v1.9 - Corrected 2-period error
- v1.10 - added flag ready

<a id="feedback"></a>
## Bugs and feedback
If you find bugs, create **Issue**, or better write to the mail immediately.[alex@alexgyver.ru](mailto:alex@alexgyver.ru)  
The library is open for revision and your **Pull Requests*!

When reporting bugs or incorrect work of the library, it is necessary to specify:
- Library version
- What is used by the IC
- SDK version (for ESP)
- Arduino IDE version
- Are embedded examples that use features and designs that cause bugs in your code working correctly?
- What code was downloaded, what work was expected from it and how it works in reality
- Ideally, attach the minimum code in which the bug is observed. Not a canvas of a thousand lines, but a minimum code.
