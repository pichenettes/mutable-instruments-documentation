![](images/front_small.jpg)

[TOC]

## Key data

*Meta-modulator*

Parameter    | Value
-------------|------
Width        | 10HP
Depth        | 25mm
+12V current | 110mA
-12V current | 5mA
Lifetime     | 11/15 to 03/22
Modulargrid  | [Link](https://www.modulargrid.net/e/mutable-instruments-warps)
Processor    | STM32F405RGT6 @ 168 MHz
Codec        | WM8731

## Original printed manual

[PDF download](downloads/warps_quickstart.pdf)

## Features

### 7 signal hybridization algorithms

* Crossfade.
* Cross-folding.
* Digital model of an analog diode ring-modulator.
* Digital ring-modulation.
* Bitwise XOR modulation.
* Octaver/comparator.
* 20 band-vocoder.

### Everything under CV control

* CV control of modulation algorithm selection, with crossfading between adjacent algorithms.
* CV control of each input's amplitude, with emulated analog saturation.
* V/O CV control of the internal oscillator (when enabled).
* For each algorithm, CV control of timbre richness/brightness/distortion.

### Built in carrier oscillator

Audio input 1 can be replaced by an internal digital oscillator with through-zero FM.

Available waveforms: sine, triangle, sawtooth, pulse, filtered noise.

### Specifications

* Input impedances: 100k.
* Audio inputs and outputs: 16-bit, 96kHz.
* CV inputs: 12-bit, 1.6kHz.
* Internal processing: 32-bit floating point, 576kHz (32kHz for the vocoder).
