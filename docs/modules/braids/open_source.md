[Github repository](https://github.com/pichenettes/eurorack/tree/master/braids)

[Schematics](downloads/braids_v50.pdf)

## Firmware hacking

Braids' source code is available under the MIT licence.

The code (along with the hardware description files) can be found in the `braids` directory in our [Eurorack modules git repository](https://github.com/pichenettes/eurorack).

After having cloned the repository, don't forget to run `git submodule init && git submodule update` to make sure the sub-projects referenced in the code are also pulled.

### Playground

A simple way of testing the oscillators code locally on a desktop computer, without long flash/test cycles, is to use the command line program in braids/test. It can be built and run with `make -f braids/test/makefile && ./oscillator_test`. No embedded toolchain is needed for that! The program generates a .wav file, `oscillator.wav`. Playing with the methods of the `MacroOscillator` class is a good way of familiarizing oneself's with Braids code.

### Toolchain

If you don't mind installing [Vagrant](https://www.vagrantup.com/) and [VirtualBox](https://www.virtualbox.org/wiki/Downloads) a [cozy environment](https://github.com/pichenettes/mutable-dev-environment) for firmware hacking is available.

If you want to set up your own environment to build Braids' code, an ARM EABI toolchain must be installed. Because of tight CPU and code size limits, we recommend you to use the same compiler version as we do: `4.8-2013-q4-major`. Various pre-compiled binaries and source packages are available [here](https://launchpad.net/gcc-arm-embedded/4.8/4.8-2013-q4-major/).

The path to the toolchain binaries must be specified in the `TOOLCHAIN_PATH` variable in `braids/makefile`.

To build the bootloader, use the following command:

```
make -f braids/bootloader/makefile hex
```


To build the code, use the following command:

```
make -f braids/makefile
```

If you modify lookup tables and want the big `resources.cc` file to be regenerated:

```
touch braids/resources/resources.py && make -f braids/makefile resources
```

### Firmware programming

A first solution is to simply use the firmware update procedure. A .wav file for firmware upgrade can be generated with:

```
make -f braids/makefile wav
```

The firmware can then be loaded into the module using the procedure described in the manual.

**Past this point, we assume you know what you are doing and we are not responsible for any damage to your module!**

Another solution is to use the built-in serial bootloader of the STM32F. Connect a FTDI dongle with a 3.3V output level ([such as this](http://www.adafruit.com/products/284)) to the 6 pin connector at the back of the module. If necessary, edit the serial port special file corresponding to the FTDI dongle in the PGM_SERIAL_PORT variable in stmlib/makefile.inc. GND must match GND, TX must match RX and vice-versa.

Hold the **RESET** switch on the side of the module. Press the **SYSBOOT** switch next to it, and release **RESET**. Nothing is shown on the module display (it is in bootloader limbo...)

Upload the firmware with:

```
make -f braids/makefile upload_combo_serial
```

The last – and recommended – solution for firmware programming is to use a JTAG interface and openOCD. We recommend Olimex' [ARM-USB-OCD-H devices](https://www.olimex.com/Products/ARM/JTAG/ARM-USB-OCD-H/). An adapter must also be purchased for the [mini-JTAG](http://www.embeddedartists.com/products/acc/acc_jtag_adapter_kit.php) connector used by Braids.

Upload the firmware with:

```
make -f braids/makefile upload_combo_jtag
```

### What do all these files do?

#### Directory structure

**/bootloader** contains the bootloader. This is a short program describing the first things the module does when it is powered on. It either jumps to the main code, or puts the module in firmware update mode. Think about it: the firmware update code cannot be part of the main firmware - otherwise it would need to erase and rewrite itself during a firmware update!

**/data** contains raw binary data (waveforms and layout of the wavetables). This data is loaded and processed by the python scripts in /resources (more about that later).

**/drivers** are C++ classes that describe how to "talk to" Braids' hardware - this provides routines for writing a character on the display, send a sample to the DAC, etc. At the exception of this code and the code in braids.cc, all the other .cc / .h files are designed so that they can be compiled and run on both the actual hardware and a desktop computer. This allows me to test/design bits of code without having to send it to the actual hardware (which is slow and doesn't always allow me to observe and reproduce bugs).

**/hardware\_design** are the hardware description files (Eagle board layout and panel drawings).

**/resources** are python scripts that build lookup tables. Some DSP algorithms make use of functions that are expensive to compute on the STM32F (like trigonometric functions, or exponential functions, or even divisions). To cut CPU use, I try to precompute as much stuff as possible on the computer building the code, and just store the results in lookup tables. All the scripts in /resources are run during compilation, and they generate a big file, resources.cc with the pre-computed results. This file also stores data like wavetables.

**/test** is a simple command line program allowing you to run Braids' synthesis code on your computer. It generates a .wav file with the resulting audio signal, and you can modify the program to change the TIMBRE / COLOR and pitch parameters.

**init.py** is a file required by the python interpreter declaring that the directory or subdirectories contain python modules.

**makefile** describes the steps to compile the files into a binary file for the STM32F (it provides configuration data for the real meaty stuff in stmlib/makefile.inc)

Everything else are C++ source files.

#### Code tour

The build process is the following:

1.  The python scripts are run to generate resources.cc and resources.h.
2.  All the .cc files in /bootloader are compiled to make a .hex files with the bootloader.
3.  All the .cc files in / are compiled to make a .hex file with the main code.
4.  The two hex files are merged together, and that's what is written to the MCU.

**analog\_oscillator** contains DSP code for basic "analog" waveforms (sawtooth, square...)

**braids** is the "top-level" code - what the module does when it starts (hardware initialization), what the module does every 1ms (UI handling), what the module does for every sample (calls into DSP code and DAC writes).

**digital\_oscillator** contains DSP code for all the fancy digital oscillator algorithms.

**envelope** is the internal AD envelope generator.

**excitation** comes from Peaks' code and is used for the 808 style drum synthesis.

**macro\_oscillator** contains the oscillator algorithms that are built by gluing together several basic analog type oscillators.

**parameter\_interpolation.h** are C macros for smoothing TIMBRE/COLOR/pitch parameters over the duration of a block of 24 samples.

**quantizer** is the built-in quantizer.

**quantizer\_scales** are lookup tables with the scales for the quantizer.

**resources** are pre-computed lookup tables.

**settings** contains the range and name of all the settings, along with code for making them persist when the module is powered off/on.

**signature\_waveshaper** contains the code for the SIGN feature.

**svf** is a digital state variable filter used in the 808 emulations.

**ui** contains all the menus and encoder-related stuff.

**vco\_jitter\_source** is the random generator generating VCO-style "sloppiness".
