# GyverTimers
ATmega328p, ATmega2560 hardware timer interrupt setup and control
- All three timers on ATmega328 and six timers on ATmega2560 are supported;
- Setting the period (µs) and frequency (Hz) of interruptions:
    - 8 bit timers: 61 Hz - 1 MHz (16 384 µs.. 1 µs);
    - 16 bit timers: 0.24 Hz - 1 MHz (4,200,000 µs.. 1 µs);
- Automatic adjustment of the period setting from the clock frequency (F_CPU);
- The function returns the exact steady period/frequency for debugging (frequency is limited by the timer resolution);
- Support for multi-channel operation: one timer calls 2 (ATmega328) or
3 (ATmega2560, timers 1, 3, 4, 5) interrupts with adjustable phase shift 0-360 degrees;
- Configurable timer hardware output action on interrupt: high beep, low beep, toggle.
Allows you to generate a meander (one- and two-stroke);
- Timer operation control: start/stop/pause/continue/initialization;

### Compatibility
ATmega328p, ATmega2560

### Documentation
The library has [extended documentation](https://alexgyver.ru/GyverTimers/)

## Content
- [Install](#install)
- [Initialization](#init)
- [Usage](#usage)
- [Example](#example)
- [Versions](#versions)
- [Bugs and feedback](#feedback)

<a id="install"></a>
## Installation
- The library can be found by the name **GyverTimers** and installed through the library manager in:
    - Arduino IDE- Arduino IDE v2
    - PlatformIO
- [Download library](https://github.com/GyverLibs/GyverTimers/archive/refs/heads/main.zip) .zip archive for manual installation:
    - Unzip and put in *C:\Program Files (x86)\Arduino\libraries* (Windows x64)
    - Unzip and put in *C:\Program Files\Arduino\libraries* (Windows x32)
    - Unpack and put in *Documents/Arduino/libraries/*
    - (Arduino IDE) automatic installation from .zip: *Sketch/Include library/Add .ZIP library…* and specify the downloaded archive
- Read more detailed instructions for installing libraries [here] (https://alexgyver.ru/arduino-first/#%D0%A3%D1%81%D1%82%D0%B0%D0%BD%D0%BE% D0%B2%D0%BA%D0%B0_%D0%B1%D0%B8%D0%B1%D0%BB%D0%B8%D0%BE%D1%82%D0%B5%D0%BA)

<a id="init"></a>
## Initialization
Not

<a id="usage"></a>
## Usage
```cpp
Methods for Timer0, Timer1, Timer2...
uint32_t setPeriod(period); // set the period in microseconds and start the timer. Returns the real period (accuracy is limited by the resolution of the timer).
uint32_t setFrequency(frequency); // set the frequency in Hertz and start the timer. Returns the actual frequency (accuracy is limited by the resolution of the timer).
float setFrequencyFloat(float frequency); // set the frequency in Hertz and start the timer, decimals are allowed. Returns the actual frequency (accuracy is limited by the resolution of the timer).
void enableISR(source); // enable interrupts, interrupt channel CHANNEL_A or CHANNEL_B (+ CHANNEL_C for Mega2560)
void disableISR(source); // disable interrupts, channel CHANNEL_A or CHANNEL_B. The counting of the timer does not stop (channel A will be turned off without specifying the parameters).
void pause(); // pause the timer count without resetting the counter
void resume(); // continue counting after pause
void stop(); // stop counting and reset counter
void restart(); // restart the timer(reset counter)
void setDefault(); // set default timer parameters ("Arduino-defaults")
void outputEnable(channel, mode); // channel: enable timer output CHANNEL_A or CHANNEL_B (+ CHANNEL_C for Mega2560). Mode: TOGGLE_PIN, CLEAR_PIN, SET_PIN (toggle/disable/enable pin by interrupt)
void outputDisable(channel); // disable timer output CHANNEL_A or CHANNEL_B (+ CHANNEL_C for Mega2560, see timer table)
void outputState(channel, state); // change channel state: HIGH / LOW
void phaseShift(source, phase); // shift the channel phase by 0-360 degrees (for 8 bit timers, only channel B moves)
bool ready(uint8_t channel); // returns true if an interrupt occurred on the specified channel
bool ready(); // returns true if an interrupt occurred on channel A

/*
  The library gives direct access to an interrupt without the "Arduino" attachInterrupt, which allows
  reduce the call time of the interrupt handler function. Interrupt with configured frequency
  will be processed in a block like ISR(channel) {}, example:
*/
ISR(TIMER1_A) {
  // your code
}

ISR(TIMER1_B) {
  // your code
}

ISR(TIMER2_B) {
  // your code
}

ISR(TIMER0_A) {
  // your code
}

/*
-------------------------------- Arduino NANO 16 MHz (ATmega328) ----------- ------------------------
Timer | Bit depth | Frequencies | Periods | Outputs | Arduino pin | Pin MK |
--------|---------------|-----|----- --------------|-----------|------|------- |
timer0 | 8 bit | 61 Hz - 1 MHz | 16 384.. 1 µs | CHANNEL_A | D6 | PD6 |
        | | | | CHANNEL_B | D5 | PD5 |
--------|---------------|-----|----- --------------|-----------|------|------- |
timer1 | 16 bit | 0.24 Hz - 1 MHz | 4 200 000.. 1 µs | CHANNEL_A | D9 | PB1 |
        | | | | CHANNEL_B | D10 | PB2 |
--------|---------------|-----|----- -------------|Cranberries -----------|-------|-------|
timer2 | 8 bit | 61 Hz - 1 MHz | 16 384.. 1 µs | CHANNEL_A | D11 | PB3 |
        | | | | CHANNEL_B | D3 | PD3 |
-------------------------------------------------- --------------------------------------------------
                        
------------------------------ Arduino MEGA 16MHz (ATmega2560) ------------- -----------------------
Timer | Bit depth | Frequencies | Periods | Outputs | Arduino pin | Pin MK |
--------|---------------|-----|----- --------------|-----------|------|------- |
timer0 | 8 bit | 61 Hz - 1 MHz | 16 384.. 1 µs | CHANNEL_A | 13 | PB7 |
        | | | | CHANNEL_B | 4 | PG5 |
--------|---------------|-----|----- --------------|-----------|------|------- |
timer1 | 16 bit | 0.24 Hz - 1 MHz | 4 200 000.. 1 µs | CHANNEL_A | 11 | PB5 |
        | | | | CHANNEL_B | 12 | PB6 |
        | | | | CHANNEL_C | 13 | PB7 |
--------|---------------|-----|----- --------------|-----------|------|------- |
timer2 | 8 bit | 61 Hz - 1 MHz | 16 384.. 1 µs | CHANNEL_A | 10 | PB4 |
        | | | | CHANNEL_B | 9 | PH6 |
--------|---------------|-----|----- --------------|-----------|------|------- |
timer3 | 16 bit | 0.24 Hz - 1 MHz | 4 200 000.. 1 µs | CHANNEL_A | 5 | PE3 |
        | | | | CHANNEL_B | 2 | PE4 |
        | | | | CHANNEL_C | 3 | PE5 |
--------|---------------|-----|----- --------------|-----------|------|------- |
Timer4 | 16 bit | 0.24 Hz - 1 MHz | 4 200 000.. 1 µs | CHANNEL_A | 6 | PH3 |
        | | | | CHANNEL_B | 7 | PH4 |
        | | | | CHANNEL_C | 8 | PH5 |
--------|---------------|-----|----- --------------|-----------|------|------- |
Timer5 | 16 bit | 0.24 Hz - 1 MHz | 4 200 000.. 1 µs | CHANNEL_A |46 | PL3 |
        | | | | CHANNEL_B | 45 | PL4 |
        | | | | CHANNEL_C | 44 | PL5 |
-------------------------------------------------- --------------------------------------------------
*/
```

<a id="example"></a>
## Example
See **examples** for other examples!
```cpp
// Demonstration of all library functions

#include <GyverTimers.h>

void setup() {
  // Reconfigure the timer and set its period or frequency
  // All functions return the real period / frequency, which may differ from the entered ones
  Timer2.setPeriod(1000); // Set a specific period to 1000 µs (~ 1000 Hz), will return the real period in µs
  Timer0.setFrequency(250); // Set the timer interrupt frequency in Hz, returns the actual frequency in hertz
  Timer1.setFrequencyFloat(50.20); // Set the frequency more precisely, in fractional numbers, relevant for low frequencies and timer 1
  // From now on, the timer is already reconfigured and runs with the selected frequency / period

  // Connect the timer interrupt, from this moment interrupts will be called
  Timer0.enableISR(); // Connect a standard interrupt, channel A, without phase shift
  Timer2.enableISR(CHANNEL_B); // Enable timer 2 interrupt, channel B
  Timer1.enableISR(CHANNEL_A); // Connect interrupt channel A
  Timer1.enableISR(CHANNEL_B); // Connect second timer interrupt 1
  // The interrupt will already be called

  // If suddenly the interrupt needs to be disabled without stopping the timer
  Timer1.disableISR(CHANNEL_B);
  // From now on interrupt B will no longer be called

  // If you need to pause the timer COMPLETELY, hardware
  Timer2.pause();
  // From now on, the timer stands still, the contents of the counter remain untouched

  // Now the timer can be returned to service
  Timer2.resume();
  // The timer continued counting from the same place

  // If you need to completely stop the timer and reset the contents of the counter
  Timer1.stop();
  // The timer stops, the counter is reset

  // Return the timer in stringth
  Timer1.restart();
  // Timer restarted, started counting from the beginning

  // If you need to return the standard Arduino - timer settings
  Timer0.setDefault();
  // Now the timer works in standard mode
}

// interrupt vectors
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
- v1.1 - fixed a bug in the calculation of periods
- v1.2 - code split into h and cpp
- v1.3 - minor bug fixed
- v1.4 - fixed table of frequencies and periods
- v1.5 - fixed restart and resume
- v1.6 - phase shift rendered by a separate method
- v1.7 - documentation fixed
- v1.8 - fixed bug with max period
- v1.9 - fixed bug with returning 2x period
- v1.10 - ready flag added

<a id="feedback"></a>
## Bugs and feedback
When you find bugs, create an **Issue**, or better, immediately write to the mail [alex@alexgyver.ru](mailto:alex@alexgyver.ru)
The library is open for revision and your **Pull Request**'s!