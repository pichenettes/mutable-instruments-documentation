## Download

[Latest version (1.2)](downloads/tides_1.2.wav)

Please read [this section](../manual#firmware) of the user manual to learn more about the upgrade procedure.

## Revisions

### v1.2 (final)

* Fixed a crash that could occur with some waveshape settings, when changing the frequency range.
* Fixed a crash occurring with very large or low **FM** CV.
* Improved the reliability of the clock tracking algorithm in extreme cases (very fast or slow clocks).
* Disabled anti-aliasing on the **EOA** and **EOR** outputs when they operate at non-audio rate. With these straighter edges, the threat of mis- or delayed triggering remains minor.
* When tracking an audio-rate signal on the **CLOCK** input, the phase of the generated signal matches the phase of the incoming audio signal, not just its frequency. The phase-modulation needed for this alignment is applied very slowly (over a few seconds) to prevent any audibly noticeable change in pitch.
* Reduced the amount of hysteresis in the quantizer responsible for converting a voltage into frequency ratios (via FM or the V/O input in clocked mode, or via **SHIFT/LEVEL** in the "different frequencies" output mode). This makes the relationship between a voltage and a specific setting more consistent when controlled by a sequencer.

### v1.1

* Powering the module on while pressing the output mode button (on the right) enables (or disables) the color-blind mode. The LED brightness and blinking patterns are more contrasted:

	* **First option**: modulated brightness.
	* **Second option**: full brightness.
	* **Third option**: low brightness.

### v1.0

Initial release.
