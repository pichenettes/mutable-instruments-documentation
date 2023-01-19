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
