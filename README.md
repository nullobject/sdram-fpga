# SDRAM Controller

Because synchronous dynamic RAM (SDRAM) has complex timing and signalling requirements, a memory controller is necessary to avoid having to deal with the nitty-gritty details when reading or writing. Its job is to hide the complexity of things like row and column addressing, precharging, and refreshing. Instead it lets us treat SDRAM just like plain old static memory.

This SDRAM controller provides a symmetric 32-bit synchronous read/write interface for a 16Mx16-bit SDRAM chip (e.g. AS4C16M16SA-6TCN, IS42S16400F, etc.).

Even though the SDRAM chip only has a 16-bit data bus, the controller uses a 32-bit data bus because it is more efficient to burst multiple words from the SDRAM than it is to do individual reads and writes.

## State Diagram

<img alt="State Diagram" src="https://raw.githubusercontent.com/nullobject/sdram-ctrl-fpga/master/state-diagram.png" />

## Licence

This project is licensed under the MIT licence. See the LICENCE file for more details.
