# PerspiC 1 RAM

PerspiC1 RAM is the Read-Write memory module for the Perspicuous Computer 1. In order to be perspicuous, the RAM needs to be static - that is, it will hold it's contents in the absence of any clock signals.

Each memory bit will include an integrated LED, making the PerspiC1 RAM a very simple 8 x 8 display.

Like other PerspiC 1 modules, it fits on a 100mm x 160mm Eurocard style PCB.

## Design
Static RAM is generally made up of logic flip-flops (latches) for storage and output switches for read access. There is also address decoding logic and output buffers. We want to implement these elements using Bipolar Junction Transistors (BJTs), diodes and resistors.

Address decoding and output buffering etc. will generally scale O(log n). So. in order to minimise cost and maximise available memory capacity, we need to design to minimise the number of components in each memory bit.

The per-bit output switch can be implemented as an open-collector NAND gate using Diode Transistor Logic (DTL) using 1 x BJT, 3 x diodes and 1 x resistor. Alternatively, implemented as a NOR gate followed by an open-collector inverter using Resistor-Transistor Logic (RTL) would require 3 x BJTs and 4 resistors. Seems the DTL NAND wins here.

The more complex part is the flip-flop for each bit, which can be implemented as a Gated D-latch with either 4 x 2-input NAND gates (using DTL) or 4 x 2-input NOR gates (using RTL), or it could be implemented as a Polarity Hold latch with 3 x 2-input NAND gates (using DTL).

The formula for an n-input DTL NAND gate is 1 x transistor, n+1 diodes and 2 resistors (less 1 for open-collector). For a D-Latch, this gives 4 x BJTs, 12 x diodes and 8 x resistors. For a Polarity Hold Latch, this gives 3 x BJTs, 9 x diodes and 6 x resistors.

The formula for an n-input RTL NOR gate is n x transistors and n+1 resistors (less 1 for open-collector). For a D-Latch, this gives 8 x BJTs and 12 x resistors

Looking at the size and cost of the discrete components, the 24 parts for a DTL NAND D-Latch is cheaper and slightly smaller than the 20 parts for the RTL NOR D-Latch, but the 18 parts for the Polarity Hold Latch wins out here.

With the bulk of the componentry per bit tied up in the flip-flop element, there exists the opportunity to have multiple output (read) ports, for example two.

## PerspiC 1 Register
To fit 8 x 8 x 4 transistors plus all the decoding and buffering logic onto one Eurocard sized board requires some extra design. The actual latch components (3 NPN BJTs, 9 diodes and 6 resistors per bit) are mounted on a 9-bit Single-Inline Memory Module (SIMM) (total 27 NPN BJTs, 81 diodes, 54 resistors and 2 capacitors).

This board is designed as PerspiC1_Reg and the gschem schematic and PCB layout] are available in my PerspiC1 SVN repository.

The PerspiC 1 Register boards measure 100mm x 21.6mm and up to nine can easily fit on a 200mm x 100mm SeeedStudio Fusion service order with 0.5mm gaps between them.

The Register board has a 39-pin (to fit in 100mm...) right-angle single inline pin connector that can plug into a matching socket on a carrier card, or be soldered directly onto the carrier. The 9 bits are arranged as two groups of 4 and 1 extra bit (as far as enabling writing is concerned). There are 9 pins for data in, 18 pins for data out and inverse data out (we get that for free), 3 pins for _Clr_ (active low, one for each group) and 3 pins for Set (active high, one for each group), plus 2 GND and 3 Vcc pins and one spare.

## PerspiC 1 RAM board
The PerspiC 1 RAM board is a carrier for eight PerspiC 1 Register boards and three PerspiC 1 decoders as well as input and output buffering. The RAM board has dual read ports and a separate write port, so up to three operations on the 8 bytes of RAM can be occurring simultaneously, making it ideal as a register file for a CPU.

As with the Register boards, the data busses of the PerspiC 1 RAM board are 9 bits wide, although only 8 bits need be implemented.
