# PiPsu
Arduino based power control for RaspberryPi with IR remote control

The goal of this project is to create simple arduino based electronics and software. It allows the Raspberry Pi to be powered on and off through a push button. Optionally the unit can be controlled through learneable IR commands.

## Concept
The PiPSU powers the Pi through it's standard micro-usb port for safety reasons (powering the Pi through GPIO is unsafe). For that, an old usb cable has to be sacrified.

The PSU communicates with the PI through the serial port for configuration. To manage power cycles, two GPIO are required. One to sense the PI state (State-GPIO) and one to tell the OS running on the PI to shutdown gracefully (Signal-GPIO).

The State-GPIO indicates whether the PI is running or not. A High level indicates "Running" state and a Low level indicates "Halt" state.
The Signal-GPIO is kept in Low state until a shut down is requested. In this case, it is driven High until power to the PI is cut.

### Power ON/OFF sequences

#### Power On
The power-on sequence can be executed when the power is off. A short press on the push-button or the reception of the "on" IR code starts the power-on sequence.

1. The PSU toggles the PI power on.
2. The status led starts blinking.
3. The PSU waits for the State-GPIO to go High.
4. When the State-GPIO goes High the sequence is complete.
5. The status led glows steady.

If after a certain configurable time-out the State-GPIO does not go High, the sequence is aborted and power is cut again.

#### Power Off
The power-off sequence can be executed when the PI power is on and the State-GPIO is High. A short press on the pus-button or the reception of the "off" IR code starts the power-off sequence.

1. The PSU toggles the Signal-GPIO Low.
2. The status led starts blinking.
3. The PSU waits for the State-GPIO to go Low.
4. When the State-GPIO goes Low, the sequence is complete.
5. The PSU toggles the PI power off.
6. The status led stops glowing.

If after a certain configurable time-out the State-GPIO does not go Low, power is cut anyway.

#### Force power Off
The force power-off sequence can be executed when the PI power is on. A long press on the push-button starts the sequence.

In this sequence, the power is immediately cut. This results in an unclean shut-down and should be used only when the PI stops responding.

## Project components
The project consists of three components:
1. A minimal and easy to build Arduino based control board
2. Firmware for the Arduino
3. A python based script that communicates with the Arduino PSU.

### The Arduino board
The power supply board can be soldered on a simple stripboard. Schematics are created with the program "Fritzing".

The Arduino board is based on:
* A standalone Atmel ATMega329p that is the heart of the original ArduinoUno.
* A push button
* A led (can be embedded in the push button)
* An optional IR receiver sensor
* A MOSFET to switch power on and off
* An old micro USB cable

### The firmware
The firmware consists of a single ".ino" file handling all the firmware code.
