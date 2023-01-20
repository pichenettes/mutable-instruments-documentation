Clouds is indeed a rare instance of a module whose scope/feature set got endlessly redefined - but with only minor hardware changes in the process.

### Initial concept: Aeons

![](images/aeons.jpg)

Spectral sampler (we sample a FFT slice and unroll it forever) with pitch-shifting and GRM warp weird thing.

Sounded great on my computer, but reacted horribly to CV once I got the code running on the hardware - in particular lots of ugly time-quantization because of FFT windowing. CVs had to be slew-limited to the point of being unrecognizable. Sounded like shit with material available in a modular environment (like synthetic waveforms, percussions).

### Evolutions

Then: multi-function processor, with 4 modes: random granular, WSOLA time-stretcher/pitch-shifter, PSOLA time/pitch (for vocals), and the original spectral mode.

Then: multi-function processor, with 4 modes: granular, multi-tap diffused delay (sort of Ursa Major Space Station), pitch-shifter, and the original spectral mode.

Then the pitch-shifter got merged with the delay and the third mode was a strange reverb in which one could pour the incoming audio through different places in the whole structure.

At this point, the code was quite large, the module did all kinds of weird things with weird meta-controls with abstract names.

I demoed it to a few people and what they liked most was the granulator, so I killed everything else and focused on that. That's when I realized I could salvage all the reverbey bits because they work so well at turning harsh grain textures into lush atmospheres.

The **BOKEH** parameter shown in some teasers was originally something that randomized the phase of the FFT and low pass filtered the magnitude in each FFT bin (equivalent to overlapping blurred versions of successive segments of sound).
