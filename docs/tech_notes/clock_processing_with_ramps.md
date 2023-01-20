## Representing clocks by ramps

In Marbles' code, pretty much everything related to time is represented as a ramp going from 0.0 at a clock tick and ramping to 1.0 right before the next clock tick.

When the module is internally clocked, generating this ramp is easy - it is just a sawtooth oscillator.

When the module is externally clocked, several types of predictors are pit against each other to predict when the next clock tick will arrive (for example, if the pulse width of the clock appears to be a constant 50%, then the falling edge of the clock signal tells us exactly when the next rising edge will occur). This allows the reconstruction of a ramp signal matching the clock.

## Ramp manipulation

Clock division and multiplication on ramps is trivial - multiply by N (mod 1.0) for multiplication, divide by N and unwrap for division. Also (but not needed in Marbles): addition (mod 1.0) is phase-shifting.

Implementing jitter that preserves the mean of the interval between ticks is also a transformation on the ramp signal.

## Ramps for random processes

Then there's the matter of Bernoulli trials. Imagine that your master clock is represented by a ramp. To do a "Bernoulli division" of it, you keep a slave ramp going: when the bernoulli trial is "no", the slave ramp goes from its current value to half of what remains (adjust "half" if the coin toss is not fair); and if the trial is "yes", the slave ramp goes from its current value to 1.0 (there's effectively a delay of 1 clock tick in this process).

## Shaping ramps into outputs

From there, generating gate outputs is just a matter of thresholding the ramp – the threshold determining the gate length.

Generating random segments or curves is easy to do because we have access to a counter smoothly going from 0 to 1 between each random decision that can be "waveshaped" into the kind of transition curve we want (something familiar to whoever has studied the typical MI envelope code).

## A silver bullet?

Of course, the devil is in the details: what to do when a clock tick happens before we had predicted, or is coming later than expected? In those cases the waveform might "stall" or quickly evolve. And of course the Bernoulli procedure I have described is not perfect because it doesn't generate straight ramps (so the gate times might be incorrect, and the interpolated segments are monotonic functions, not straight lines).