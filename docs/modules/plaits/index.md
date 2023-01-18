![](images/front_small.jpg)

[TOC]

## Key data

*Macro-oscillator*

Parameter    | Value
-------------|------
Width        | 12HP
Depth        | 25mm
+12V current | 50mA
-12V current | 5mA
Lifetime     | 03/18 to 11/22
Modulargrid  | [Link](https://www.modulargrid.net/e/mutable-instruments-plaits)
Processor    | STM32F373CCT6 @ 72 MHz
DAC          | PCM5100A

## Original printed manual

[PDF download](downloads/plaits_quickstart.pdf)

## Features

### 16 deep synthesis models

#### 8 synthesis models for pitched sounds

* Two detuned virtual analog oscillators with continuously variable waveforms.
* Variable slope triangle oscillator processed by a waveshaper and wavefolder.
* 2-operator FM with continuously variable feedback path.
* Two independently controllable formants modulated by a variable shape window (VOSIM, Pulsar, Grainlet, Casio CZ-style resonant filter...).
* 24-harmonic additive oscillator.
* Wavetable oscillator with four banks of 8x8 waves, with or without interpolation.
* Chord generator, with divide down string/organ emulation or wavetables.
* A collection of speech synthesis algorithms (formant filter, SAM, LPC), with phoneme control and formant shifting. Several banks of phonemes or segments of words are available.

#### 8 synthesis models for noise and percussions

* Granular sawtooth or sine oscillator, with variable grain density, duration and frequency randomization.
* Clocked noise processed by a variable shape resonant filter.
* 8 layers of dust/particle noise processed by resonators or all-pass filters.
* Extended Karplus-Strong (aka Rings' red mode), excited by bursts of white noise or dust noise.
* Modal resonator (aka Rings' green mode), excited by a mallet or dust noise.
* Analog kick drum emulation (two flavors).
* Analog snare drum emulation (two flavors).
* Analog high-hat emulation (two flavors).

#### Dual output

* The **AUX** output carries a variant, sidekick or by-product of the main signal.
* Patch both **OUT** and **AUX** to [Warps](../warps) for weird hybridization experiments!

### Internal or external modulations

* Dedicated CV input for synthesis model selection. No need to activate a mysterious `META` mode!
* An internal decay envelope is normalled to the **TIMBRE**, **FM** and **MORPH** CV inputs: the amount of internal modulation is directly adjusted with the attenuverters.

### Internal low-pass gate (LPG)

* Dedicated **LEVEL** CV input controlling the amplitude and brightness of the output signal.
* The internal LPG can also be directly plucked by the trigger input.
* Two parameters of the LPG can be adjusted: amount of low-pass filtering (VCFA to VCA), and response time of the virtual vactrol.

### Specifications

* All inputs: 100k impedance, DC to 2kHz.
* **FM**, **MORPH**, **TIMBRE** input range: +/- 8V.
* **HARMO** and **MODEL** input range: +/- 5V.
* **LEVEL** and **TRIG** input range: 0 to +8V.
* **V/OCT** input range: -3 to +7V.
* 16-bit CV capture.
* Audio output: 48kHz, 16-bit, DC-coupled.
* Internal processing: 32-bit floating point.

### Plaits vs Braids?

Plaits shares no hardware or software with Braids!

* Floating-point DSP code written from scratch or inherited from Elements, Warps and Rings.
* Band-limited synthesis used almost everywhere, producing aliasing-free results at the base sample rate of 48kHz (Braids ran at 96kHz and used naive oversampling).
* High-quality Sigma-Delta ADC for CV acquisition, approaching a resolution of 16-bit (12-bit for Braids).
* ADC reads are interpolated in software to eliminate zipper noise.
* DC-coupled audio output, extending the range of the module to very low frequencies (Braids could not reach LFO ranges).
* Lean hardware design: less HP, mA and €.
