![](images/front_small.jpg)

[TOC]

## Key data

*Mingling and Mangling*

Parameter    | Value
-------------|------
Width        | 4HP
Depth        | 25mm
+12V current | 25mA
-12V current | 25mA
Lifetime     | 03/16 to 05/21
Modulargrid  | [Link](https://www.modulargrid.net/e/mutable-instruments-kinks)

## Original printed manual

[PDF download](downloads/kinks_quickstart.pdf)

## Features

### Three essential utility functions

#### SIGN section

This section consists of a precision signal inverter, and half- and full- wave rectifiers (which respectively clips to 0V, and inverts the negative half of the signal).

They can help in creating new LFO shapes, add overtones to audio signals or even transform CV melodies.

#### LOGIC

An analog **OR** gate outputs the maximum of two signals: not only it can merge two streams of triggers/gates like its digital counterparts, but it can also hybridizes LFOs, envelopes or even audio signals – creating the same type of inharmonic side-bands as ring-modulators.

The analog **AND** gate outputs the minimum of the two signal. With it you can mute/un-mute a stream of triggers with another gate, or explore other shades of audio and CV mangling.

#### S&H

A no-nonsense implementation of a classic synthesizer circuit.

On each trigger received on the TRIG input, the output voltage takes the value of the input voltage and holds this voltage.

The signal input **IN** is normalized to a white noise generator – to easily generate stepped random values.

The white noise signal is also available through the **NOISE** output jack.

### Specifications

* All inputs DC-coupled.
* All circuits handle audio-rate signals.
* Input impedance: 66k for **RECTIFY**, 90k for **LOGIC**, 1M for **S&H**.
* S&H **TRIG** input edge detector: minimum pulse width: 25µs. Maximum slew rate: 0.2V/ms.
* S&H sampling time: 100µs.
* S&H droop rate: <0.8mV/s.
* Noise output: flat from 10 Hz to 30kHz (-6dB/octave rolloff beyond), standard deviation = 1.8V +/- 10%.

## Revisions and variants

80 units made in 2021 demonstrated out of specs performances for the S&H and noise section, presumably from a bad batch of transistors. They were sold at a discount when Mutable Instruments shut down.
