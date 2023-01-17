![](images/front_small.jpg)

[TOC]

## Key data

*Resonator*

Parameter    | Value
-------------|------
Width        | 14HP
Depth        | 25mm
+12V current | 110mA
-12V current | 5mA
Lifetime     | 12/15 to 04/22
Modulargrid  | [Link](https://www.modulargrid.net/e/mutable-instruments-rings)
Processor    | STM32F405RGT6 @ 168 MHz
Codec        | WM8731

## Original printed manual

[PDF download](downloads/rings_quickstart.pdf)

## Features

### Three resonator models

* Modal resonator, as used in Elements.
* Sympathetic strings, modelled by a network of comb filters.
* String with non-linearity/dispersion (comb filter with multimode filter and non-linearities in the loop).

All resonator models can be used polyphonically, with 1, 2 or 4 polyphony voices.

### Voltage-controlled resonator

Each model provides a unified set of parameters, all voltage controlled, with an attenuverter:

* **Structure**. With the modal and non-linear string models, controls the inharmonicity of the spectrum (which directly impacts the perceived "material"). With the sympathetic strings model, controls the intervals between strings.
* **Brightness**. Specifies the brightness and richness of the spectrum.
* **Damping**. Controls the damping rate of the sound, from 100ms to 10s.
* **Position**. Specifies at which point the structure is excited.

### Easy to patch

* Audio input accepting modular level signals, up to 16 Vpp.
* Two audio outputs, either splitting odd/even partials in monophonic mode, or even/odd notes in polyphonic mode.
* Voltage change detector on the V/O input, allocating a new polyphony voice each time a different note is played.
* Internal exciter triggered by the **STRUM** input (filtered pulse or noise burst), allowing the module to be used without an external excitation source.

### Specifications

* Trigger input: 100k impedance, 0.6V threshold.
* CV inputs: 100k impedance, +/- 8V, 12-bit 2kHz.
* Audio I/O: 16-bit, 48kHz.
* Internal processing: 32-bit floating point.
