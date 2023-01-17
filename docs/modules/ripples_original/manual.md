## Overview

Ripples is a voltage-controlled filter. It provides 3 output modes: 2-pole band-pass, 2-pole low-pass and 4-pole low-pass. The filtering is achieved by a high-quality quad VCA. An OTA in the filter feedback path brings the resonance under voltage control, and also serves as a soft-limiter to ensure a clean self oscillation. Finally, a VCA allows the level of the 4-pole low-pass output to be modulated by a CV. This makes Ripples a good "final stage" module for a subtractive synthesis patch.

The tonal character of the filter is inspired by japanese classics from the 80s.

## Installation

Ripples is designed for Eurorack synthesizer systems and occupies 8 HP of space. It requires a +/- 12V power supply, consuming 35mA from each supply rail. The red stripe of the ribbon cable must be oriented on the same side as the "Red stripe" marking on the printed circuit board.

## Controls

![](images/manual.png)

**A. Resonance level**. Self-oscillation occurs at about 80% of the course of this potentiometer.

**B. Cutoff** frequency.

**C. Frequency modulation attenuverter**. Adjusts the amount and polarity of frequency modulation from the FM input.

## Inputs and outputs

**1. RES**: Resonance control voltage, added to the value set by the RES knob. A control voltage of +4V starts bringing the filter to self-oscillation. Control voltages above +5V are compressed and only bring a moderate increase of the loudness of the self-oscillation tone.

**1. FREQ**: 1 V/Oct cutoff frequency control. This input is very helpful for getting the filter frequency to track the keyboard note. Notice that when resonance is set to its maximum value, with no input signal, Ripples behaves like a sine wave oscillator. The LP2 and BP2 outputs produce a slightly distorted sine-wave. The LP4 output produces a clean output.

**1. FM**: Cutoff frequency CV. This signal is attenuated and polarized by the corresponding knob.

**2. IN**: Signal input. This input is DC-coupled. However, the internal connection between the VCF output and the VCA stage is AC-coupled.

**3. GAIN**: VCA control voltage. This voltage controls the level of the signal produced at the LP4>VCA output. 0V silences the signal; 5V applies a unitary gain. Values above +5V are progressively compressed.

**4. Outputs**: 

* BP2 is a 2-pole band-pass output.
* LP2 is a 2-pole low-pass output.
* LP4 is a 4-pole low-pass output.
* LP4>VCA is the 4-pole low-pass output sent into a VCA.

## Calibration procedure

1.  Set the FREQ knob to 10 o'clock (exact position does not matter).
2.  Set the RES knob to its maximum position. Disconnect any input signal or FM modulation CV.
3.  Connect the note CV output of a well-calibrated keyboard interface or MIDI-CV converter to the FREQ input.
4.  Listen to the tone at the LP4 output.
5.  Adjust the trimmer resistor at the back of the circuit board until the musical intervals played on the keyboard are correctly reproduced (actual note values do not matter, but when playing an octave, it must sound like an octave).

The filter is designed to track well over 4 octaves, but is not temperature-stabilized.
