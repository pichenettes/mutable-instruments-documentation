[Github repository](https://github.com/pichenettes/eurorack/tree/master/yarns)

[Schematics](downloads/yarns_v03.pdf)

## Firmware hacking

Yarns' source code is available under the MIT licence.

The code (along with the hardware description files) can be found in the `yarns` directory in our [Eurorack modules git repository](https://github.com/pichenettes/eurorack).

After having cloned the repository, don't forget to run `git submodule init && git submodule update` to make sure the sub-projects referenced in the code are also pulled.

### Toolchain

If you don't mind installing [Vagrant](https://www.vagrantup.com/) and [VirtualBox](https://www.virtualbox.org/wiki/Downloads) a [cozy environment](https://github.com/pichenettes/mutable-dev-environment) for firmware hacking is available.

If you want to set up your own environment to build Yarns' code, an ARM EABI toolchain must be installed. Because of tight CPU and code size limits, we recommend you to use the same compiler version as we do: `4.8-2013-q4-major`. Various pre-compiled binaries and source packages are available [here](https://launchpad.net/gcc-arm-embedded/4.8/4.8-2013-q4-major/).

The path to the toolchain binaries must be specified in the `TOOLCHAIN_PATH` variable in `yarns/makefile`.

To build the bootloader, use the following command:

```
make -f yarns/bootloader/makefile hex
```


To build the code, use the following command:

```
make -f yarns/makefile
```

If you modify lookup tables and want the big `resources.cc` file to be regenerated:

```
touch yarns/resources/resources.py && make -f yarns/makefile resources
```

### Firmware programming

A first solution is to simply use the firmware update procedure. A .syx file for firmware upgrade can be generated with:

```
make -f yarns/makefile syx
```

The firmware can then be loaded into the module using the procedure described in the manual.

**Past this point, we assume you know what you are doing and we are not responsible for any damage to your module!**

The recommended solution for firmware programming is to use a JTAG interface and openOCD. We recommend Olimex' [ARM-USB-OCD-H devices](https://www.olimex.com/Products/ARM/JTAG/ARM-USB-OCD-H/). An adapter must also be purchased for the [mini-JTAG](http://www.embeddedartists.com/products/acc/acc_jtag_adapter_kit.php) connector used by Yarns.

Upload the firmware with:

```
make -f yarns/makefile upload_combo_jtag_no_erase
```

Note that the `upload_combo_jtag_no_erase` command is used to prevent the FLASH to be entirely erased at each update – keeping the precious calibration data in place!
