## Download

[Latest version (1.3)](downloads/marbles_1.3.wav)

[Firmware update procedure](../manual#firmware)

## Revisions

### v1.3 (final)

#### Fixes and minor improvements

* Fixed a bug causing spurious jumps in the **DEJA VU** loop playback position (occurring randomly, when **DEJA VU** is turned CCW), and another bug causing an unexpected change in the loop (occurring randomly, on average once every 37 hours) when **DEJA VU** is locked at 12 o'clock. Thanks to Tyler/[float32](https://github.com/float32) for finding these two!
* Fixed a bug occurring when a buggy external clock source "stutters" by sending two rapid rising edges distant by a few ms at each clock tick. 
* Lowered the amount of hysteresis in the quantizers responsible for converting a voltage or knob position into discrete settings (eg: a clock divider ratio, or **LENGTH**). This makes the relationship between a voltage (or knob position) and a specific setting more consistent.
* Increased the width of the "virtual notch" for the 14-step setting of the **LENGTH** knob.

#### Implicit and explicit reset

* If a clock pulse arrives after a long pause (3 seconds, or 4 times the interval between two clock ticks, whichever the longest), the module will reset all counters, dividers, patterns, and the step number of the relevant section (t or X). Provided **DEJA VU** is enabled and set at 12 o'clock, this gives a completely reproducible behavior at each restart of the clock.
* Hold the t and X section mode buttons (labelled **E** and **N** in the manual) simultaneously to repurpose the **JITTER** and **STEPS** CV inputs as explicit reset inputs for the **t** and **X** sections. The corresponding LEDs blink temporarily for 2s when the repurposed reset inputs are enabled, and go off when they are disabled (which is the default setting).

The **JITTER CV** input is repurposed as a reset input for the t section: a rising edge on this input will reset, **on the next clock tick**, the clock divider, the step counter in the **DEJA VU** loop and the position of the rhythmic pattern.

The **STEPS CV** input is repurposed as a reset input for the X section: a rising edge on this input will reset, **on the next clock tick**, the position of the **DEJA VU** loop.

If a reset is received on the **t** side, this reset will be cascaded to the **X** section in the two following situations:
* The **X** section's clock input is left unpatched (ie, X1, X2, X3 are internally clocked by t1, t2, t3 respectively).
* The **X** section's clock is directly patched from t1, t2 or t3 (self-patching detection).

Outside of these cases, the **X** section is considered to be running independently from the **t** section, and a reset on the **t** side won't be propagated to the **X** side.

Emphasis was put on **on the next clock tick**: for this reset feature to work accurately, the reset pulse must precede, ideally by 1ms, the subsequent clock pulse. If the reset arrives right at the same time as the first clock pulse, the behavior is unspecified! Don't hesitate to delay your clock (or advance your reset) if you experience odd timing.

### v1.2

* When the module is in external processing mode, with an external clock for the **X** section, and when this clock's patch cable is unplugged from the **X** clock input, so that an internal clock is now used for the **X** section, the acquired CV data on output **X1** (which was shifted to outputs **X2** and **X3**) is now copied to the **DEJA VU** loop of all three outputs.

* If a 3-second pause (or a pause lasting more than 4 clock ticks, whichever is the longest) is observed on the **t** section's clock signal, the rhythmic pattern played by the **t** section will reset to the first step at the next clock tick.

* New *super lock* feature. A long press on the **t** or **X** **DEJA VU** buttons locks the random generation for this section – which will stop responding to the **DEJA VU** knob. When a section is in this *super locked* state, the illuminated push-button blinks rapidly. Press it to bring it back to normal. In other words, the **t** and **X** section can be in these 3 states:
  * Illuminated push-button off: random without repetition (equivalent to **DEJA VU** set to the minimum).
  * Illuminated push-button on: randomness amount controlled by the **DEJA VU** knob.
  * Illuminated push-button rapidly blinking: locked loop without randomness (equivalent to **DEJA VU** locked to 12 o’clock).

### v1.1

* Powering the module on while pressing the **X** mode selection button **(N)** enables (or disables) the color-blind mode. LED brightness and blinking patterns are more contrasted. Glowing, moderate brightness for the first setting, high intensity for the middle setting, low brightness for the last setting. When selecting scales, the number of blinks (from 1 to 6 – continuous) indicates the selected scale.

* Fixed a bug causing the X's section clock follower to temporarily go haywire when a patch cable is inserted in the clock input of the X section.

### v1.0

Initial release.
