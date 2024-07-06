This is an automatic translation, may be incorrect in some places. See sources and examples!

# Gyvertimers
Setting up and controlling interruptions on the hardware timer ATMEGA328P, Atmega2560
- all three timers on Atmega328 and six timers on Atmega2560 are supported;
- Setting up the period (ISS) and frequency (Hz) interruptions:
    - 8 bit timers: 61 Hz - 1 MHz (16 384 μs .. 1 μs);
    - 16 bits of timers: 0.24 Hz - 1 MHz (4,200,000 μs .. 1 μs);
- automatic adjustment of the settings of the period from the tactive frequency (F_CPU);
- the function returns the exact established period/frequency for debugging (the frequency is limited by the timer resolution);
- Support for the multichannel mode of operation: one timer calls 2 (atmega328) or
3 (ATMEGA2560, timers 1, 3, 4, 5) interruption with a custom-made shift in a phase of 0-360 degrees;
- the customizable action of the hardware output of the timer for interruption: high signal, low signal, switching.
Allows you to generate a meander (one- and two-stroke);
- timer control: start/stop/pause/continue/initialization;

## compatibility
Atmega328p, Atmega2560

### Documentation
There is [extended documentation](https://alexgyver.ru/gyvertimers/) to the library 

## Content
- [Installation](#install)
- [Initialization](#init)
- [Usage](#usage)
- [Example](#example)
- [Versions](#versions)
- [Bugs and feedback](#feedback)

<a id="install"> </a>
## Installation
- The library can be found by the name **gyvertimers** and installed through the library manager in:
    - Arduino ide
    - Arduino ide v2
    - Platformio
- [download the library](https://github.com/gyverlibs/gyvertimers/archive/refs/heads/main.zip) .Zip archive for manual installation:
    - unpack and put in * C: \ Program Files (X86) \ Arduino \ Libraries * (Windows X64)
    - unpack and put in * C: \ Program Files \ Arduino \ Libraries * (Windows X32)
    - unpack and put in *documents/arduino/libraries/ *
    - (Arduino id) Automatic installation from. Zip: * sketch/connect the library/add .Zip library ... * and specify downloaded archive
- Read more detailed instructions for installing libraries [here](https://alexgyver.ru/arduino-first/#%D0%A3%D1%81%D1%82%D0%B0%BD%D0%BE%BE%BE%BED0%B2%D0%BA%D0%B0_%D0%B1%D0%B8%D0%B1%D0%BB%D0%B8%D0%BE%D1%82%D0%B5%D0%BA)
### Update
- I recommend always updating the library: errors and bugs are corrected in the new versions, as well as optimization and new features are added
- through the IDE library manager: find the library how to install and click "update"
- Manually: **remove the folder with the old version**, and then put a new one in its place. “Replacement” cannot be done: sometimes in new versions, files that remain when replacing are deleted and can lead to errors!


<a id="init"> </a>
## Initialization
No

<a id="usage"> </a>
## Usage
```CPP
Methods for Timer0, Timer1, Timer2 ...
uint32_t setperiod (period);// Installation of the period in microseconds and starting the timer.Returns the real period (accuracy is limited by the timer resolution).
uint32_t setfrequency (frequency);// Frequency installation in Hertz and the timer launch.Returns the real frequency (accuracy is limited by the timer resolution).
Float SetFrequencyFloat// Frequency installation in Hertsa and the timer launch, decimal fractions are allowed.Returns the riverfrequency (accuracy is limited by timer resolution).
VOID Enableisr (source);// Turn on the interruptions, the channel of interruptions Channel_a or Channel_b (+ Channel_C by Mega2560)
Void Disableisr (source);// Turn off the interruptions, channel Channel_a or Channel_b.The timer account does not stop (the channel A will be turned off without specifying the parameters).
VOID PAUSE ();// suspend the timer account without dropping the counter
VOID Resume ();// Continue the score after a pause
VOID Stop ();// stop the score and drop the counter
VOID RESTART ();// restart the timer (drop the counter)
VOID Setdefault ();// Install the default timer parameters ("arduino-wilting")
VOID outputenable (channel, mode);// Channel: Turn on the output of the Channel_a or Channel_b timer (+ channel_c in Mega2560).Mode: Toggle_pin, Clear_pin, Set_pin (switch/turn off/turn on the PIN on interruption)
VOID outputdisable (channel);// Disconnect the output of the Channel_a or Channel_b timer (+ channel_c from Mega2560, see Thamere Taklitsa)
VOID outputState (channel, condition);// Change the condition of the channel: High / Low
VOID PHASSHIFT (source, phase);// shift the phase of the channel by 0-360 degrees (only channel b moves 8 bits of timers)
Bool Ready (Uint8_T Channel);// returns True if an interruption on the specified channel occurs
Bool Ready ();// returns True if there is an interruption on the channel A

/*
  The library gives direct access to interruption without “arduinsky” Attachinterrapt, which allows
  Reduce the time of calling the interruption processor function.Interruption with configured frequency
  will be processed in the block of the type of ISR (channel) {}, example:
*/
ISR (Timer1_a) {
  // Your code
}

ISR (Timer1_b) {
  // Your code
}

ISR (Timer2_b) {
  // Your code
}

ISR (Timer0_A) {
  // Your code
}

/*
------------------------------------------- ARDUINO Nano 16 MHz (atmega328) ------------------------------------
Timer |Discharge |Frequencies |Periods |Exits |Pin Arduino |PIN MK |
-------- | ------------------ | -------------------------------------------------------------- | ----------- | ------------------------------------------------------|
Timer0 |8 bits |61 Hz - 1 MHz |16 384 .. 1 μs |Channel_a |D6 |PD6 |
        ||||Channel_b |D5 |PD5 |
-------- | ------------------ | -------------------------------------------------------------- | ----------- | ------------------------------------------------------|
Timer1 |16 bit |0.24 Hz - 1 MHz |4 200 000 .. 1 μs |Channel_a |D9 |PB1 |
        ||||Channel_b |D10 |PB2 |
-------- | ------------------ | -------------------------------------------------------------- | ----------- | ------------------------------------------------------|
Timer2 |8 bits |61 Hz - 1 MHz |16 384 .. 1 μs |Channel_a |D11 |PB3 |
        ||||Channel_b |D3 |PD3 |
------------------------------------------------------------------------------------------
                        
-------------------------------------------- MEGA 16 MHz (atmega2560) ---------------------------------------
Timer |Discharge |Frequencies |Periods |Exits |Pin Arduino |PIN MK |
-------- | ------------------ | -------------------------------------------------------------- | ----------- | ------------------------------------------------------|
Timer0 |8 bits |61 Hz - 1 MHz |16 384 .. 1 μs |Channel_a |13 |PB7 |
        ||||Channel_b |4 |PG5 |
-------- | ------------------ | -------------------------------------------------------------- | ----------- | ------------------------------------------------------|
Timer1 |16 bit |0.24 Hz - 1 MHz |4 200 000 .. 1 μs |Channel_a |11 |PB5 |
        ||||Channel_b |12 |PB6 |
        ||||Channel_c |13 |PB7 |
-------- | ------------------ | -------------------------------------------------------------- | ----------- | ------------------------------------------------------|
Timer2 |8 bits |61 Hz - 1 MHz |16 384 .. 1 μs |Channel_a |10 |PB4 |
        ||||Channel_b |9 |PH6 |
-------- | ------------------ | -------------------------------------------------------------- | ----------- | --------------- | ------ |
Timer3 |16 bit |0.24 Hz - 1 MHz |4 200 000 .. 1 μs |Channel_a |5 |PE3 |
        ||||Channel_b |2 |PE4 |
        ||||Channel_c |3 |PE5 |
-------- | ------------------ | -------------------------------------------------------------- | ----------- | ------------------------------------------------------|
Timer4 |16 bit |0.24 Hz - 1 MHz |4 200 000 .. 1 μs |Channel_a |6 |PH3 |
        ||||Channel_b |7 |PH4 |
        ||||Channel_c |8 |PH5 |
-------- | ------------------ | -------------------------------------------------------------- | ----------- | ------------------------------------------------------|
Timer5 |16 bit |0.24 Hz - 1 MHz |4 200 000 .. 1 μs |Channel_a |46 |PL3 |
        ||||Channel_b |45 |PL4 |
        ||||Channel_c |44 |PL5 |
------------------------------------------------------------------------------------------
*/
```

<a id="EXAMPLE"> </a>
## Example
The rest of the examples look at ** Examples **!
```CPP
// Demonstration of all functions of the library

#include <gyvertimers.h>

VOID setup () {
  // reconfigure the timer and set him a period or frequency
  // All functions return the real period / frequency that may differ from the introduced
  Timer2.Setperiod (1000);// set a specific period of 1000 μs (~ 1000 Hz), will return the real period to the ISS
  Timer0.setfrequency (250);// Set the frequency of the timer interruptions in Hz, will return the real frequency in Herza
  Timer1.setfrequencyFloat (50.20);// Set the frequency more accurately, in fractional numbers, it is relevant for low frequencies and timer 1
  // From that moment on, the timer is already reconfigured and drives with a fractured frequency / period

  // Connect the interruption of the timer, from this moment the interruption will begin to be called
  Timer0.enableisr ();// connect the standard interruption, channel A, without a phase retire
  Timer2.enableisr (Channel_b);// Connect the interruption of timer 2, channel b
  Timer1.enableisr (Channel_a);// Connect the interruption of the channel a
  Timer1.enableisr (Channel_b);// connect the second interruption of timer 1
  // interruption will already begin to be called

  // if suddenly the interruption needs to be turned off without stopping the timer
  Timer1.disableisr (Channel_b);
  // From this moment, interruption b will no longer be called

  // if you need to suspend the timer completely, hardware
  Timer2.pause ();
  // From that moment on the timer stands still, the contents of the counter remains untouched

  // Now the timer can be returned to duty
  Timer2.Resum ();
  // Timer continued to count from the same place

  // if you need to completely stop the timer and drop the contents of the counter
  Timer1.Stop ();
  // Timer stands, the counter is dropped

  // Return the timer to duty
  Timer1.Restart ();
  // The timer is restarted, began to count from the beginning

  // if you need to return the standard arduino - timer settings
  Timer0.Setdefault ();
  // Now the timer works in Stanland mode
}

// Vectors of interruptions
ISR (Timer1_a) {
}

ISR (Timer1_b) {
}

ISR (Timer2_b) {
}

ISR (Timer0_A) {
}

VOID loop () {

}
```

<a id="versions"> </a>
## Versions
- V1.1 - Fixed error in the calculation of periods
- V1.2 - The code is divided into h and CPP
- v1.3 - a slight bug is corrected
- V1.4 - Fixed frequency and periods are fixed
- V1.5 - Restart and Resume fixed
- V1.6 - Phase Shift is made in a separate method
- v1.7 - documentation corrected
- V1.8 - Fixed bug with max period
- V1.9 - Fixed a bug with a return 2 -period
- V1.10 - added flag of Ready

<a id="feedback"> </a>
## Bugs and feedback
Create an **issue** when you find bugs, and better immediately write to the mail [alex@alexgyver.ru](mailto:alex@alexgyver.ru)
The library is open for refinement and your **pull requests**.


When reporting about bugs or incorrect work of the library, it is necessary to indicate:
- The version of the library
- What is MK used
- SDK version (for ESP)
- Version of Arduino IDE
- Whether the built-in examples work correctly, in which the functions and designs are used, leading to a bug in your code
- What code is loaded, what was expected, and how it works in reality
- Ideally, attach the minimum code in which the bug is observed. Not a thousand lines, but a minimum code.
