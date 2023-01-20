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

## Hardware implementation

### Principle

The main pot and attenuverters are read by the MCU's ADC – they do not offset or attenuate the CV in the analog domain.

The CV input is scaled to the 0..3.3V range and read by the MCU's ADC. Typically, the bottom of the CV input range is mapped to 3.3V, and the top of the CV input range is mapped to 0V. This scaling is done with a rail to rail op-amp, in inverting configuration. In this configuration, the op-amp input is never directly exposed to the external voltage.

The MCP600x is a good candidate for this task: it’s super cheap and designed for low supply voltages, like MCUs. No need to use anything fancier - the MCU’s ADC will be the weakest link in terms of noise and bandwidth anyway.

### Advantages

* 100k input impedance guaranteed.
* The V- input of the op-amp is a summing point - you can attach there as many CV inputs or pots if you want to offer multiple CV inputs.
* The circuit can be easily adjusted for various CV input ranges (as opposed to a lazier design in which the CV is clamped and directly connected to the MCU's ADC input).
* The capacitor in the feedback path of the op-amp acts as a 1-pole low-pass filter which removes some of the high frequencies in the CV signal - providing cleaner readouts.
* The output of the op-amp works as a very low impedance source for the ADC. The successive approximation ADC found in MCUs does not like being driven from sources with high ouptut impedances!
* It makes factory testing easier, since one can test the pot, attenuverter, and CV input separately.
* From the perspective of the user, hardware attenuverters like those used in Braids make it more difficult to adjust subtle modulation amounts, and since they work by crossfading the original signal and the signal inverted through an op-amp, the circuit cannot entirely cancel very fast edges. There is also the issue of ADC resolution: if your ADC has a resolution of 10-bit and if your hardware attenuverter attenuates the signal by a factor of 16, you’ll capture the signal with only 6-bits of resolution. Whereas doing the attenuation in software preserves resolution, especially in the recent modules which all use 32-bit floating point numbers internally.
* Individually acquiring the main potentiometer and attenuverter position gives freedom to tweak the response curve of these pots, for example to add “virtual notches” to them - a plateau near 12 o'clock for the attenuverter, or an enlarged spot around middle C for a pitch control.

### Design of the negative voltage reference

As a case study, let's consider [Grids](../modules/grids/downloads/grids_v02.pdf)

The voltage reference receives current through a shunt resistor `Rshunt`, here `R8`.

The -5V voltage it provides goes to six 100k resistors to (virtual) ground, that is to say, `ILoad = 5 x 6 / 100k = 0.3mA`. Or if you want to think in terms of `RLoad`, this is equivalent to six 100k resistors in parallel, that is to say 16.7k.

Where does `Iload` come from?

Assuming the -12V rail is stable, we have `(12 - 5) / 10k = 0.7mA` flowing through the shunt resistor, into the reference.

Thus, `0.7 - 0.3 = 0.4mA` will be sunk by the reference. And `0.4e-3 x 5 = 2mW` of heat will be dissipated by the reference which is way below the maximum rated dissipation.

One can think of it this way: the reference takes an excess of current, supply some of it to the load, and the rest is sunk by it. It is important not to starve the device (providing through `Rshunt` less current than the load will take) - but the opposite (providing through `Rshunt` more current than the load will take) is possible, as long as the dissipated heat stays reasonable. And that's kind of the point of using a reference!

One might wonder why 0.4mA are "wasted" like that. To reduce the waste, I could have used a lower `Rshunt` like 22k, which would have given 0.32mA flowing through Rs, 0.3mA through the load and only 0.02mA sunk by the reference. But then, what if the PSU regulation is bad and it supplies only -11.8V? We would starve the reference!

So I usually allow 20 to 30% more current. And I really don't see the point of picking a specific resistor value just for this one resistor. So if I need 15k and there's nowhere else in the circuit where this value is used... then I go for 10k, which is very often used somewhere else. Or sometimes I put two resistors in parallel to get the value I need. The module will be wasting a few extra tenths of mA, but we don't have the extra cost and wastage of loading another resistor reel in the pick and place when making the boards.

In the schematics of more recent modules, the voltage reference is annotated with the actual current delivered to the load, and how much it receives through the shunt resistor.

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