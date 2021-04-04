# PerspiC 1 ROM
Perspicuous Computer 1 ROM is a 100mm x 160mm Eurocard style PCB implementing 512 bits of "Read-Only Memory" as an array of diodes.

The ROM card can be used for storing machine code instructions (typical programs), microcode sequences, look-up tables, and decoders (such as instruction decoders etc.).

The ROM will appear as 64 bytes of data, or 32 short words of 16-bits each, with each half of the output data separately enabled. Multiple PerspiC 1 ROM cards can be included in any given implementation to allow more ROM memory.

## Functional Blocks

### The PerspiC 1 ROM board consists of:
- address decoder
- diode array
- output buffers

The ROM data exists as the presence or absence of 1N914-style small signal diodes in a matrix of 16 x 32. As each diode has two pins, this results in an array of 32 x 32 holes on 0.1" centers. This array can be populated directly with diodes, or it can be populated with turned pin socket strips, allowing diodes to be inserted and removed as required during development.

The diodes form part of a "wired-AND" array (using Diode-Transistor Logic), with each row driven by an active low enable. Because of inversion of the data at the output buffers, the presence of a diode represents a "0" bit on the open-collector outputs of the PerspiC1 ROM card.

The output data appears on the output connector as Open-Collector inverted data, allowing multiple cards to be connected via a bus.

## Components

### PerspiC1 ROM consists of:
- 84 x 2N2222 NPN transistors
- 151 resistors (all pull-up)
- 270 x 1N914 diodes + up to 512 more for the data bits
- 5 capacitors
- 1 LED
- 5 header blocks
- 1 edge connector
for a total of 517 components + data bit resistors. (not including the header blocks, there are as many components to support the 512 bits of data as there are bits of data...)

## Configuration
There are 5 sets of header blocks for configuring address decoding and data bus width.

## Data Bus Width
Header block H1 controls the enables for the upper half of the 16-bit data bus.

The lower half (bits D0 - D7, connected to J1 pins c24 - a27) is enabled by "A0" (J1 pin c2) pulled low. Therefore, this input ("A0") also acts as a data low byte enable.

The upper half (bits D8 - D15, connected to J1 pins c28 - a31) is enabled by "A0" high, if H1 is in the 1-2 position (away from J1). Otherwise, if H1 is in the 2-3 position, then the upper data bus is controlled by data high byte enable on J1 pin c9 being pulled low.

If 8-bit operation is required, connect the two halves of the data bus together (D0 to D8, D1 to D9 etc.) and set H1 into position 1-2, then use "A0" as the least significant address bit.

If 16-bit opertion is required, set H1 into position 2-3, and connect "A0" to high byte enable and use as a separate active low enable.

## Address Selection
Headers H2 - H5 control polarity of 4 inputs to enable the output buffers. Typically, these four inputs (on J1 pins c10, c12, c14 and c16) would be driven by upper address bits A6 - A9 and be in parallel across a number of ROM boards.

Setting each of these headers with the jumper in the 1-2 position selects active high enable for the respective address bit. Setting two jumpers in positions 1-4 and 2-3 would select active low enable. Note - don't connect pins 3-4 of these headers as that will cause the inverter to have its input connected to its output, causing unspecified operation and possibly consuming much current.

There is also a 5th active high enable on J1 pin c18. Pulling this low will disable the data bus output drivers.

## Design Musings
Should the array be 32 x 16, or 16 x 32? Single-stage decoding or two-stage?

All options require buffers on address inputs A5..A1. These buffers will produce inverted address lines ~A5..~A1 and require 10 diodes and 5 BJTs.
 All options also require additional output buffers to control the open-collector output bus requiring 32 diodes + 16 BJTs. There is also address decoding and control logic to control these final output buffers.

### 32 x 16 Single Stage
Need a 1-of-32 decoder with each output driving as many as 16 diode loads into the output buffers.
-Parts required:

### 10 diodes + 5 BJTs for buffered inverse address
-32 x 5 diodes for AND array
-32 diodes + 32 BJTs for row drivers
-16 diodes + 16 BJTs for output buffers
-Total: 218 diodes + 53 BJTs
-Each address buffer will drive 16 diode input loads and each decoder output will drive up to 16 diode input loads to the output buffers.
-16 x 32 Single Stage
Need a 1-of-16 decoder with each output driving as many as 32 diode loads into the output buffers.
-Parts required:

### 8 diodes + 4 BJTs for buffered inverse address
-16 x 4 diodes for AND array
-16 diodes + 16 BJTs for row drivers
-32 diodes + 32 BJTs for output buffers
-32 diodes + 16 BJTs for additional width of final output buffers
-Total: 152 diodes + 68 BJTs
Now each address buffer will drive only 8 diode input loads and each decoder output will drive up to 32 diode input loads to the output buffers.

### 32 x 16 Two Stage
1-of-4 followed by 4 x 1-of-8
-10 diodes + 5 BJTs for buffered inverse address
-4 x 2 diodes for AND array (1-of-4)
-4 x (3 diodes + 2 x BJTs) for active high enables
-4 x (8 x 4) diodes for 4 AND arrays (with enables)
-32 diodes + 32 BJTs for row drivers
-16 diodes + 16 BJTs for output buffers
-Total: 216 diodes + 66 BJTs

### 32 x 16 Two Stage
1-of-8 followed by 8 x 1-of-4
-10 diodes + 5 BJTs for buffered inverse address
-8 x 3 diodes for AND array (1-of-8)
-8 x (3 diodes + 2 x BJTs) for active high enables
-8 x (4 x 3) diodes for 8 AND arrays (with enables)
-32 diodes + 32 BJTs for row drivers
-16 diodes + 16 BJTs for output buffers
-Total: 202 diodes + 69 BJTs

### 32 x 16 Two Stage Diode-Logic
Most Diode Logic (DL) references advise against more than one level. So, no idea how this might work. 1-of-8 and 1-of-4 feeding into 32 x 2-input ANDs
-10 diodes + 5 BJTs for buffered inverse address
-8 x 3 diodes for AND array 1 (1-of-8) (A5..A3)
-4 x 2 diodes for AND array 2 (1-of-4) (A2..A1)
-32 x 2 diodes for 2nd level 2-input AND gates
-32 x 2 diodes (in series) + 32 BJTs for row drivers
-16 diodes + 16 BJTs for output buffers
-Total: 186 diodes + 53 BJTs
