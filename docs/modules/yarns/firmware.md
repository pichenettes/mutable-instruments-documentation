## Download

[Latest version (1.5)](downloads/yarns_1.5.syx)

[Firmware update procedure](../manual#firmware)

## Revisions

### v1.5 (final)

* Added a new **STEAL MOST RECENT** voice allocation mode, which steals the most recently played note (duh!) when the maximum polyphony is reached. This allows you to hold a note or chord on the left hand, and play legato a melody on the right hand that won't cut the chord.
* When several parts are receiving on the same MIDI channel, and when recording is enabled for one of them while a sequence is playing on the other parts, the received ```Note On``` MIDI messages no longer cause spurious transpositions of the sequences (original fix proposed by Chris Rogers).
* Added a new **Tx TUNING FACTOR** setting which multiplies or divides the output voltage in order to obtain interesting scales (for example, a whole octave stretched to 24 keys, or reversely, 2 octaves crammed in 12 keys). [Here is a nice illustration](images/tuning_factor.jpg) from the D50 manual showing what can be achieved with this feature. Note that the **TR TUNING ROOT** setting determines the central key around which tuning is stretched.
* The **O/ OUTPUT CLK DIV** and **B- BAR DURATION** settings are now available in the **4T** layout.
* The swing setting is no longer limited to the internal clock only, but can also be applied on top of an external MIDI clock.
* A new setting, **NUDGE FIRST TICK**, increases the delay separating the reset pulse sent at every bar or when the master clock is started, and the first clock pulse. This may solve synchronization issues with some sequencers that do not like receiving those two almost simultaneously.
* A new setting, **MS (MANUAL START)** prevents the clock from automatically starting when a key is pressed. When this setting is active, the clock can only be started by pressing the **START/STOP** button on the front panel.
* The **LG LEGATO** setting now has three options: **OFF** (as before), **AUTO PORTAMENTO** (notes played legato are not retriggered, portamento is applied only on notes played legato, similar to **ON** in the previous version, and to the SH-101's *AUTO* portamento setting), and **ON** (notes played legato are not retriggered, portamento is applied irrespectively of playing style).
* Fixed a bug causing the calibration data to be temporarily erased when using the **INIT** menu.

### v1.4

* Minor bug fixes.

### v1.3

* Added new settings to freely assign the CV outputs in the **1M**, **2&gt;**, **2P** and **4&gt;** layouts. The corresponding settings are called **3&gt;** and **4&gt;** and deprecate **AUX**. The available data sources that can be converted to CV are: velocity, modulation wheel (CC 1), aftertouch, breath controller (CC 2), foot pedal (CC 4), pitch bend, vibrato LFO (the intensity is controlled by the mod. wheel), and raw vibrato LFO (constant intensity).
* Added new settings to freely assign the third and fourth CV outputs in the **2M** layout. The corresponding setting is called **CV**, and affects CV output 3 or 4 depending on whether part \#1 or \#2 is selected with the **PA** setting.
* Added a new layout called **4V**, for raw conversion of continuous controllers into CVs. It consists of 4 parts. For each part **PA**, you can select a MIDI channel **CH** and a data source **CV**. Values are mapped between 0V and +5V.
* Fixed a bug occurring when Yarns had to forward pitch bend messages from its MIDI IN to its MIDI OUT.

### v1.2

* Fixed a bug in the just intonation code.

### v1.1

* Upgraded the version of gcc required to build the code. Might help DIYers in building the code!
* Fixed a bug which caused a spurious pulse to appear on the clock reset output, when the **B-** setting (**BAR DURATION**) was set to a value different from 0 and +oo ; and for some specific tempo values.
* Rewrote the band-limited synthesis code used for the audio oscillator mode (same change as for the recent Braids' firmware upgrade). In addition to a slight decrease of CPU use and an increase in "crispiness" at low frequencies, this drastically reduces the firmware size, giving more room for future fixes and upgrades.
* Solved a conflict in the handling of CC120 / CC121 / CC123. In the previous firmware revision, these messages were always assigned to "All sound off", "Reset controllers" and "All notes off" respectively. In this new version, they can be remapped to "Part 4 clock division", "Part 4 gate length" and "Part 4 arp direction" as stated in the MIDI implementation chart, when the "remote control" feature is enabled. The "remote control" feature provides extended editing facilities through Control Change messages.
* Added a coarse adjustment function to the calibration mode: press the TAP button while turning the encoder to increase/decrease the output voltage by about 5mV (instead of 0.2mV). WARNING: use Yarns' per-octave calibration mode at your own risk. We suggest you to perform the calibration adjustments on your VCO side and leave Yarns in its factory-calibrated configuration! Yarns is calibrated with precision equipment to give 1.000V, 2.000V, 3.000V, etc. for each C note.
* Fixed a bug which caused the state of the polyphonic voice allocation algorithm to be reset whenever the clock stopped.
* Added a new polyphonic voice allocation algorithm: **SO(RTED)**. In this mode, the lowest note is always played on CV/Gate outputs 1, the second note on CV/Gate outputs 2 and so on, from lowest to highest on the keyboard. Releasing a key does not reassign notes to channels.
* Added two new polyphonic voice allocation algorithms: **U1 (UNISON 1)** and **U2 (UNISON 2)**. When a single note is played, it is routed to all available output channels in unison; but when a chord is played, each note of the chord is dispatched to a different voice (lowest note to the first channel, highest note to the last channel). This option is available in the 2P mode to recreate the behaviour of pseudo-duophonic synths like the Polivoks or Odyssey. It is also available in the 4P mode even if, to our knowledge, no polysynth ever had such a feature! The difference between the variants 1 and 2 is the following: when two keys are held, and one of the keys is released, **U1** reassigns the remaining note to all channels (hardcore Odyssey behavior) ; while **U2** doesn't change anything (nice for playing and letting decay 2-note chords).
* Changed the behaviour of the portamento setting. The first values, displayed as **T0, T1, T2**... up to 50 correspond to constant-time (RC filtering, exponential curve) portamento, as implemented in the previous firmware version. The following values, displayed as **R0, R1, R2**... up to 50 correspond to constant-time (slew-limiting, linear ramp) portamento.
* Mapped CC\#112 and CC\#113 to "record tie" and "record rest" actions. It is thus possible to assign pads/buttons on a controller to these functions, to assist in the recording of sequences.

### v1.02 (released 08/01/2014)

* Most parameters can now be modified by MIDI control change messages. A global remote control channel can also be configured - this allows all parts of a multi-channel setup to be edited from a single channel. Please refer to [this chart](https://docs.google.com/spreadsheet/pub?key=0Ai4xPbRS5YZjdDlVWG5XWFZuM1I3X08tdVQ2eU5WT0E&gid=1).
* The vibrato LFO can now be locked to the MIDI clock. The first settings (0 to 99) correspond to a free-running LFO of increasing speed, the following settings correspond to clock divisions.
* The vibrato LFO (without any modulation wheel attenuation) can be output on the aux output in **1M** / **2M** modes.
* From now on, the firmware version number is displayed when the module starts up (02 is displayed in this version).

### v1.01 (released 05/14/2014)

* Visual feedback (a blinking **//** symbol) is now provided on the display when an arpeggio or note is latched.
* New **3+** mode with 2 parts, designed specially for [Edges](../../edges). The first part has 3 voices of polyphony on channels 1-3, and the second part is monophonic, on channel 4.
* The **B- (BAR DURATION)** setting has been modified to allow the generation of a short reset pulse only when the sequencer starts (**oo** setting), or the generation of a continuous gate while the sequencer runs (**0** setting).
* A glitchy pitch discontinuity - which occurred when both the portamento and transposition features were used simultaneously - has been removed.
* The display refresh screen has been increased to reduce risks of power supply buzz on sensitive modules nearby.

