[Github repository](https://github.com/pichenettes/eurorack/tree/master/streams)

[Schematics](downloads/streams_v05.pdf)

## Firmware hacking

Streams' source code is available under the MIT licence.

The code (along with the hardware description files) can be found in the `streams` directory in our [Eurorack modules git repository](https://github.com/pichenettes/eurorack).

After having cloned the repository, don't forget to run `git submodule init && git submodule update` to make sure the sub-projects referenced in the code are also pulled.

### Toolchain

If you don't mind installing [Vagrant](https://www.vagrantup.com/) and [VirtualBox](https://www.virtualbox.org/wiki/Downloads) a [cozy environment](https://github.com/pichenettes/mutable-dev-environment) for firmware hacking is available.

If you want to set up your own environment to build Streams' code, an ARM EABI toolchain must be installed. Because of tight CPU and code size limits, we recommend you to use the same compiler version as we do: `4.8-2013-q4-major`. Various pre-compiled binaries and source packages are available [here](https://launchpad.net/gcc-arm-embedded/4.8/4.8-2013-q4-major/).

The path to the toolchain binaries must be specified in the `TOOLCHAIN_PATH` variable in `streams/makefile`.

To build the bootloader, use the following command:

```
make -f streams/bootloader/makefile hex
```


To build the code, use the following command:

```
make -f streams/makefile
```

If you modify lookup tables and want the big `resources.cc` file to be regenerated:

```
touch streams/resources/resources.py && make -f streams/makefile resources
```

### Firmware programming

A first solution is to simply use the firmware update procedure. A .wav file for firmware upgrade can be generated with:

```
make -f streams/makefile wav
```

The firmware can then be loaded into the module using the procedure described in the manual.

**Past this point, we assume you know what you are doing and we are not responsible for any damage to your module!**

Another solution is to use the built-in serial bootloader of the STM32F. Connect a FTDI dongle with a 3.3V output level ([such as this](http://www.adafruit.com/products/284)) to the 6 pin connector at the back of the module. If necessary, edit the serial port special file corresponding to the FTDI dongle in the PGM_SERIAL_PORT variable in stmlib/makefile.inc. GND must match GND, TX must match RX and vice-versa.

Hold the **RESET** switch on the side of the module. Press the **SYSBOOT** switch next to it, and release **RESET**. Nothing is shown on the module display (it is in bootloader limbo...)

Upload the firmware with:

```
make -f streams/makefile upload_combo_serial
```

The last – and recommended – solution for firmware programming is to use a JTAG interface and openOCD. We recommend Olimex' [ARM-USB-OCD-H devices](https://www.olimex.com/Products/ARM/JTAG/ARM-USB-OCD-H/). An adapter must also be purchased for the [mini-JTAG](http://www.embeddedartists.com/products/acc/acc_jtag_adapter_kit.php) connector used by Streams.

Upload the firmware with:

```
make -f streams/makefile upload_combo_jtag
```
