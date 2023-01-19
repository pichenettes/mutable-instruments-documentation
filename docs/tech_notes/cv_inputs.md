## Software tips

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
