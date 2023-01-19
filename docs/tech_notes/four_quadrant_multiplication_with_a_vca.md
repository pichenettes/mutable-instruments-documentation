(As used in Blinds)

Let's say your signals are between -10V and +10V.

`IN` is your (bipolar) input signal (carrier).
`CV` is your (bipolar) cv (modulator).

Your toolkit consists of op-amps to add and subtract signals, and unipolar linear VCAs which compute `V (IN, CV) = IN x CV / 10`, but only for unipolar CVs (no restriction on IN though).

You want to use this to build a four-quadrant multiplier that computes `IN x CV / 10` even if `CV` is bipolar. Let's do a couple of algebraic manipulations...

		IN x CV / 10
		= IN x (CV / 10 + 1) - IN
		= IN x (CV + 10) / 10 - IN
		= V (IN, CV + 10) - IN

And that's it... Shift the `CV` high enough to ensure that it is unipolar, and compensate by removing the input from the result.

Now let's get to the tricky bit. Because four-quadrant-multiplication is commutative, you can swap the inputs IN and `CV` and get a circuit that should (in theory!) output the same thing. Indeed we have `V (IN, CV + 10) - IN = V (CV, IN + 10) - CV`. At least algebraically...

Blinds' circuit computes `V (CV, IN + 10) - CV`, that's why you see the audio signal going into what you're believing is the control path of the linear VCA ; and the CV going through two branches.

So why this odd choice, of computing `V (CV, IN + 10) - CV` instead of `V (IN, CV + 10) - IN`?

Let's have a look at `V (IN, CV + 10) - IN`... In the real world, the two terms in this equation will go through different paths, the first term will go through a 2164 cell and an op-amp before hitting the op-amp that does the subtraction, the other term won't go through that, so the first term will have some tiny bit of distortion, noise, high frequency roll-off and slew-limiting. Which means that when `CV` is 0, `V (IN, CV + 10) - IN` won't be zero, but will contain some faint garbage (high frequencies that are not completely nulled, higher harmonics from distortion, bonus noise). However, `V (CV, IN + 10) - CV` will be 0 as expected, first term is zero because the VCA has good offness, the second term is zero.

Since people expect the output to be silent when `CV` is null, I have chosen the `V (CV, IN + 10) - CV` variant. This swap is not magical - its downside is that if `IN` is null, we'll still hear the high frequencies of the CV bleeding through. But in a typical modular applications, when using Blinds as a VCA, the signal going into `IN` is rarely silent; and the CV rarely has super hard edges. So it's less of an issue!