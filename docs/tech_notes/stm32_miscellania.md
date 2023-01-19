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

## STM32F37x I2S at 48kHz with a 8 MHz crystal

PLL setting: 9/47.

Actual sample rate: 8e6 x 9/47 / 16 / 2 / 48000 = 47872.34 Hz

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
