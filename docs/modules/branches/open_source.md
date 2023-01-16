[Github repository](https://github.com/pichenettes/eurorack/tree/master/branches)

[Schematics](downloads/branches_v40.pdf)

## Firmware hacking

Branches' source code is available under the GPL licence.

The code (along with the hardware description files) can be found in the `branches` directory in our [Eurorack modules git repository](https://github.com/pichenettes/eurorack).

After having cloned the repository, don't forget to run `git submodule init && git submodule update` to make sure the sub-projects referenced in the code are also pulled.

### Toolchain

If you don't mind installing [Vagrant](https://www.vagrantup.com/) and [VirtualBox](https://www.virtualbox.org/wiki/Downloads) a [cozy environment](https://github.com/pichenettes/mutable-dev-environment) for firmware hacking is available.

If you want to set up your own environment to build Branches' code, avr-gcc and avrdude must be installed. These are standard packages on Linux. On OS X, [Crosspack](http://www.obdev.at/products/crosspack/download.html) can be installed.

The path to the toolchain can be edited in the `AVRLIB_TOOLS_PATH` variable in `avrlib/makefile.mk` directory. It might also be necessary to change the ISP programmer name in the `PROGRAMMER` variable.

To build the code, use the following command:

```
make -f branches/makefile
```

### Firmware programming

Programming must be done with an AVR ISP programmer. These are very common and can be found for a few dollars - however, the most reliable units are Atmel's own AVR ISP mkII.

The ISP programmer must be connected on the back of the module - red stripe of the cable on the same side as the ISP text on the board. To upload the code:

```
make -f branches/makefile bootstrap_all
```

