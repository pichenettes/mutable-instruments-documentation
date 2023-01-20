## Alternate modes

In its tumultuous teenage years, Clouds tried to be everything, including a delay/pitch-shifter, a spectral processor, a projectionist and a cab driver in Rouen. This experimental code is still available in the module, by pressing the **B** button for 5 seconds until one of the LEDs glows in orange, and then repeatedly pressing the button to select one of 4 functions:

-   (First LED lit) Granular processor (normal operation).
-   (Second LED lit) Pitch shifter/time-stretcher.
-   (Third LED lit) Looping delay.
-   (Fourth LED lit) Spectral processor.

These features are experimental, and the knobs might be reassigned to functions very different from what is printed on the panel. It is not recommended to use these alternate modes if you plan to let Tony Rolando come play with your system.

### Granular mode

Home sweet home.

### Pitch-shifter/time-stretcher

This mode is quite similar to the granular mode, except that it uses two overlapping grains synchronized with the most salient period of the sound. The grains are carefully spliced so that they mesh well with each other (a technique similar to the "deglitching" of early pitch-shifters). Modulating **POSITION** when recording is frozen will "scrub" through the audio buffer. Clouds' uses classic time-domain methods which are not suitable for polyphonic or percussive material (unless this percussive material is breakbeats and you liked Akai samplers. Then: smile).

**DENSITY** creates a granular diffusion effect based on all-pass filters. 

**TEXTURE** acts as a low-pass/high-pass filter.

**SIZE** controls the size of the overlapping windows used for pitch-shifting and time-stretching – from an extremely grainy "drilling" sound to smooth bits of loops.

Sending a trigger to the **TRIG** input creates a clock-synchronized loop (when **FREEZE** is enabled) or stuttering effect – equivalent to applying a tempo-synchronized decaying envelope on the **POSITION** parameter.

### Looping delay

The looping delay mode continuously plays back audio from the buffer without any kind of granularization.

**POSITION** controls the distance between the playback head and the recording head (in other words, the delay time). Modulating **POSITION** will create effects similar to vinyl scratching or manual manipulation of tape.

When **FREEZE** is activated, the content of the audio buffer is looped (stutter effect). **POSITION** controls the loop start and **SIZE** the loop duration. **DENSITY** creates a granular diffusion effect based on all-pass filters; and **TEXTURE** acts as a low-pass/high-pass filter.

**SIZE** controls the size of the overlapping windows used for pitch-shifting – fully clockwise for a smooth result that might smear transients, fully counterclockwise for a grainy, almost ring-modulated sound.

When **FREEZE** is enabled, sending a trigger on the **TRIG** input creates a clock-synchronized stuttering loop. Otherwise, the period of the trigger pulses sets the delay time – provided this delay is shorter than the recording buffer size.

### Spectral madness

In this mode, the incoming signal is converted into "frames" of spectral data, that are stored, transformed, recombined, and resynthesized as a time-domain signal.

**POSITION** selects into which buffer the audio is poured (when **FREEZE** is not active), or from which buffer the audio is synthesised (when **FREEZE** is active). For example, set **POSITION** to its minimum value. Press **FREEZE**. You get a first texture. Set **POSITION** to its maximum value. **UNFREEZE**. Wait for something else to happen in the incoming audio. Press **FREEZE** again. By moving **POSITION** you interpolate between the two textures which had been captured at the press of **FREEZE**. Depending on the quality settings there are 2 to 7 buffers laid out over the course of the **POSITION** knob. What the module does is crossfade between a "wavetable" of FFT slices.

**SIZE** changes the coefficients of a polynomial that determines how frequencies are mapped between the analysis and synthesis buffers. It's like a 1-knob GRM Warp. Over the course of the knob it will do spectral shifting, but also spectral reversal.

**PITCH** controls the transposition (pitch-shifting).

**DENSITY** determines how results from the analyzer are passed to the resynthesizer. Below 12 o'clock, there's some increasing probability that a given FFT bin won't get updated, causing a kind of partial freeze. After 12 o'clock, adjacent analysis frames are increasingly merged together (like a low-pass filter in the amplitude each frequency bin). At extreme settings, random phase modulation is applied to smooth things - giving you different flavours of spectral muddling/reverb.

**TEXTURE** does two things: below 12 o'clock, it increasingly quantizes the amplitudes of the spectral components, like a very low-bitrate audio file. After 12 o'clock, it increasingly weakens the strongest partials and amplifies the weakest ones. This has the effect of making the spectrum more noise-like. 

Sending a trigger to the **TRIG** input creates various frequency-domain glitches typical of corrupted (encoded) audio files. It works as a kind of build-up/feedback effect - the shorter the pulse, the smaller the effect. With a continuous gate, it'll really start to rot.

