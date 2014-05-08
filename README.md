CE5
===

MIPS Architecture

### Task 1: Simple Assembly Program
addi $S0, $0, 44

addi $S1, $0, -37

add $S2, $S0, $S1

sw $S2, 0x54($0)

### Task 2: Machine Code
| INSTRUCTION        	| BINARY MACHINE CODE                   	| HEX CONV  	|
|-------------------	|---------------------------------------	|------------	|
| addi $s0, $0, 44  	| 001000 00000 10000 0000000000101100   	| 0x2010002C 	|
| addi $s1, $0, -37 	| 001000 00000 10001 1111111111011011   	| 0x2011FFDB 	|
| add $s2, $s0, $s1 	| 000000 10000 10001 10010 00000 100000 	| 0x02119020 	|
| sw $s2, 0x45($0)  	| 101011 00000 10010 0000000001010100   	| 0xAC120054 	|

![Waveform Up to 40 ns](https://github.com/KevinCabusora/CE5/blob/master/Waveform_Screenshot.PNG?raw=true "Image")

In the first clock cycle, the value in wd3 is 44, and in the next clock cycle it is -37, or the 2's complement of 37.  This corresponds to the two addi functions.  WD3 also corresponds with the register file in the MIPS architecture.  The next value in the next clock cycle is 7, which is the summation of the two terms stored in $s0 and $s1.  In the next clock cycle, the sum is loaded into the hex register 0x54, as demonstrated by the value of WD3 in the final clock cycle.

This screenshot only went to 40ns, because there were only four operations executed, corresponding to four clock cycles.

Therefore, this program works.

### Task 3: Adding ORI
| INSTRUCTION        	| BINARY MACHINE CODE                   	| HEX CONV  	|
|-------------------	|---------------------------------------	|------------	|
| addi $s0, $0, 44  	| 001000 00000 10000 0000000000101100   	| 0x2010002C 	|
| addi $s1, $0, -37 	| 001000 00000 10001 1111111111011011   	| 0x2011FFDB 	|
| add $s2, $s0, $s1 	| 000000 10000 10001 10010 00000 100000 	| 0x02119020 	|
| ori $s3, $s2, x8000 | 001101 10010 10011 1000000000000000     | 0x36538000  |
| sw $s2, 0x45($0)  	| 101011 00000 10010 0000000001010100   	| 0xAC120054 	|
