## Alternate modes

A long press on the mode button for the *t* generator (labelled **E** in the [manual](../manual/)) activates a variant of the currently selected mode, represented by a blinking LED.

* Green blinking: **Independent Bernoulli**. The choice between ***t<sub>1</sub>*** and ***t<sub>3</sub>*** is no longer exclusive. The coin toss is independent, and both channels, or none of them can be on.
* Orange blinking: **Deterministic divider/multiplier**. A clock division or multiplication ratio selected by **BIAS** is applied to ***t<sub>3</sub>***, and its reciprocal is applied to ***t<sub>1</sub>***. There is no randomness.
* Red blinking: **Three states**. The module randomly selects between a trigger on ***t<sub>1</sub>***, on ***t<sub>3</sub>***, or on no output at all.

A short press on the button reverts to the normal mode.

## Commented out "markov" mode

The code contains an unaccessible, additional mode for the *t* generator.

[Implementation here](https://github.com/pichenettes/eurorack/blob/master/marbles/random/t_generator.cc#L213
)

Just a bunch of heuristics to make “balanced” rhythmic patterns.

To implement a true Markov model, you’d need a large table storing the probability of playing a hit at clock tick t, for all the possible decisions taken in the past n ticks (aka “contexts”). You need long contexts if you want to learn well large-scale structures (like, a whole bar), with the drawback that you’d need a lot of training data to estimate the probabilities, a lot of memory to store everything, and then you might overfit and learn some patterns by heart. Not so great.

A different idea to drastically reduce the number of parameters of the model is to state that the probability of playing a hit at clock tick t is only influenced by several “features” extracted from the context – the code and comments are clear about what these features are.

The weight of each of these features is empirically chosen, and is influenced by the **BIAS** knob. I didn’t spend a lot of time tweaking these weights. One could actually train this properly, it’s just a little logistic regression. But you’d still have to introduce some very arbitrary choices in how the weights are modulated by the BIAS knob.
