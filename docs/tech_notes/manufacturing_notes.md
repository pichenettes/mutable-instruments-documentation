## Manufacturing process

On a typical module, the only parts that are hand-soldered are the connector on the BOTTOM part of the board (on recent modules, there is only a power connector). All components are placed on the TOP side of the board, the board goes through a regular SMT assembly process, then the remaining PTH parts are inserted and the board is wave-soldered.

In the case of Yarns, Stages, and Beads, there are SMT parts on the BOTTOM part of the board, in that case, a wave solder pallet is made. It masks every SMT component and only exposes areas with through-hole solder pads. The board can be wave soldered.

This process has some drawbacks:

* There must be some minimal distance between the THT pins and the closest SMT component.
* The SMT components shouldn’t be taller than 4 or 5 mm. That’s why the crystal and electrolytic caps on Yarns are on the other side of the board.
* It’s costly (about 1.5k€).

![](images/pallet.jpg)
