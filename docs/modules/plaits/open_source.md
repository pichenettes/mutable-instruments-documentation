[Github repository](https://github.com/pichenettes/eurorack/tree/master/plaits)

[Schematics](downloads/plaits_v50.pdf)

## Firmware hacking

Plaits' source code is available under the MIT licence.

The code (along with the hardware description files) can be found in the `plaits` directory in our [Eurorack modules git repository](https://github.com/pichenettes/eurorack).

After having cloned the repository, don't forget to run `git submodule init && git submodule update` to make sure the sub-projects referenced in the code are also pulled.

### Toolchain

If you don't mind installing [Vagrant](https://www.vagrantup.com/) and [VirtualBox](https://www.virtualbox.org/wiki/Downloads) a [cozy environment](https://github.com/pichenettes/mutable-dev-environment) for firmware hacking is available.

If you want to set up your own environment to build Plaits' code, an ARM EABI toolchain must be installed. Because of tight CPU and code size limits, we recommend you to use the same compiler version as we do: `4.8-2013-q4-major`. Various pre-compiled binaries and source packages are available [here](https://launchpad.net/gcc-arm-embedded/4.8/4.8-2013-q4-major/).

The path to the toolchain binaries must be specified in the `TOOLCHAIN_PATH` variable in `plaits/makefile`.

To build the bootloader, use the following command:

```
make -f plaits/bootloader/makefile hex
```


To build the code, use the following command:

```
make -f plaits/makefile
```

If you modify lookup tables and want the big `resources.cc` file to be regenerated:

```
touch plaits/resources/resources.py && make -f plaits/makefile resources
```

### Firmware programming

The recommended solution for firmware programming is to use a ST Discovery board. Connect the 4 SWD lines of the board to the 4 programming lines of Plaits (```RESET```, ```SWDIO```, ```SWCLK``` and ```GND```).

Make sure that the programming interface (```PGM_INTERFACE``` variable) is set to ```stlink-v2``` in ```stmlib/markefile.inc```.

Upload the firmware with:

```
make -f plaits/makefile upload
```
