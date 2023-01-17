![](images/front_small.jpg)

[TOC]

## Key data

*Multifunction gap-filler* (*Dual trigger to signal converter* on the packaging)

Parameter    | Value
-------------|------
Width        | 8HP
Depth        | 25mm
+12V current | 60mA
-12V current | 2mA
Lifetime     | 08/14 to 10/17
Modulargrid  | [Link](https://www.modulargrid.net/e/mutable-instruments-peaks)
Processor    | STM32F103CBT6 @ 72 MHz
DAC          | DAC8552

## Original printed manual

[PDF download](downloads/peaks_quickstart.pdf)

## Features

### Four in one...

#### ADSR envelope generator

* Segment durations ranging from 0.2ms to 8s.
* Quartic attack, exponential decay, exponential release.

#### LFO and tap-tempo LFO

* 0.03 Hz to 160 Hz.
* 5 basic waveforms: square, triangle, sine, stepped, random.
* Waveform variations and morphing for each of these: PWM, slope, folding/harmonics, step size, interpolation.
* Phase at reset control.
* Tap LFO can lock onto irregular rhythms.

#### Drum synth

* Channel 1: 808 kick model with extra parameters (tune, punch).
* Channel 2: 808 snare model with extra parameters (tune, decay).
* Channel 2: A specific combination of settings transform the snare into a modelled 808 hi-hat.

### Control modes

* **Twin**: Channel 1&2 share the same parameters but can be triggered independently.
* **Split**: Channel 1 is edited by knobs 1&2, channel 2 edited by knobs 3&4, with a simplified 2-parameter control scheme.
* **Expert**: Channel 1&2 are completely independent.

### Specifications

* Inputs: 100k impedance, threshold at 0.6V.
* 16-bit CV/audio generation with 48kHz sample rate.
* Output level: 0 to +8V for envelopes, 10Vpp for LFOs and drum signals.

## Revisions and variants

An initial batch of 250 units was made by a dubious CM in the US, only a small fraction of which has been sold. The next batches were made in France at the end of 2014.

### 2014

First batch made in France, labelled "v0.2":

* Green PCB (originally black).

### 2015

Hardware revision labelled "v3" with the following differences:

* Thonkiconn jacks.
* LEDs with round top (originally flat).
* Illuminated push-buttons sourced from RunRun (originally E-switch), with a more even illumination.
* No trimmers in the back, the calibration being now performed in software.
