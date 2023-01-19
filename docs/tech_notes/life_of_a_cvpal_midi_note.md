## Life of a MIDI note and DAC code

[(Explanations for this bit of CVpal code)](https://github.com/pichenettes/cvpal/blob/master/cvpal/calibration_table.cc#L88-L99
)

The argument `note` is a 16-bit integer containing the MIDI note << 7 (ie, 7680 for middle C, MIDI note 60) – which is a common unit of pitch in all Mutable Instruments MIDI projects (the increased precision allows pitch bend).

The goal is to convert this into a DAC code such that note `36<<7` (the lowest C note on a 61 notes keyboard) is converted into 0V, and we'll get an increase of 1V for each increase of `note` by `12<<7`. Hence the `- 36 << 7` subtraction at the beginning of the code. From now on, 0 means lowest C (#36) means 0V. The DAC is 12-bit and has a 4.096V internal reference, it is connected straight to the CV out with only a low protection resistor in-between, so ideally, a DAC code of 1000 translates into a voltage of 1.000V. *Ideally*, it would thus be a matter of taking this note, and multiplying it by 1000/1536 (we have `1536 = 12 << 7`). Alas, the DAC and its reference aren't perfect!

Instead, we use a calibration table. The calibration table stores the 9 DAC codes which will cause the DAC to output exactly 0.000V, 0.500V, 1.000V, 1.500V, 2.000V, and so on...

So `note 0` is mapped to calibration table entry 0
Note `6<<7` is mapped to calibration table entry 1
Note `12<<7` is mapped to calibration table entry 2
and so on..

(As an aside, you'll see [here](https://github.com/pichenettes/cvpal/blob/master/cvpal/calibration_table.cc#L36) that the calibration table is initialized with the "ideal" values of 0, 500, 1000 and so on).

For the notes which fall inbetween, we will use linear interpolation. For this purpose, we need to compute a 16-bit fractional part, which expresses how close we are to the next entry in the table (0 = exactly at an entry, 32768 = half-way between entries, 65535 = almost at the next entry). So what we really need is both `note / 768` (integral part), and `(note % 768) / 768 * 65536` (fractional part).

So `note 0` is mapped to 0 (integral), 0 (fractional)
`note 3<<7` is mapped to 0 (integral), 32768 (fractional)
`note 4<<7` is mapped to 0 (integral), 43691 (fractional)
`note 6<<7` is mapped to 1 (integral), 0 (fractional)
`note 7<<7` is mapped to 1 (integral), 10923 (fractional)
`note 9<<7` is mapped to 1 (integral), 32768 (fractional)

To do all this, the first `while` loops computes `note / 192` (stored in `first_index`), and `note % 192` (stored in `note`).

After this preliminary step, the integral part is just `first_index` divided by 4, because `note / 192 / 4 = note / 768`.

To compute the fractional part, we use a lookup table which stores `x / 768 * 65536 = x / 3 * 256` for all values of x between 0 and 191. If x is below 192, it's a direct lookup. If x is between 192 and 384, we just add 16384 for the result, if x is between 384 and 576, we add 32768 to the result and so on... that is the purpose of the second `while` loop.

Note that if we were lazy, we could have used first a while loop which divides by 768, and used a lookup table with the full 768 entries, but this would have used 1152 more bytes of flash memory!

