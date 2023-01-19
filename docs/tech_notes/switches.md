## Debouncing

The switch is polled at a rate of 1kHz. At each tick, the state of the switch is read as a bit, and shifted into a switch register storing the history of the switch state, as an 8-bit value.

This value is:

* `0b00000000` for a switch in a stable, pressed state.
* `0b11111111` for a switch in a stable, released state.
* `0b10000000` for a switch that has just reached a stable "pressed" state.

Code examples from Tides 2018:

* [Switch reading](https://github.com/pichenettes/eurorack/blob/master/tides2/drivers/switches.cc#L69)
* [Switch state enquiry](https://github.com/pichenettes/eurorack/blob/master/tides2/drivers/switches.h#L53)

The internal pull-up of the GPIO is used.

## Timing

[Code example from Tides 2018](https://github.com/pichenettes/eurorack/blob/master/tides2/ui.cc#L84)

Explanations:

* When a switch has been recently pressed (1), we store the time (2) at which it has been pressed, and we generate a “switch pressed” event. If the button doesn’t have to respond to long presses, this event is usually immediately handled!
* If a switch is pressed, and the elapsed time exceeds a certain threshold (say 2 seconds), we generate a “switch released” event, and prevent the generation of this event when the switch is released.
* If a switch is released, and is not inhibited, we generate a “switch released” event.

(1) the switch debouncing code stores the state of the switch over the past 8 scans, with a scan rate of 1kHz. So if the state is `0b11111110`, we know that the switch has been recently pressed.

(2) The 1kHz timer responsible for UI management counts the number of seconds elapsed since the module is powered on.
