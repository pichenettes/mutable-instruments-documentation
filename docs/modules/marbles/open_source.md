[Github repository](https://github.com/pichenettes/eurorack/tree/master/marbles)

[Schematics](downloads/marbles_v70.pdf)

## Firmware hacking

Marbles' source code is available under the MIT licence.

The code (along with the hardware description files) can be found in the `marbles` directory in our [Eurorack modules git repository](https://github.com/pichenettes/eurorack).

After having cloned the repository, don't forget to run `git submodule init && git submodule update` to make sure the sub-projects referenced in the code are also pulled.

### Toolchain

If you don't mind installing [Vagrant](https://www.vagrantup.com/) and [VirtualBox](https://www.virtualbox.org/wiki/Downloads) a [cozy environment](https://github.com/pichenettes/mutable-dev-environment) for firmware hacking is available.

If you want to set up your own environment to build Marbles' code, an ARM EABI toolchain must be installed. Because of tight CPU and code size limits, we recommend you to use the same compiler version as we do: `4.8-2013-q4-major`. Various pre-compiled binaries and source packages are available [here](https://launchpad.net/gcc-arm-embedded/4.8/4.8-2013-q4-major/).

The path to the toolchain binaries must be specified in the `TOOLCHAIN_PATH` variable in `marbles/makefile`.

To build the bootloader, use the following command:

```
make -f marbles/bootloader/makefile hex
```


To build the code, use the following command:

```
make -f marbles/makefile
```

If you modify lookup tables and want the big `resources.cc` file to be regenerated:

```
touch marbles/resources/resources.py && make -f marbles/makefile resources
```

### Firmware programming

The recommended solution for firmware programming is to use a ST Discovery board. Connect the 4 SWD lines of the board to the 4 programming lines of Marbles (```RESET```, ```SWDIO```, ```SWCLK``` and ```GND```).

Make sure that the programming interface (```PGM_INTERFACE``` variable) is set to ```stlink-v2``` in ```stmlib/makefile.inc```.

Upload the firmware with:

```
make -f marbles/makefile upload
```

Note that this will destroy the factory calibration data.

### DAC Calibration

With a factory-made module, it is recommended to backup and restore the data originally written in the Flash memory of the module in range ```0x08004000:0x08008000``` (factory calibration data).

In a DIY situation, the most direct way of calibrating the module is to initialize the structure storing the calibration data to the correct values, deduced by measurement. Modify ```Settings::Init()``` to include the following lines:

```
persistent_data_.calibration_data.dac_offset[0] = 32768.0f /* FIXME */;
persistent_data_.calibration_data.dac_scale[0] = -6212.8f /* FIXME */;
// SNIP SNIP
persistent_data_.calibration_data.dac_offset[3] = 32768.0f /* FIXME */;
persistent_data_.calibration_data.dac_scale[3] = -6212.8f /* FIXME */;
```

The correct values can be obtained by the following process:

* Flash the module with the default calibration values.
* For each of the 4 outputs, accurately measure the voltage produced when the module settings correspond to a 1V and 3V constant voltage (full range, SPREAD fully CCW, STEPS fully CW, BIAS adjusted for the correct octave).
* Use the [following script](https://colab.research.google.com/drive/15lgHfnonmF2f14j5YUasVgpaKPe6ov0F?usp=sharing) to do the math!
