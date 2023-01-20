## SPI

(For example to write to a DAC482x, used on most AVR projects).

### With a dedicated SPI peripheral (ATMega328p)

You just configure the SPI device, write a byte to the data register and it'll be shifted out in the background.

To write a word, you have two strategies:
1. [write a byte](https://github.com/pichenettes/anushri/blob/master/anu/anu.cc#L91), [wait for the transmission bit to be cleared](https://github.com/pichenettes/avril/blob/276b2887e4110ca913294fcbb313163dfb28a448/spi.h#L114), write the second byte (as done on the Anushri).
2. Write a byte, do some other stuff that takes more clock cycles than shifting the first byte (blinking some status LEDs or doing some arithmetic to figure out the next byte to write), write the second byte. This is how things are done on the [Shruthi-1 DSP board](https://github.com/pichenettes/shruthi-1/blob/master/dsp/dsp.cc#L80).

Note that on the Atmega328p, [the USART can also be configured for SPI transmission](https://github.com/pichenettes/avril/blob/276b2887e4110ca913294fcbb313163dfb28a448/spi.h#L249). The advantage is that it has a two-byte buffer! So to write two bytes, you write the first byte in the data register, it's immediately copied to the transmission buffer and transmission starts, [then you can write immediately a second byte in the data register](https://github.com/pichenettes/ambika/blob/master/voicecard/voicecard.cc#L79) that will be sent later. This is how I do things on the Ambika voicecards.

### Without a dedicated SPI peripheral

From the CVPal code:

	  enum UsiFlags {
	    TICK = _BV(USIWM0) | _BV(USITC),
	    TOCK = _BV(USIWM0) | _BV(USITC) | _BV(USICLK)
	  };

	  static inline void SpiSend(uint16_t word) {
	    USIDR = word >> 8;

	    USICR = TICK;
	    USICR = TOCK;
	    USICR = TICK;
	    USICR = TOCK;
	    USICR = TICK;
	    USICR = TOCK;
	    USICR = TICK;
	    USICR = TOCK;
	    USICR = TICK;
	    USICR = TOCK;
	    USICR = TICK;
	    USICR = TOCK;
	    USICR = TICK;
	    USICR = TOCK;
	    USICR = TICK;
	    USICR = TOCK;

	    USIDR = word & 0xff;

	    USICR = TICK;
	    USICR = TOCK;
	    USICR = TICK;
	    USICR = TOCK;
	    USICR = TICK;
	    USICR = TOCK;
	    USICR = TICK;
	    USICR = TOCK;
	    USICR = TICK;
	    USICR = TOCK;
	    USICR = TICK;
	    USICR = TOCK;
	    USICR = TICK;
	    USICR = TOCK;
	    USICR = TICK;
	    USICR = TOCK;
	  }

[Datasheet](http://ww1.microchip.com/downloads/en/devicedoc/Atmel-7701_Automotive-Microcontrollers-ATtiny24-44-84_Datasheet.pdf)

Starting from page 105, especially pages 112 to 114.

The USI is a low-level hardware block that can be used for serial data transfer (SPI or I2C). Contrary to the SPI or I2C devices found on more expensive MCUs that are "set and forget" (you write your data in a register and then the MCU shifts it in the background), you still have to do a lot of stuff manually in software. The USI can only do basic things like shifting bits in the data register, toggling clock pins, and setting I/O ports according to a bit of the data register or the state of the clock.

The **USIDR** register contains the byte to be output.

Then you manually control the **USICR** register to toggle the clock pin (TICK), then toggle the clock pin back and shift a data bit (TOCK).

So what the code really does is:

		Write a byte in the data register
		Toggle the clock
		Toggle back the clock, output a bit of the data register, shift the data register
		...
		8 times
		Write a second byte in the data register
		Toggle the clock
		Toggle back the clock, output a bit of the data register, shift the data register
		...
		8 times

A lot of tedious work, but still less work than a software implementation of SPI!

## Shruthi/Ambika button and LED I/O

Both use:

* One (or several daisy-chained) 595 serial to parallel converter driving all the LEDs.
* One (or several daisy-chained) 165 parallel to serial converter to read the switches.

These are connected to the `MISO`, `MOSI`, `SCK` line of the AVR's SPI peripheral, and an additional GPIO output, labelled `IO_ENABLE`.

When I want to read the 165s:

* I strobe the `IO_ENABLE` line (quickly set it from high to low back to high). This causes the 165 to load its data.
* I do one SPI read per byte of data to be read. The SPI hardware correctly shifts the data in on the `MISO` and `SCK`lines. Whatever happens with `MOSI` is ignored by the 595 because the `IO_ENABLE` (connected to its `RCLK` pin) remains high during the transfer.

When I want to write to the 595s:

* I set `IO_ENABLE` low.
* I do my writes. The SPI hardware shifts things out on the `MOSI` and `SCK` lines. Because `IO_ENABLE` is low, the 165 won't read and shift any data.
* I set `IO_ENABLE` high.
