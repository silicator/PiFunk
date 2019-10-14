# README

![alt text](https://raw.githubusercontent.com/silicator/PiFunk/master/docs/favicon.ico "Logo PiFunk")

## PiFunk Radio Transmitter - with FM/AM-Modulation for HAM-Bands

**Early Experimental! (WIP)**

___

### Configurations

1. Get this program:

`sudo apt-get install git` via git or download it on the page as tar.gz/zip

`git clone https://github.com/silicator/PiFunk`

2. To configure the Pi for modules via menu (I2C, UART etc.): `sudo raspi-config`

Using w1-gpio sometimes needs a 4.7 - 10 kΩ pullup resistor connected on GPIO Pin

(if you have problems deactivate 1-wire config!)

1-Wire by default BCM4 setting needs to be activated in boot-config for autostart

3. Manually open with nano-editor: `sudo nano /boot/config.txt` (i provide one too)

check/add lines:

`dtoverlay=w1-gpio,gpiopin=4,pullup=0` add pullup=1 if needed

`dtoverlay=audio=on` for bcm audio

optional:

`dtparam=spi=on` for SPI support

`dtoverlay=i2c1-bcm2708` for I2C Bus

`enable_uart=1` for UART RX & TX

`init_uart_baud=9600` data transmission rate

`dtoverlay=pps-gpio,gpiopin=18` for GPS-device pps(puls-pro-second)-support

Sync to GPS 1 PPS signal for Pi PCM-Clock (PIN 12 / GPIO 18 = PCM_CLK / PWM0) for accuracy

`sudo nano /etc/modules` opens modules.conf with text editor (provide one too)

`pps-gpio` Add this line

5. Save your changes with ctrl-o <return/enter> then exit with ctrl-x

___

### Installations

5. First update & upgrade system:

`sudo apt-get update` for system updates

`sudo apt-get upgrade` for system upgrades

6. You will need some libraries for this:

a) `sudo apt-get install libraspberrypi-dev raspberrypi-kernel-headers` for Kernel & Firmware

b) `sudo apt-get install libsndfile1-dev` for ALSA SND-lib

or download it directly [SND](https://packages.debian.org/de/sid/libsndfile1-dev)

c) `sudo apt-get install python-dev python3-dev` for py3

d) [RPi.GPIO lib v0.7.0 for Py3](https://files.pythonhosted.org/packages/cb/88/d3817eb11fc77a8d9a63abeab8fe303266b1e3b85e2952238f0da43fed4e/RPi.GPIO-0.7.0.tar.gz) (also in repo)

[RPi.GPIO Project Site](https://pypi.org/project/RPi.GPIO/) 

[RPi.GPIO Sourceforge-Site](https://sourceforge.net/projects/raspberry-gpio-python/files/)

or download via terminal: `sudo wget https://pypi.python.org/packages/source/R/RPi.GPIO/RPi.GPIO-0.7.0.tar.gz`

then extract: `tar -xvf RPi.GPIO-0.7.0.tar.gz`

and install it: `sudo pip-3.7 install RPi.GPIO` for Py3 (easiest way)

or alternative way: `sudo apt-get -y install python3-rpi.gpio`

7. Compiler installation:

a) GNU C Compiler GCC `sudo apt-get install gcc-9.2.0` or g++

b) GNU Debugger GDB `sudo apt-get install gdbserver`

8. `sudo reboot` then reboot to apply the changes

___

### Build

9. Navigate to directory:

a) `cd PiFunk` with default path: `/home/pi/PiFunk/`

b) GCC Compiler flags:

`-g3` for normal GNU compiler debug informations (1-3 level, 2 is default)

`-ggdb3` for GNU debugger informations level 3

`-Wall` for debug all warning informations

`-Werror` for debug error informations

`-v` Print compilation verbose informations

`-std=c99` (as iso `-std=iso9899:1999` strict)

`-std=gnu99` with additional gnu extension to c99

(`-std=gnu++17` if you like when using g++)

`-pedantic-errors` for error console messages if problem between c99 and gnu extensions

`-Iinclude ` for using include-directory with header files

`-I/opt/vc/include/` for loading bcm header folder

`-L/opt/vc/lib` for loading bcm folder

`-Llib` for using library-directory

`-lm` for math-lib is obligatory!

`-lbcm_host` for loading bcm firmware >= v1.20190718

`-lpthread` lib for process threads

`-lgthread` lib for graphic threads

`-lsndfile` -l links library name for ALSA "snd"-lib

`-shared` for generating shared object libraries

`-c` for compiling without linking for making object

`-E` for stopping after preprocessing stage in compilation

`-D_USE_MATH_DEFINES` for mathematical lib definitions

`-D_GNU_C_SOURCE` for loading GNU C Source Macros for non-std setups

(combining lot of different ones: ISO C89, ISO C99, POSIX.1, POSIX.2, BSD, SVID, X/OPEN, LFS)

`-D_POSIX_C_SOURCE=200809L` for POSIX2 Macros needed with bcm (or 199309L for POSIX1)

`-DRASPI=1` defines the macro to be used by the preprocessor (here the Pi model or 0-4, else std-values 0-3)

 -> will be detected by the Makefile via the type of the ARM-Processor

 (other macros possible if in the C-code implemented)

`-fPIC` for generating position independent code (PIC) for bigger programs

`-O3` for optimization stage 1-3 (memory, speed etc.) via compiler

`-o` for individual output-filename flag

10. Generating libraries:

a) Image of the GCC Flow-diagram for generating [Libraries](docs/GCC_Schema.jpg)

*.c=C-code, *.h=headerfile,

*.i=assembled preprocessor C-code, *.S=assembler-code, *.s=preprocessed assembler-code,

*.o=compiled object, *.lib=library object, *.a=archive object, *.so=shared dynamic library object,

*.out=default binary, pifunk(.bin)=executable binary (program)

b) manually compiling/linking libraries:

`sudo gcc -Wall -Werror -std=gnu99 -pedantic-errors -g3 -ggdb3 -Iinclude -Llib -I/opt/vc/include -lbcm_host -lm -lsndfile -lpthread -shared -O3 -fPIC pifunk.c -D_USE_MATH_DEFINES -D_GNU_SOURCE -DRASPI=1 -o include/pifunk.i lib/pifunk.s lib/pifunk.o lib/pifunk.a lib/pifunk.lib lib/pifunk.so`

c) manually compiling/linking executable binary:

`sudo gcc -Wall -Werror -std=gnu99 -pedantic-errors -g3 -ggdb3 -Iinclude -Llib -I/opt/vc/include -lbcm_host -lm -lsndfile -lpthread -shared -O3 -fPIC pifunk.c -D_USE_MATH_DEFINES -D_GNU_SOURCE -DRASPI=1 -o bin/pifunk`

d) optional Pi-Flags:

 `-march=armv6l` architecture version of ARM ("native" is auto option)

 `-mtune=arm1176jzf-s` special architecture type tuning

 `-mfloat-abi=hard` floating-point ABI to use, permissible values are: ‘soft’, ‘softfp’, ‘hard’

 `-mfpu=vfp` virtual floating point hardware module support, for pi2/3 use neon-vfpv4

 `-ffast-math` increase speed for float ops and outside the IEEE-standard and deactivates errno-functions

 `sudo piversion` for checking your pi version

e) Makefile commands:

 `sudo make piversion` for checking your pi version via make

 `sudo make install` for installing pifunk files incl. build folder

 `sudo make uninstall` for uninstalling pifunk files

 `sudo make` for compilation with pre-configured flags for compilation for all Pi's

 `sudo make run` for running with standard pifunk flags

 `sudo make help` for starting help command of pifunk

 `sudo make assistant` for starting step-assistant of pifunk

 `sudo make clean` for removing pifunk.out and pifunk.o files in bin folder if necessary

___

### Preparations

11. Hardware-Setup:

a) Use (original) power supply 10 W, 5 V @ ~2 A or ~5 V/500 mA via mini-USB 2.0 or 5 V Pins possible)

b) Check specifications: my Pi B+ v1.2 @ 700 MHz / 512 MB RAM on ARM processor with driver bcm2835-v1.55

-> SoC from Broadcom	depending on pi model: BCM2711, BCM2835,	BCM2836,	BCM2837,	BCM2837B0

for more infos on other boards just visit [Adafruit](http://www.adafruit.com)

or [Wikipedia Spec Summary](https://de.wikipedia.org/wiki/Raspberry_Pi)

c) Antenna to GPCLK0 (GPIO 4, PIN 7) for PWM (Pulse with Modulation)

@ 2-4 mA (max. 50 mA on ALL PINs and 16 per bank!)

- Antenna should be grounded (see Pinout image) to prevent noise and other problems

- For transmission you should use tested/certified antennas with mounts (BNC/SDA/PL - m/f) if possible

- Tip: You could use just a copper wire for antenna:

 CB 11 m-Band (lambda/2, 5.5 m, 216.535" in) and 70 cm-Band (PMR) (lambda(1/4), 17.0 cm, 6.7" in)

![Pinout](docs/pinout-gpio-pib+.jpg)

d) You can try to smooth the Resistence R out with a 1:X (2-43)-balun if using long HF antenna

- Dummy-load: 1-100 W @ 50 Ohm "cement" or similar (aluminium case) with cooler for testing

e) For handling overheating of the Pi's processor use cooling-ribs with fan (5 V DC/0.2 A - 20x20 mm)

you can overclock the Pi if you want to on own risk but it's not recommended

f) RTC: Module DS3231 uses

3.3 V (PIN 1), SDA0 (PIN 3, GPIO0 on I2C), SCL0 (PIN 5, GPIO1 on I2C) & GND (PIN 9)

-> need to activate I2C in pi config!

![RTC](docs/RTC-top.jpg)

g) GPS Module: Ublox Neo 7M 

Pinout: 5 V (PIN 4), GND (PIN 6), RX to UART-TXD (GPIO 14 PIN 8), TX to UART-RXD (GPIO 15, PIN 10), PPS to PCM_CLK (GPIO 18, PIN 12)

it prints in NMEA format so change config `ttyAMA0` to `tty1`

`sudo cat /dev/ttyAMA0` or alternative `sudo cat /dev/ttyS0`

-> need to activate UART (serial 0) in pi config! Yes here crosswiring!! -> (RX of GPS receives what Pi TX'ed)

![GPS](docs/GPS-Neo7M.jpg)

h) Morse-code-table:

Will be implemented later!

![Morsecode](docs/morsecodeCW.jpg)

___

### Run

12. Run with admin/root permissions:

Arguments: would be best to input in this specific order to prevent problems

Use '. dot' as decimal-comma separator!

`[-n <filename (.wav)>] [ -f <freq (MHz)>] [-s <samplerate (kHz)>] [-m <modulation (fm/am)>] [-c <callsign (optional)>] [-p <power 0-7)>]`

extra single menu-flags: -> no further argument needed

`[-a]` for assistant in step-by-step

`[-h]` for help with more infos and arguments

`[-u]` for extra menu (csv, commandline)

default: `sudo ./pifunk -n sound.wav -f 446.006250 -s 22050 -m fm -c callsign -p 7`

Radio works with .wav-file with 16-bit @ 22050.000 [Hz] mono / 0.1-700 to 1500 MHz range depending on the Pi.

It's recommended not to transmit on frequencies higher than the processor speed at the moment to prevent stuttering/lags,

but results would be interesting to know with overclocking.

Explicit CTSS-Tones (38 included) for PMR can be found here as a list: [CTSS](ctsspmr.csv)

___

### Disclaimer

13. Warnings/Caution:

- Private Project! It's Early Access & Work in Progress (WIP)!

- I'm not a professional so **NO guarantees or warranty** for any damage etc.!!

- Usage at **your own risk** !!

- Check laws of your country first! Some Frequencies & Powerlevels (Watt) are prohibited or need at least a HAM-License!

- Pi operates with square-waves (²/^2)!! Use Low-/High-Band-Pass-Filters with dry (not electrolytic) capacitors

  (C=10-100 pF) with solenoid (ring) toroid (ferrite) chokes (B=10-50 uH like the FT37-43)

  or resistors (R=~10 kOhm), diodes to prevent backflow

  transmission (TX) simultaneously on permitted frequencies! -> [Bandpass-Diagram](docs/filter_600.jpg)

* Help / Testers and Feedback are always appreciated! :)

* Thank you and have fun 73!

___

### Links:

14. Additional Guidelines

[GitPage](https://silicator.github.io/PiFunk/)

[Wiki](https://github.com/silicator/PiFunk/wiki)

[Readme Guideline](README.md)

[Contribution Guideline](docs/CONTRIBUTING.md)

[Code of Conduct Guideline](docs/CODE_OF_CONDUCT.md)

[Copying Guideline](docs/COPYING.md)

[License Guideline](LICENSE.md) under Open-Source GPLv3.0

Would appreciate being named in the source, Thank you.

### Acknowledgements

 15. Credits

based on pifm/am, pifmadv scripts/snippets
