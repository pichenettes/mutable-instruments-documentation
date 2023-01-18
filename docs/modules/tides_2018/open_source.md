[Github repository](https://github.com/pichenettes/eurorack/tree/master/tides2)

[Schematics](downloads/tides_v40.pdf)

## Firmware hacking

Tides' source code is available under the MIT licence.

The code (along with the hardware description files) can be found in the `tides2` directory in our [Eurorack modules git repository](https://github.com/pichenettes/eurorack).

After having cloned the repository, don't forget to run `git submodule init && git submodule update` to make sure the sub-projects referenced in the code are also pulled.

### Toolchain

If you don't mind installing [Vagrant](https://www.vagrantup.com/) and [VirtualBox](https://www.virtualbox.org/wiki/Downloads) a [cozy environment](https://github.com/pichenettes/mutable-dev-environment) for firmware hacking is available.

If you want to set up your own environment to build Tides' code, an ARM EABI toolchain must be installed. Because of tight CPU and code size limits, we recommend you to use the same compiler version as we do: `4.8-2013-q4-major`. Various pre-compiled binaries and source packages are available [here](https://launchpad.net/gcc-arm-embedded/4.8/4.8-2013-q4-major/).

The path to the toolchain binaries must be specified in the `TOOLCHAIN_PATH` variable in `tides2/makefile`.

To build the bootloader, use the following command:

```
make -f tides2/bootloader/makefile hex
```


To build the code, use the following command:

```
make -f tides2/makefile
```

If you modify lookup tables and want the big `resources.cc` file to be regenerated:

```
touch tides2/resources/resources.py && make -f tides2/makefile resources
```

### Firmware programming

The recommended solution for firmware programming is to use a ST Discovery board. Connect the 4 SWD lines of the board to the 4 programming lines of Tides (```RESET```, ```SWDIO```, ```SWCLK``` and ```GND```).

Make sure that the programming interface (```PGM_INTERFACE``` variable) is set to ```stlink-v2``` in ```stmlib/markefile.inc```.

Upload the firmware with:

```
make -f tides2/makefile upload
```

Note that this will destroy the factory calibration data.

### DAC Calibration

With a factory-made module, it is recommended to backup and restore the data originally written in the Flash memory of the module in range ```0x08004000:0x08008000``` (factory calibration data).

In a DIY situation, the most direct way of calibrating the module is to initialize the structure storing the calibration data to the correct values, deduced by measurement. Modify ```Settings::Init()``` to include the following lines:

```
persistent_data_.dac_calibration[0].offset = 32768.0f /* FIXME */;
persistent_data_.dac_calibration[0].scale = -4032.9f /* FIXME */;
// SNIP SNIP
persistent_data_.dac_calibration[3].offset = 32768.0f /* FIXME */;
persistent_data_.dac_calibration[3].scale = -4032.9f /* FIXME */;
```
