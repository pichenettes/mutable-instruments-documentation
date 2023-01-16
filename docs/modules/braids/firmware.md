## Download

[Latest version (1.8)](downloads/braids_1.8.wav)

[Firmware update procedure](../manual#firmware)

## Revisions

### v1.8
* Rewrote from scratch the band-limited waveform synthesis code used in the analog waveform models (CS-80 saw, waveform morphing, square-saw morphing, sync square, triple saw, triple square). As a result, the sound quality of these models has been improved. The generated waveforms are crisper, with a more consistent amplitude, and less aliasing.
* Increased the range of the **TIMBRE** and **COLOR** settings on the **CSAW** model. It is still possible to synthesize the CS-80 "sawtooth with a notch" waveform of the previous revisions, but this model now covers a larger range of saw/square hybrid tones.
* Added a new synthesis model, **HARM**, which generates a mixture of sine harmonics. **COLOR** modifies the distribution of the amplitudes of each harmonics, around a central frequency set by **TIMBRE**.
* Improved the quality of the **Z?PF** models when they are played with fast envelopes, by adding linear interpolation to the **TIMBRE** parameter.
* Improved the quality of the **VFOF** model.
* Removed some of the "clickiness" of the built-in VCA.
* Improved the **DRFT** setting, which generates analog-style frequency drifting and detuning. The random process generating the noise has a more realistic behavior; and the amount of frequency drifting can be adjusted (instead of a plain on/off switch).
* Reworked the **SIGN** setting, which was designed to give each module a unique tone coloration, and glitches, generated from its serial number. The generated curves are now more "fuzzy" and "organic"; and the amount of coloration can be adjusted (instead of a plain on/off switch).
* Exposed many of the parameters of the internal AD envelope. The **TDST** setting has been replaced by 4 new individual settings to control the amount of **FM**, **TIMBRE**, **COLOR** and VCA modulation from the internal AD envelope – making it dead easy to create drum sounds with Braids. The **TENV** setting (the preset envelope shapes with silly names) has disappeared and you can now control both the attack and decay of the internal AD.
* Added many scales (about 50!) to the built-in quantizer, whose code has been rewritten from scratch to allow all kind of stuff not based on the 12TET system. A new setting, **ROOT**, selects the root note upon which the scale is built.
* Reversed the polarity of the output signal, so that the sawtooth wave ramps up.

### v1.7

* Added 808 kick and snare models.
* Added cymbal noise model.
* Added triple sine and triple triangle models.

### v1.6

* Fixed an issue causing rare glitches with the sawtooth and square modes.

### v1.5

* Added a new **TDST** option to assign a behavior to the trigger input: phase reset (sync), AD timbre modulation, AD amplitude envelope, and AD timbre/amplitude modulation.
* Visual feedback added to the **META** mode.
* The CV-controlled model change in **META** mode is now relative to the current model, as selected by the encoder.

### v1.4

* Fixed a bug that prevented the system settings to be written in memory in some situations.
* Added a new setting to the **RANG** menu that locks the output frequency to 440 Hz (useful as a reference for tuning other modules).
* Altered the phase and amplitude of some waveforms in the **WTBL** and **WMAP** modes to make the interpolation/scanning smoother.
* Added a new wavetable mode, **WLIN**, which allows the wavetable data to be scanned linearly by the **TIMBRE** control; while **COLOR** morphs between various interpolation methods and resolutions (between waveforms or within waveform sample data).
* Added a new wavetable mode, **WTx4**, generating four voices of wavetable synthesis. **TIMBRE** morphs through a representative selection of the waveforms; while **COLOR** selects various intervals between the four voices (unison with detuning, octaves, chords…)
* Added two new synthesis modes, with 3 voices of anti-aliased sawtooth or square waves. **TIMBRE** and **COLOR** control the interval/detuning of the second and third voices.
* Added a new synthesis model, **DRUM**, which is a variant of **BELL** generating an additive timpani-like sound. **TIMBRE** controls its decay, while **COLOR** the tone brightness.
* Reworked the **FOLD** mode to provide more destructive wavefolding on its triangle/sine internal inputs. The amount of wavefolding decreases when note frequency is increased to prevent aliasing.
* Reworked the **FBFM** mode. The range of the FM index control (**TIMBRE**) is dynamically adjusted as a function of the COLOR and main frequency settings. This allows a greater amount of destructive FM in the low and mid ranges, while keeping aliasing under control when the frequency reaches the higher octaves.

### v1.3

Initial release.
