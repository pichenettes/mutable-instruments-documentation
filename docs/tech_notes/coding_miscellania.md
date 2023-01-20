## Polyphonic voice allocator implementation

[Code](https://github.com/pichenettes/stmlib/blob/0a641fb2fd1572b9d8c849a50b7d9bb70aeba3aa/algorithms/voice_allocator.h#L47)

`pool_` stores the state of each voice. `lru_` is used to figure out the “age” of each voice - the lower the number, the most recent the voice has been used, the less desirable it is to steal it.

Two pieces of information are stored in the `pool_` array. The highest bit is whether the note is active; the lowest 7 bits is the actual MIDI note. To retrieve the note, we mask the 7 lower bits `(& 0x7f)`. To retrieve the activity flag, we mask the highest bit `(& 0x80)`. 

Now why do we need to store both pieces of information? When a new note arrive and we need to allocate a channel for it, we want to use, in priority, the **least recently used inactive channel** (there might still be the “tail” of a decaying envelope). if none is available, the **least recently used active channel**. So even if a note is off I still need to keep track of it in the array!

The LRU array is filled backwards. So the channel 1 has to be marked as least recently used, and this is the first channel that will be used when the first ever note arrives.