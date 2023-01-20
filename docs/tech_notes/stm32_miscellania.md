## History time: why STM32 chips?

### Why not DSPs?

* DSPs require proprietary, often expensive, development tools running on Windows.
* DSPs often require external memory chips.
* DSPs often come in large packages.
* The code written for DSPs is not portable and is often write only. The development cycle (prototype in a higher level language, then port to assembly, then it's over) is not my preferred way of working because I like revising ideas, and running the code on my development machine to experiment with it rather than on the embedded target. Think of it: VCV Rack wouldn't have been possible if Rings ran DSP code.

### Why ARM MCUs?

There are open source development tools like openOCD and gcc. The fact that ARM is the most popular platform for smartphones and IoT devices means that there is a large community, including employees from large companies, maintaining and improving gcc for the ARM target, and that the generated code and the optimizations are good.

### Why STM32?

At the time I got into it in 2012, there were two choices: STM32F and NXP LPC (I'm not even sure Freescale Kinetis was available, but if it was it wasn't very visible). STM32 had better support from the open source tools, and more importantly the embedded systems class at my alma mater used them. I could get access to their course material, so that's what I went with.

In a nutshell, it could be summarized by the fact that I come from a software development background using UNIX command line tools, and that I wanted to keep a similar workflow when developing my modules. If I had come from an embedded systems or more generally EE background, I wouldn't have shied away from a proprietary IDE running on Windows and might have gone for something else.

Another large DIY project, MIDIBox, was mostly STM32F at the time; and with the popularity of Mutable Instruments and the availability of the schematics, code and makefiles, I suspect this might have influenced many aspiring developers to get into this platform in the following years.

### Which MCUs?

There are three "classes" in terms of CPU use, and Peaks and Plaits are in the same:

* Heavy: Beads (STM32H7, 480 MHz)
* Medium: Rings, Warps, Clouds, Elements, Marbles (STM32F405, 168 MHz)
* Light: Peaks, Yarns, Tides, Frames (STM32F10x, 72 MHz, integer math), Plaits, Stages, Tides 2018 (STM32F37x, 72 MHz, floating point math)

Pretty much all modules are designed to use 80 to 95% of the available computing power on the chip. I tweak the sample rate and/or adjust the complexity of the code (order of interpolations, number of voices, that kind of things...) to reach that goal.

In 2021 I would have probably used the STM32G4 line for all modules other than Beads! But then there is... chronology. When I designed Rings in 2015, I had no idea a better choice of MCU would come 4 years later (and if I knew about it, would I have waited for it to be available)?

There was no other choice than the F4 at that time. But how awful the ADCs are on that MCU! You need a lot of post-processing to get a stable and accurate reading.

Then ST launched the F37x line. This was an interesting proposition: you get less CPU power than the F4, but excellent ADCs. The F37x looked like the perfect choice to upgrade modules which were not inherently CPU hungry: Braids (-> Plaits), Peaks (-> Stages), and Tides.

With the G4 line, there is no longer any compromise to do on CPU vs ADC precision. And given that they are available in 48 pin packages, they are suitable for small modules (like Plaits).

## FLASH layout and bootloader

The bootloader is compiled and linked with a linkerscript that places the interrupt vector and code at [0x08000000](https://github.com/pichenettes/stmlib/blob/f8a4b46b7abb5d752a6a72932eeac1a03fe958fd/linker_scripts/stm32f10x_flash_cl.ld#L7) (beginning of flash).

The application code is compiled and linked with a different linkerscript that places the interrupt vector and code at [0x08004000](https://github.com/pichenettes/stmlib/blob/f8a4b46b7abb5d752a6a72932eeac1a03fe958fd/linker_scripts/stm32f10x_flash_cl_application.ld#L7) (16k after the beginning of the flash)

Both blocks of code are converted to .hex files and [merged together](https://github.com/pichenettes/stmlib/blob/f8a4b46b7abb5d752a6a72932eeac1a03fe958fd/makefile.inc#L327) in a single .hex file - so effectively the first 16k of flash contains the bootloader, and the rest the application code.

When the module is powered on, the processor boots with the interrupt vector and code at 0x08000000, so it starts running the [bootloader code](https://github.com/pichenettes/eurorack/blob/master/braids/bootloader/bootloader.cc#L172). It either stays there if the encoder is pressed; or it leaves the `white (!exit_updater)` loop. In which case, the [processor is reset and the program counter is forced to address 0x08004000](https://github.com/pichenettes/eurorack/blob/master/braids/bootloader/bootloader.cc#L228), so it jumps to the main [application code](https://github.com/pichenettes/eurorack/blob/master/braids/braids.cc#L299). One of the very first things done in the application code is [to set the interrupt vector table to 0x4000](https://github.com/pichenettes/eurorack/blob/master/braids/drivers/system.cc#L36) - so that it doesn't use the interrupt table at the beginning of the flash.

## Why Plaits does not run at 48kHz exactly

PLL setting: 9/47.

Actual sample rate: 8000000 x 9/47 / 16 / 2 / 48000 = 47872.34 Hz

## Low-level pin access

		// For F1, F3, G4
		GPIOA->BSRR = GPIO_Pin_0; // Set Pin 0 of GPIOA high
		GPIOA->BRR = GPIO_Pin_0; // Set Pin 0 of GPIOA low

		// For F4
		GPIOA->BSRRL = GPIO_Pin_0; // Set Pin 0 of GPIOA high
		GPIOA->BSRRH = GPIO_Pin_0; // Set Pin 0 of GPIOA low

		// For H7
		GPIOA->BSRR = GPIO_Pin_0; // Set Pin 0 of GPIOA high
		GPIOA->BSRR = GPIO_Pin_0 << 16U; // Set Pin 0 of GPIOA low

## Why no equivalent of avril for STM32?

`avril` was Mutable Instruments' hardware abstraction library for the AVR family, heavily based on C++ templates.

When I started programming on AVR, the two alternatives were either direct register writes (unreadable without the AVR datasheet) or the Arduino libraries (poorly designed, run-time abstractions with huge overhead for every call). I really felt the need for something that would allow me to write readable/reusable code... that could be compiled down to a single machine instruction. It was very important because I knew the projects I was working on were pushing the processor to its limits in terms of CPU use and code size. I also knew I would frequently have to do bit-banging.

The need for a such a library was less pressing when I moved to STM32F. First, the "firmware library" from ST covered relatively well the need for an abstraction layer above raw register writes. It's not very efficient (overhead of function calls), but I don't need it to be very efficient because most I/O occurs at a low rate (usually 1kHz), and the stuff that occurs at a higher rate is usually controlled by the hardware (DMA, specialized peripherals...). Also, the chips are more generous in terms of flash size, so less efficient code is tolerated. And another thing I learnt from my AVR days: all projects have something unique on the hardware side that makes it a bit futile to write a reusable library above the GPIO level.

There are still places where I use templates in the new projects, though!

For example, Clouds' [grain rendering algorithm](https://github.com/pichenettes/eurorack/blob/master/clouds/dsp/grain.h#L120) is specialized for each combinations of channel \#, audio quality, and sample interpolation method - removing as much tests and branching from the loop, and keeping the inside of the loop small enough for the compiler to unroll.

Another example is the [multimode filter](https://github.com/pichenettes/stmlib/blob/master/dsp/filter.h#L190) used in Clouds and Elements, in which the filter type is a compile-time parameter.

And Warps uses template-based polyphase FIR resamplers - the templates are used to unroll pretty much all the code at compile-time to make it very efficient.

The idea is that if a function `f(argument, parameter)` is called for only a few values of the parameter known at run-time, it might help performance (at the expense of code size) to create for each used value of the parameter a `f(argument)` variant in which the parameter is a known constant, and everything depending on the parameter can be pre-computed and assumed constant too (including all branches depending on the parameter being straightened). In other words: split the arguments of the function between “data” arguments and “configuration” arguments, and partially evaluate the “configuration” arguments at compile-time.
