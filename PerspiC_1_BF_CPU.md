# Perspicuous Computer 1 BF CPU

Perspicuous Computer 1 BF CPU is one of several CPUs that can be used with PerspiC 1. PerspiC 1 BF CPU implements a tokenised version of the 8-instruction BF language.

## Architecture

The PerspiC 1 BF CPU is a semi-Harvard architecture, with both the instruction and data memories sharing an address bus, but not an address space (instructions cannot be fetched from data memory and the ALU cannot manipulate data from the program memory).
PerspiC 1 BF CPU will use a microcoded sub-machine code, using a PerspiC 1 ROM to store the microcode. It will also use a PerspiC 1 RAM to implement the registers.
Instructions

The 8 BF instructions have implied operands only and can be encoded in 3 bits.
The instructions are:
- right - move data pointer one cell to the right (BF ">")
- left - move data pointer one cell to the left (BF "<")
- inc - increment the value at the data pointer by one (BF "+")
- dec - decrement the value at the data pointer by one (BF "-")
- while - if value at data pointer is 0, jump to matching end (BF "[")
- end - if value at data pointer is not 0, jump to the matching while (BF "]")
- in - input an 8-bit value from the input port - block if not ready (BF ",")
- out - output an 8-bit value to the output port - block if not ready (BF ".")

## Instruction packing
Because the PerspiC 1 ROM can be up to 16 bits wide, we can pack up to 5 3-bit BF instructions into each word of ROM memory. This is a form of caching, fetching a "line" of 5 instructions at a time.

## Arithmetic Logic Unit
The ALU is 4-bits wide, so dealing with 8-bit (or more) data requires multiple operations. The ALU is a Discrete 74181 modelled on the ubiquitous 74181 4-bit ALU.
Operations
The only operations required from the PerspiC 1 BF CPU's ALU is increment and decrement. During reset sequencing, the ALU can also participate in generating 0 values to be written to all registers and data memory.

## Registers

PerspiC 1 BF CPU requires 3 main registers:
- instruction pointer
- data pointer (index register)
- loop depth counter

These registers are implemented as triple-port RAM using a PerspiC 1 RAM board operating as the CPU register file.
The write (input) port is connected directly to the output of the ALU's temp register.
The 8-bit read port is connected directly as the lower byte of the address bus to the instruction and data memories.
The 4-bit read port is connected as an input to the ALU.

## Instruction Pointer
The only operations available on the Instruction Pointer are increment and decrement. The instruction pointer is implemented as a counter and a register:
a 3-bit 0..4 up-down counter to demultiplex the 16-bit instruction line into 5 instructions, and
an 8- or 16-bit (2 x 8-bit) register implemented in the register file board.
The register component is only ever modified if the 3-bit counter overflows or underflows. At such a time, the ALU is used to increment or decrement the register 4-bits at a time, and then a 16-bit instruction "line" fetch operation will occur to read the next (previous) line of instructions into the instruction fetch register.
Underflow of this register (only possible when processing an "end" instruction), signals end of program.
Using an 8-bit register allows up to 256 x 5 = 1280 instructions (and requires 8 PerspiC 1 ROM boards). A 16-bit register would allow up to 327680 instructions (and require _lots_ of ROM boards!).

## Data Pointer
The data pointer is an 8-bit or 16-bit (2 x 8-bit) register implemented in the register file board. Like the other registers, it is only ever incremented or decremented, plus cleared at reset.
Using an 8-bit register allows up to 256 data locations. The BF specification calls for 30000, so the 16-bit data register can be used for this, but would require 4096 PerspiC 1 RAM boards! Even 256 bytes will require 32 boards!
Loop Depth Counter
When processing "while" and "end" instructions, it is necessary to keep track of matching pairs of such instructions. This 4-bit or 8-bit register does this, allowing maximum loop depths of 15 or 255. No particular care is taken to check for overflow, so it is up to the programmer to ensure that maximum loop depths are not exceeded.

