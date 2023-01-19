Threads, processes, RTOS? No.

Just one or two interrupts and `main()`.

### Highest priority: DSP

A high-priority ISR tied to the DAC/Codec's DMA is responsible for processing audio. In that ISR, CVs and Gate inputs are also read, once per block of audio samples. The "control rate" is thus defined by the buffer size. It is typically 1, 2 or 4kHz.

### Lower priority: UI Polling (optional)

A lower priority handler polls the switches and refreshes the LEDs. When a button press is detected, an event is shoved in a FIFO.

In terms of code organization, `Ui::Poll()` contains all the code that is called on a regular basis to scan the state of buttons, encoders (and sometimes pots) and transmit information to the LEDs and displays.

Who calls `Ui::Poll()`? Either the interrupt handler for a 1kHz timer, called `SysTick_Handler()`, and defined in the main module file (for example, check [`rings.cc`](https://github.com/pichenettes/modules/blob/master/rings/rings.cc#L68)). This low level timer is a facility offered by all ARM CPUs - and is often used by RTOS for context switching. Or the caller of `Ui::Poll()` is the interrupt handler that is invoked whenever a buffer of samples is ready to written to/read from the codec. That is the case in `warps.cc`, check its `FillBuffer` [routine]( https://github.com/pichenettes/modules/blob/master/warps/warps.cc#L63).

In any case, since it is called 1000 times a second, it shouldn't attempt to do anything that takes more than 1ms. Otherwise... stack overflow (if it always takes more than its allotted time) or audio glitches (if it occasionally takes more than expected)! 

So whenever we detect a button press in `Ui::Poll()`, we just shove an event in a message queue (the `Event` object stores everything we need to know: what kind of control? which control? for how long has it been pressed?) and we just get out. We don't attempt to do the actual thing the button press should do (like changing the state of the module, saving things, sending a SysEx over MIDI...), because it might actually take more than 1ms to do this thing!

### Actual handling of UI actions

At the lowest level of priority, the `main()` loop checks the FIFO for events, and handle them.

Practically, whenever the processor is done with `FillBuffer()` (which contains the bulk of the audio processing code), and more generally whenever there is no interrupt to service, it returns back in the main loop.

One can think of this loop as what the CPU is left to do whenever all the higher priority stuff is done (LED blinking? buttons checked? codec fed with audio? now let's check for UI stuff to do!). This is in this loop that we call `ui.DoEvents()`. It checks for unread messages in the event loop, and call the required methods to handle button presses or other types of events. With this organization of the execution time, the code handling the button presses is free to take as long as it needs: if necessary, it might be interrupted multiple times whenever something more useful needs to be done (like feeding audio to the codec).

### Debate: polling vs interrupts for UI stuff

This code organization is not the only possible approach. For example, it is possible to ask the MCU to trigger an interrupt whenever there's a change on the voltage at the pin hooked to the button. So we don't have to check the state of the button continuously - we only get a notification when its state changes. Sounds cool, why am I not using that?

1. Easy debouncing. My preferred debouncing technique (shove the read bits in a shift register, wait for a full string of 0s or 1s to decide that the button has reached a stable state) works better when the button is read continuously. Debouncing in ISRs... you need to start measuring intervals between calls to the ISR...
2. Habit from desktop synth projects with many buttons, in which the buttons are all read by shift registers. In this case case the state of all buttons is streamed bit by bit to a single CPU pin, and a voltage change at this pin doesn't mean anything about the state of a specific button.
3. There are sometimes limitations on the number of interrupts one can subscribe to, or on the MCU pins that can receive interrupts. Not relying on interrupts gives me more freedom for deciding which signal is connected to what. Since buttons are not critical traces (low speed signals, no special function required), they tend to be assigned to whatever pins are left after all the important peripherals are hooked to the processor.
4. When stuff happens in too many interrupt handlers, it gets hard to troubleshoot audio glitches/unregistered button presses. On all my modules, I can assume that `Ui::Poll()` runs in constant time and uses about 0.2% of the CPU. Then the only thing I have to check for is that `FillBuffer()` runs in less than 99% of its allotted time, and we're left with a tiny bit of CPU for `Ui::DoEvents()`. You can often find something called `PROFILE_INTERRUPT` in my projects. When enabled, this toggles a processor pin at the beginning of `FillBuffer()` and at its end. I can monitor this pin with my scope, the period of the waveform is the latency (buffer duration), and the pulse width gives me the CPU time consumed by the audio processing code. I can zoom on the falling edge and see if there are combinations of settings that increase the CPU use.
