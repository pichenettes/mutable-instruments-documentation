## Life of an audio buffer

(Applies to Clouds, Rings, Warps, Elements).

1. The audio samples received from the codec (resp: sent to it) are shoved by the DMA controller into `Codex:: rx_dma_buffer_` (resp: `Codec:: tx_dma_buffer_`).

2. An [ISR](https://github.com/pichenettes/eurorack/blob/master/rings/drivers/codec.cc#L551) is called whenever 50% of the input buffer is filled. This also means that 50% of the output buffer has to be filled. The [code handling](https://github.com/pichenettes/eurorack/blob/master/rings/drivers/codec.cc#L523) this interrupt will invoke the callback passed to `Codec::Init`, with pointers to the input and output buffers.

3. Typically, `Codec::Init` has been called on startup, with a callback named [`FillBuffer`](https://github.com/pichenettes/eurorack/blob/master/rings/rings.cc#L147).

4. [`FillBuffer`](https://github.com/pichenettes/eurorack/blob/master/rings/rings.cc#L88) will read the current value of the CV, convert the input samples to floats, invoke the main processing function (for example, modal synthesis for Rings) with the input data and the parameters read from the CV, then clip and convert the output samples to integer.
