## Download

[Latest version (1.2)](downloads/plaits_1.2.wav)

[Firmware update procedure](../manual#firmware)

## Revisions

### v1.2 (final)

#### Fixes and minor improvements

- Fixed a spurious retriggering of the internal envelope when entering/leaving the speech synthesis model.
- Reduced the amount of software filtering on the **TIMBRE** and **MORPH** knobs, to increase their responsiveness.
- Reduced the amount of hysteresis in the quantizer responsible for converting a voltage into discrete settings (for example, for model selection with the **MODEL** CV input, or chord selection with the **HARMONICS** knob and CV input). This makes the relationship between a voltage and a specific setting more consistent when controlled by a sequencer.
- The filters used for band-limited wavetable synthesis have been improved, eliminating the amplitude drop observed for the highest notes.
- The frequency range selector has a new mode: octaves. In this mode, the right button and **FREQUENCY** knob combo adjusts the root note, then the **FREQUENCY** knob transposes this root note up or down.

#### 8 new synthesis models

Pressing both buttons simultaneously **for a very short time** (a long press triggers the calibration procedure) enables or disables an alternative way of selecting a synthesis model: the first button selects the previous model, the second button the next one.

In particular, this allows you to navgiate to a bank of 8 new synthesis models:

* **1** Classic waveshapes with filter (**HARMO**: resonance and filter character - gentle 24dB/octave CCW, harsh 12dB/octave CW, **TIMBRE**: filter cutoff, **MORPH**: waveform and sub level, **OUT**: LP output, **AUX**: 12dB/octave HP output)
* **2** Phase distortion and modulation (**HARMO**: distortion frequency, **TIMBRE**: distortion amount, **MORPH**: distortion asymmetry, **OUT**: carrier is sync'ed (phase distortion), **AUX**: carrier is free-running (phase modulation)).
* **3, 4, 5** 2-voice, 6-operator FM synth with 32 presets (**HARMO**: preset selection, **TIMBRE**: modulator(s) level, **MORPH**: envelope and modulation stretching/time-travel). The two voices are alternatively triggered whenever a trigger is received on the **TRIG** input – this requires CV/Gate signals with accurate timing! When the **TRIG** input is not patched, a single voice plays as a drone, and the **MORPH** knob allows time-travel along the envelopes and modulations. The **LEVEL** input is repurposed as a velocity control - controlling loudness or timbre depending on how each preset is programmed. Each model corresponds to a different bank of 32 presets among basses/synths, keyboards/plucked strings/percussions, organs/pads/strings/brass.
* **6** Wave terrain synthesis with continuous interpolation between eight 2D terrains (**HARMO**: terrain, **TIMBRE**: path radius, **MORPH**: path offset, **OUT**: Direct terrain height (z), **AUX**: Terrain height interpreted as phase distortion (sin(y+z)).
* **7** String machine emulation with stereo filter and chorus (**HARMO**: chord, **TIMBRE**: chorus/filter amount, **MORPH**: waveform, **OUT**: voices 1&3 predominantly, **AUX**: voices 2&4 predominantly).
* **8** Four variable square voices for chords or arpeggios (**HARMO**: chord, **TIMBRE**: arpeggio type or chord inversion, **MORPH**: PW/Sync, **OUT**: square wave voices, **AUX**: NES triangle voice, **TIMBRE** attenuverter: envelope shape). Plug a trigger input to clock the arpeggiator.

#### Model customization and online editor

The module can receive audio data on its **TIMBRE** input to load custom data into a synthesis model (only one synthesis model can be altered at a time). This new feature is available for the following models:

* 6-operator FM: transfer of 32 custom patches in DX7 SysEx format. They will replace one of the built-in banks.
* Wave terrain synthesis: transfer of a custom wave terrain, accessible with **HARMONICS** turned fully CW.
* Wavetable synthesis: transfer of 15 custom waveforms and customization of bank D's wave map (in replacement of that bank's randomized selection of waveforms).

[Try the editor here (fully tested on Chrome)!](https://pichenettes.github.io/plaits-editor/)

### v1.1

* Powering the module on while pressing the **right** button enables (or disables) the color-blind mode. A different brightness level, instead of the green and red colors, is used to distinguish the first and second group of models.

* The frequency range/octave setting has a new setting (represented by a "chasing lights" pattern) for LFO operation. In LFO mode, the **FREQUENCY** knob goes from about 0.015 Hz (1 minute) to about 16 Hz.

### v1.0

Initial release.
