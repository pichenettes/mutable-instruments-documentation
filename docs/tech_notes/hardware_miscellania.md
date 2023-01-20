## Reverse polarity protection

Two approaches can be found in the schematics:

1. Series polyfuse + shunt diode. When the current is applied in the wrong direction, it flows through the diode which acts like a short, sucking a lot of current from the PSU. Then the polyfuse "blows" and disconnect everything. Note that the polyfuse is absolutely necessary for this to work.

2. Series diode, preferably Schottky (low voltage drop). When the current is applied in the wrong direction, the diode blocks it. This protection scheme doesn't need a polyfuse.

So why did I stop using 1?

-   With 1/, the small resistance of the polyfuse causes a voltage drop that varies with the current consumption of the module. The result is that the +12V supply for the op-amps slightly follows the current consumption of the digital section. Not good.
-   What happens when a module is plugged backwards? With 2/, nothing happens, it's like an open circuit. With 1/, for a short time, the PSU will try to generate as much current as it can. I thought some PSUs might not like this at all. And there's the question of very beefy PSUs - some PSUs are now so beefy that they'd happily dump 2A of current - in other words, the shunt diode should tolerate very large currents.

Note that I continue using the polyfuse + shunt diode for circuits which uses the V2164 - since this IC absolutely needs to be protected against a missing V- - and this requires the shunt diode.
