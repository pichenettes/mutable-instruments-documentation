[Github repository](https://github.com/pichenettes/eurorack/tree/master/stages)

[Schematics](downloads/stages_v70.pdf)

## Firmware hacking

Stages' source code is available under the MIT licence.

The code (along with the hardware description files) can be found in the `stages` directory in our [Eurorack modules git repository](https://github.com/pichenettes/eurorack).

After having cloned the repository, don't forget to run `git submodule init && git submodule update` to make sure the sub-projects referenced in the code are also pulled.

### Toolchain

If you don't mind installing [Vagrant](https://www.vagrantup.com/) and [VirtualBox](https://www.virtualbox.org/wiki/Downloads) a [cozy environment](https://github.com/pichenettes/mutable-dev-environment) for firmware hacking is available.

If you want to set up your own environment to build Stages' code, an ARM EABI toolchain must be installed. Because of tight CPU and code size limits, we recommend you to use the same compiler version as we do: `4.8-2013-q4-major`. Various pre-compiled binaries and source packages are available [here](https://launchpad.net/gcc-arm-embedded/4.8/4.8-2013-q4-major/).

The path to the toolchain binaries must be specified in the `TOOLCHAIN_PATH` variable in `stages/makefile`.

To build the bootloader, use the following command:

```
make -f stages/bootloader/makefile hex
```


To build the code, use the following command:

```
make -f stages/makefile
```

If you modify lookup tables and want the big `resources.cc` file to be regenerated:

```
touch stages/resources/resources.py && make -f stages/makefile resources
```

### Firmware programming

The recommended solution for firmware programming is to use a ST Discovery board. Connect the 4 SWD lines of the board to the 4 programming lines of Stages (```RESET```, ```SWDIO```, ```SWCLK``` and ```GND```).

Make sure that the programming interface (```PGM_INTERFACE``` variable) is set to ```stlink-v2``` in ```stmlib/markefile.inc```.

Upload the firmware with:

```
make -f stages/makefile upload
```

Note that this will destroy the factory calibration data.

### Calibration

With a factory-made module, it is recommended to backup and restore the data originally written in the Flash memory of the module in range ```0x08004000:0x08008000``` (factory calibration data).

In a DIY situation, the most direct way of calibrating the module is to initialize the structure storing the calibration data to the correct values, deduced by measurement. Modify ```Settings::Init()``` to include the following lines:

```
persistent_data_.channel_calibration_data[0].dac_offset = 32768.0f /* FIXME */;
persistent_data_.channel_calibration_data[0].dac_scale = -32263.0f /* FIXME */;
persistent_data_.channel_calibration_data[0].adc_offset = 0.0f /* FIXME */;
persistent_data_.channel_calibration_data[0].adc_scale = -1.0f /* FIXME */;
// SNIP SNIP
persistent_data_.channel_calibration_data[5].dac_offset = 32768.0f /* FIXME */;
persistent_data_.channel_calibration_data[5].dac_scale = -32263.0f /* FIXME */;
persistent_data_.channel_calibration_data[5].adc_offset = 0.0f /* FIXME */;
persistent_data_.channel_calibration_data[5].adc_scale = -1.0f /* FIXME */;
```
## About the serial connection

Which information transits on the serial link connecting two Stages modules?

    struct LeftToRightPacket {
      uint8_t last_patched_channel;
      int8_t segment;
      float phase;
      Loop last_loop;
      ChannelBitmask switch_pressed[kMaxChainSize];
      ChannelBitmask input_patched[kMaxChainSize];
    };
  
    struct RightToLeftPacket {
      ChannelState channel[kNumChannels];
    };
  
    enum Request {
      REQUEST_NONE,
      REQUEST_SET_SEGMENT_TYPE = 0xfe,
      REQUEST_SET_LOOP = 0xff
    };
  
    struct RequestPacket {
      uint8_t request;
      uint8_t argument[4];
    };
  
    struct DiscoveryPacket {
      uint32_t key;
      uint8_t counter;
    };
  
    union Packet {
      RightToLeftPacket to_left;
      LeftToRightPacket to_right;
      DiscoveryPacket discovery;
      RequestPacket request;
      uint8_t bytes[kPacketSize];
    };


* Information about which inputs have signals patched into them.
* Current active segment and envelope phase (if an envelope spans two modules, the module on the left is in charge of generating it, but the module on the right must still generate the segment activity signals for its 6 outputs).
* Loop points.
* Information about which switches are pressed (to handle the creation of a loop spanning several modules).
* Channel state (position of the pot, of the slider, value of the CV input).
* Notifications that the user is trying to perform some action, so that it can be vetted by a module (eg: trying to create a loop between module 1 and 3 while there's a jack patched in module 2).
* Neighbor discovery messages (when the module is powered on, it searches for neighbors).
