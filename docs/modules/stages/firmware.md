## Download

[Latest version (1.2)](downloads/stages_1.2.wav)

[Firmware update procedure](../manual#firmware)

## Revisions

### v1.2 (final)

#### Fixes and minor improvements

* The S&H applies a 2ms delay before sampling the CV, to play well with CV/Gate sources with a slewed or delayed CV signal.
* When using a clocked LFO segment, the edges of the LFO waveforms are aligned with the rising edge of the clock.
* The amount of hysteresis in the quantizer responsible for converting a voltage or slider position into discrete settings (eg: an LFO divider ratio, or a sequencer step address) has been lowered. This makes the relationship between a voltage (or position) and a specific setting more consistent when controlled by a sequencer.
* Taking inspiration from [qiemem's alternative firmware](https://github.com/qiemem/eurorack/tree/bipolar/stages), the **SHAPE/TIME** knob for a solo looping red segment controls the probability of accepting the gate gate.
* Taking inspiration from [qiemem's alternative firmware](https://github.com/qiemem/eurorack/tree/bipolar/stages), the module tracks any adjustment made to the level of the previous segment, instead of sampling it. This allows crossfading or pseudo-VCA applications.
* The clock tracking algorithm (for a solo looping green segment) reuses code from Tides 2018, and benefits from a few improvements, for instance no spurious reset when tracking very slow clocks.
* When the module operates as a harmonic oscillator, a mild low-pass filter is applied on the amplitude slider to reduce noise.
* The memory footprint has been kept under 24kb to prevent rare bugs occurring with modules built with the STM32F373RBT6 MCU, which has been used occasionally to finish produ. As a result, a slightly smaller buffer is used for the CV delay feature.

#### Extended sequencer mode

In order to access this extended sequencer, simply put a **HOLD** or **RAMP** segment at the beginning of your step sequence. For example, to create a 4-step sequence with extended control, create a **RAMP** **STEP** **STEP** **STEP** **STEP** group.

The first segment will not be part of your sequence, but will control the playback and the quantization:

The **SHAPE/TIME** pot of the first segment controls the playback mode:

* Up
* Down
* Pendulum
* Alternating (plays steps 1, 2, 1, 3, 1, 4, 1, 3, 1, 2…)
* Random
* Pseudo-random (without repeats)
* Addressable

The **GATE** input of this first segment continues working as a clock, and the sequence CV is also being output by the first segment. When using the addressable playback mode, the clock signal is ignored.

The **TIME/LEVEL** CV input (and its slider) either serves as an address CV control when using the addressable playback mode, or as a reset input, when using one of the other playback modes.

If the first segment is a **HOLD** segment, no quantization is applied and the output range is full (a range of 0 to 8V is selected by the slider). If the first segment is a **RAMP** segment, the output range is limited to 0-1V, and the steps are quantized to semitones (ie, increments of 1/12V).

If your segments contain a loop, playback and addressing will be restricted to the looped portion.

The reset input works as follows. A rising edge on this input resets the sequencer to the first step, and inhibit the next clock pulse if it comes within the next 5ms. The clock is also inhibited while the reset input is held high.

The active step is continuously sampled, allowing the module to be used as a sequential or CV-addressable “switch”.

#### Audio oscillator

Hold for more than 3s the segment type selection button of a solo segment (clocked or not) to turn this segment into an audio oscillator. The LED goes through a complex blinking pattern to indicate that this function is active. This is indeed a variant of the LFO with the following differences:

* The output is bipolar (+/- 5V).
* A different selection of waveforms is accessible with the **SHAPE/TIME** potentiometer: triangle with variable slope -> sawtooth -> sawtooth and square blend -> square with variable pulse width.
* Band-limited synthesis.
* The frequency tracking algorithm used to track the signal on the **GATE** input, adapted from Tides 2018, is optimized for locking onto audio-rate signals.
* A root note of C4 is played with the slider mid-way.
* The blinking rate of the slider's LED provides assistance with tuning: its rate slows down as the note gets closer to a C. This can also be used to help tuning an external oscillator connected to the **GATE** input.

### v1.1

* Powering the module on while pressing the **left** button enables (or disables) the color-blind mode. The LED brightness and blinking patterns are more contrasted:

	* **RAMP**: moderate brightness, with a faint brightness modulation. Smooth blinking pattern when the loop is enabled.
	
	* **STEP**: high brightness. Sharp blinking pattern when the loop is enabled.

	* **HOLD**: low brightness. Sharp blinking pattern when the loop is enabled.

### v1.0

Initial release.
