## Ranges, polarity, offsets and attenuverters

This section explains the interactions between the attenuverter and the main knob for a synthesis parameter, and how they impact the minimum and maximum value this parameter will take, when modulated.

### Polarity

LFOs are sometimes unipolar (say they'll wiggle between 0V and 10V) or bipolar (they wiggle between -5V and +5V). How to know if your LFO is bipolar or unipolar? It's usually explained in its manual, but you can also check the colors on its output LEDs, or when you patch it through a module like Shades!

You'll also see that sometimes, LFO modules have a lower range (say unipolar between 0V and 5V).

### Main knob: offset

The main knob (eg: **DAMPING** on Rings) controls what is called an offset (between 0 and 5V). Think of it as a "base setting", ie, what setting you'd get with a flat (null) modulation signal.

If your LFO is bipolar, the parameter will wiggle around this base setting. If your LFO is unipolar, it'll wiggle between this base setting and a value further CW.

### Attenuverter

How wide will it wiggle around this base value? You decide with the attenuverter. In its central position, there's no wiggling. Turn the attenuverter further away from the central position and the wiggling amplitude increases, up to 1.5x the amplitude of the LFO (why 1.5x? Because some modulation sources can have a lower amplitude, so it's good to be able to amplify them). On the "+" side, the modulation keeps its polarity. On the "-" side the modulation's polarity is reverted.

### Example

You have a ramp (sawtooth with an increasing slope) bipolar LFO going from -5V to +5V. If you want to sweep the parameter from its minimum value to maximum value, you need to set the knob to its central position (offset of 2.5V), and the attenuverter around 2 o'clock to get a gain of 0.5. When the LFO is at the minimum, the module will receive `2.5 + 0.5 x -5V = 0V` (minimum position of the knob) ; when the LFO reaches its maximum, the module will receive `2.5 + 0.5 x 5V = 5V` (maximum position of the knob).

Now you have a unipolar LFO going from 0V to +8V, and you want to sweep the parameter from its maximum value to its medium value. You set the knob to its maximum value (offset of 5V), and the attenuverter around 11 o'clock to get a gain of -0.33. When the LFO is at the minimum, the module will receive `5 + -0.33 x 0 = 5V` (maximum value of the knob). When the LFO Is at the maximum, the module will receive `5 - 0.33x8 = 2.5V` (medium value of the knob).

### Clipping

Last thing to keep in mind: the minimum value is mapped to 0V, the maximum value is mapped to 5V. It's easy to go below or above these values with some modulations and some knob configurations. Nothing bad will happen, the wiggling will just be constrained to remain within those bounds. For example, unipolar LFO going from 0V to +8V, knob at the minimum (0V), attenuverter to the maximum. The modulation will go from `0 + 1.5 x 0 = 0V` (minimum position of the knob) to `0 + 1.5 x 8 = 12V`. This means that for about 40% of the course of the LFO, we'll just stay at the maximum position of the knob.

**For accuracy's sake: on most digital modules, the math is done digitally**

[Demo](https://www.desmos.com/calculator/fzx3oyitny)

* Green curve: LFO. You can adjust its minimum and maximum value with lmin and lmax.
* a: position of the attenuverter.
* k: position of the knob.
* Purple curve: how your parameter evolves, from minimum (0) to maximum (5).

## Software implementation

### Normalization probe

Starting from Rings and Warps, modules often try to detect whether their CV or trigger inputs are patched or not.

This is achieved by sending a *normalization probe* signal, pseudo-random noise, generated at the same rate as the ADC sampling. 

If ADC reads match the pseudo-random noise (no more than 2 mismatches in 32 consecutive reads), we know that no patch cable is inserted in the corresponding input.

[Example from Plaits](https://github.com/pichenettes/eurorack/blob/master/plaits/ui.cc#L368)

### ADC input filtering

Most of the time, a [one-pole IIR filter](https://github.com/pichenettes/eurorack/blob/master/tides2/cv_reader_channel.h#L61) on the ADC readout.

Boxcar filtering is doing a better job at eliminating impulsive outliers (common on the F4 ADCs), and a worse job at eliminating high frequencies. In terms of CPU it's slightly more inefficient than a one-pole filter, and it uses RAM (a fast implementation would work like this: pick the last sample in a delay line and remove it from the running sum, add the new sample to the sum and shove it in the delay line).

I also experimented with median filtering on the F4 (along with other adaptative schemes - for example adjusting the filter coefficient depending on the error between the current value and the readout, to allow faster tracking of fast changes), and was surprised that none of them worked much better than 2-pole filtering, which I'm now using on F4 projects.

As for backlash/thresholding, I'm trying to stay away from it as much as possible. Any implementation (including the one mentioned in the thread above) has the downside that it might make the maximum/minimum value of the pot impossible to reach. At the very least, you have to scale the value by (1.0 + 2 * threshold) and offset it by -threshold, then clamp to [0, 1.0], assuming that's the range of your parameter, to make sure turning the pot to the minimum/maximum allows you to always reach the minimum and maximum settings. Note that pots already have mechanical backlash, and that their response curve already have a flat zone at the minimum and maximum.