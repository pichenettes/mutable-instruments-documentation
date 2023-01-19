Do digital oscillators with analog V/O inputs employ an analog exponential converter? Not at all!

## Drawbacks of exponentiation in the analog domain

It would be impractical to do this in the analog domain:
- All the disadvantages of analog exponential conversion such as temperature dependency.
- Low notes would be converted into very small voltages, requiring a very precise ADC! For example, assuming that the ADC is referenced at 2.5V and that we want a span of 10 octaves, a semitone at the bottom octave would correspond to an increment of `2.5 * (2^-10 - 2^(-10+1/12.0)) = 0.14mV`. One would need at least a 16-bit ADC to detect that. And a rather astounding voltage reference!

The beauty of V/O is that you can represent a large octave span without having to deal with ridiculously tiny voltage at the bottom end of the scale.

## Case study: Braids

So how does it work in Braids?

1. The V/O input CV and the voltage at the wiper of the COARSE knob are added together.
2. The resulting voltage is scaled by a factor of 0.25 by [IC9B and IC7A](../modules/braids/downloads/braids_v50.pdf) to yield a voltage between 0V and 2.5V, read by the ADC. Because the ADC is 12-bit (0 to 4095), we have a scale of about 409.6 ADC units per octave.
3. This ADC value is scaled / offset by two numbers to get a 14-bit number - the highest 7 bits represent the MIDI note number, the 7 lowest bits a 1/128th of a semitone. `Settings::adc_to_pitch` does this conversion. https://github.com/pichenettes/eurorack/blob/master/braids/braids.cc#L231 So what are the magic numbers? The scaling ratio is about 128 * 12 / 409.6. But since 1% resistors are used all the way from the CV input to the ADC, the actual value is measured during the calibration procedure.
4. So the question is now how to convert this 7+7 bits number into a phase increment for the oscillator. The code is here! https://github.com/pichenettes/eurorack/blob/master/braids/analog_oscillator.cc#L46 How does it work? We transpose up until we reach the top octave, count the number of transpositions, read the value from a linear to exponential table pre-computed for the top octave, then shift down.

## More recent modules

* The summing of the V/O CV input and the FREQUENCY (or COARSE) knob is no longer done in the analog domain - the V/O CV and the FREQUENCY knob are read by independent ADC channel, and the summing is done in software. Why? This allows software controlled scale, or quantization, or "virtual notches" on the knob.
* Pitch is internally represented as a floating point MIDI note. (for example, 69.5000 for an A4 up a quartertone).
* The conversion is done by this [routine](https://github.com/pichenettes/stmlib/blob/master/dsp/units.h#L40), which evaluates `2^(x / 12.0)` by multiplying values read from two lookup tables, one for the integral part of x, the other for the fractional part of x.
* The phase increment for an oscillator is thus `440.0f / sample_rate * SemitonesToRatio(midi_note - 69.0f)`

## The future

The following routine does the job of `SemitonesToRatio` without employing a LUT, with a precision selectable by a template parameter:

	// Computes 2^x by using a polynomial approximation of 2^frac(x) and directly
	// incrementing the exponent of the IEEE 754 representation of the result
	// by int(x). Depending on the use case, the order of the polynomial
	// approximation can be chosen.
	template<int order>
	inline float Pow2Fast(float x) {
	  union {
	    float f;
	    int32_t w;
	  } r;

  
	  if (order == 1) {
	    r.w = float(1 << 23) * (127.0f + x);
	    return r.f;
	  }
  
	  int32_t x_integral = static_cast<int32_t>(x);
	  if (x < 0.0f) {
	    --x_integral;
	  }
	  x -= static_cast<float>(x_integral);

	  if (order == 1) {
	    r.f = 1.0f + x;
	  } else if (order == 2) {
	    r.f = 1.0f + x * (0.6565f + x * 0.3435f);
	  } else if (order == 3) {
	    r.f = 1.0f + x * (0.6958f + x * (0.2251f + x * 0.0791f));
	  }
	  r.w += x_integral << 23;
	  return r.f;
	}
