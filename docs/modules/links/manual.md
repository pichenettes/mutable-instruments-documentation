## Installation

Links requires a **-12V/+12V** power supply (2x5 pin connector). The ribbon cable connector must be aligned so that the red stripe of the ribbon cable (-12V) is on the same side of the module's power header as the "Red stripe" marking on the board. The module draws 15mA from both the +12V and -12V supply rails.

![](images/manual.png)

## 1:3 section

This section of the module is a buffered multiple. The signal patched to the **IN** input is duplicated, without voltage drop, to each of the three **OUT** outputs.

This section can be used to send the note CV from a keyboard, sequencer or MIDI interface to several VCOs; but it can also distribute an audio signal or modulation to several modules.

## 2:2 section

This section acts as a precision adder/mixer and as a multiple. The two signals patched in the **IN** inputs are added, and the result is made available on each of the two **OUT** outputs.

It is of course possible to use this section as a 1:2 multiple - when only one of the **IN** inputs is left unpatched.

Because this section uses a precision adder circuit, it is possible to use it to transpose a note CV: for example, patch the CV output of a sequencer to the first input, and the CV output of a keyboard to the second input. Patch the output(s) to (a) VCO(s). With this patch, the sequence is transposed by the keyboard.

Audio or modulation signals can also be mixed and/or multed by this section.

## 3:1 section

This section acts as an averager. The three signals sent to each of the **IN** inputs are mixed together, with a gain of 1/3 applied to the mix. It is particularly useful for mixing audio signals without causing clipping.
