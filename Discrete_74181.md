# Discrete 74181
According to Wikipedia, the "74181 is a bit slice arithmetic logic unit (ALU)" and is the "first complete ALU on a single chip".

The Discrete 74181 will be modules of the Discrete CPU and will be compatible with a 74181, albeit a lot slower and consume more power.

## Analysis of 74181
The 74181 is a fully combinatorial logic circuit with 14 inputs and 8 outputs. Naively, this could be implemented with a 16k x 8 lookup ROM device, which would exhibit a fixed propagation delay for all input cases.

However, the ~F0 output is actually only dependent upon 8 inputs (256 x 1 lookup), the ~F1 by 10 bits of input (1024 x 1 lookup), the ~F2 input by 12 bits (4096 x 1) and the other 5 by all 14 inputs (16k x 5).

Looking at the datasheets from Signetics (local copy) or Fairchild (local copy), it is plain that the device is actually made of 4 identical logic elements, one for each bit, all feeding into a 4-bit full-adder.

Each of these input logic elements has 6 inputs: ~An, ~Bn, S0, S1, S2 and S3 (they each share S0, S1, S2 and S3) and produce two outputs, which we'll label In and Jn.

```
Input Logic Truth Table

S3	S2	S1	S0	In	Jn
-------------------------------------------
0	0	0	0	An	1
0	0	0	1	An.Bn	1
0	0	1	0	An.~Bn	1
0	0	1	1	0	1
```
