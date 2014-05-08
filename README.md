CE5
===

MIPS Architecture

C3C Kevin Cabusora, Dr. Neebel, ECE281

### Task 1: Simple Assembly Program
addi $S0, $0, 44

addi $S1, $0, -37

add $S2, $S0, $S1

sw $S2, 0x54($0)

Essentially, the first step of the program is to do an add immediate of 0 and 44, and storing it in $S0.  This function essentially loaded 44 into the register $S0.

The same was performed for the second ADDI function.  However, the binary value stored was the 2's complement of 37.

The third function was an R-type function, adding the values in registers $s0 and $s1 and storing them in the register $s2.

The last function stored the word or value of $s2 into the hex value 0x54, or 84 in decimal.

### Task 2: Machine Code

##### Markdown Table with Machine Code

| INSTRUCTION        	| BINARY MACHINE CODE                   	| HEX CONV  	|
|-------------------	|---------------------------------------	|------------	|
| addi $s0, $0, 44  	| 001000 00000 10000 0000000000101100   	| 0x2010002C 	|
| addi $s1, $0, -37 	| 001000 00000 10001 1111111111011011   	| 0x2011FFDB 	|
| add $s2, $s0, $s1 	| 000000 10000 10001 10010 00000 100000 	| 0x02119020 	|
| sw $s2, 0x45($0)  	| 101011 00000 10010 0000000001010100   	| 0xAC120054 	|

##### Testbench Implementation

To implement this machine code, I put it into the given testbench in the syntax provided by the handout.

```
		-- addi $s0, $0, 44
		instr <= X"2010002C";
		wait for clk_period;
		
		-- addi $s1, $0, -37
		instr <= X"2011FFDB";
		wait for clk_period;
		
		-- add $s2, $s0, $s1
		instr <= X"02119020";
		wait for clk_period;
		
		-- sw $s2, 0x45($0)
		instr <= X"AC120054";
		wait for clk_period; 
```
##### Waveform

![Waveform Up to 40 ns](https://github.com/KevinCabusora/CE5/blob/master/Waveform_Screenshot.PNG?raw=true "Image")

Originally, the simulation waveform would not show up even after editing the txt file for the mips_waveform_wdb.  I discovered with some help that it was due to my naming the file CE5_testbench, where the file was looking for a module named mips_tb.

In the first clock cycle, the value in wd3 is 44, and in the next clock cycle it is -37, or the 2's complement of 37.  This corresponds to the two addi functions.  WD3 also corresponds with the register file in the MIPS architecture.  The next value in the next clock cycle is 7, which is the summation of the two terms stored in $s0 and $s1.  In the next clock cycle, the sum is loaded into the hex register 0x54, as demonstrated by the value of WD3 in the final clock cycle.

This screenshot only went to 40ns, because there were only four operations executed, corresponding to four clock cycles.

Therefore, this program works.

### Task 3: Adding ORI

##### Description

The instruction to be included for Task 3 was: ORI $s3, $s2, x8000.

The instruction was an OR immediate, making this an I-Type command.

x8000 converted to 32768, or 1000000000000000 in binary.

The purpose of the ORI function is to OR the value in $s2 (which is currently 7) with x8000, then stores that in register $s3, which the ORI function inherently performs.

The instructions in the lab were to put this ORI function before the SW (store word) function.

##### Markdown Table

| INSTRUCTION        	| BINARY MACHINE CODE                   	| HEX CONV  	|
|-------------------	|---------------------------------------	|------------	|
| addi $s0, $0, 44  	| 001000 00000 10000 0000000000101100   	| 0x2010002C 	|
| addi $s1, $0, -37 	| 001000 00000 10001 1111111111011011   	| 0x2011FFDB 	|
| add $s2, $s0, $s1 	| 000000 10000 10001 10010 00000 100000 	| 0x02119020 	|
| ori $s3, $s2, x8000   | 001101 10010 10011 1000000000000000           | 0x36538000    |
| sw $s2, 0x45($0)  	| 101011 00000 10010 0000000001010100   	| 0xAC120054    |

##### VHDL Additions

The following is the addition I made to the code -- the first being additions in MIPS; the second being changes in the testbench file.

```
-- 2 MIPS.vhd additions
  architecture behave of maindec is
    signal controls: STD_LOGIC_VECTOR(8 downto 0);
  begin
    process(op) begin
     case op is
       when "000000" => controls <= "110000010"; -- Rtype
        when "100011" => controls <= "101001000"; -- LW
       when "101011" => controls <= "001010000"; -- SW
       when "000100" => controls <= "000100001"; -- BEQ
       when "001000" => controls <= "101000000"; -- ADDI
       when "000010" => controls <= "000000100"; -- J
		  when "001101" => controls <= "101000011"; -- ORI
--- Above is the addition made to the architecture for ORI
      when others   => controls <= "---------"; -- illegal op
    end case;
  end process;
  
 --- Next, we go down to the aludec. 
  
  architecture behave of aludec is
begin
  process(aluop, funct) begin
    case aluop is
      when "00" => alucontrol <= "010"; -- add (for lb/sb/addi)
      when "01" => alucontrol <= "110"; -- sub (for beq)
		when "11" => alucontrol <= "001"; -- ORI (from ALU)
--- Above is the addition; refer to the table image I put below to see why.
      when others => case funct is         -- R-type instructions
                         when "100000" => alucontrol <= "010"; -- add (for add)
                         when "100010" => alucontrol <= "110"; -- subtract (for sub)
                         when "100100" => alucontrol <= "000"; -- logical and (for and)
                         when "100101" => alucontrol <= "001"; -- logical or (for or)
                         when "101010" => alucontrol <= "111"; -- set on less (for slt)
                         when others   => alucontrol <= "---"; -- should never happen
                     end case;
  
```


```
-- Testbench, with ORI Addition
		-- addi $s0, $0, 44
		instr <= X"2010002C";
		wait for clk_period;
		
		-- addi $s1, $0, -37
		instr <= X"2011FFDB";
		wait for clk_period;
		
		-- add $s2, $s0, $s1
		instr <= X"02119020";
		wait for clk_period;
		
		-- ori $s3, $s2, x8000
		instr <= X"36538000";
		wait for clk_period;
		
		-- sw $s2, 0x45($0)
		instr <= X"AC120054";
		wait for clk_period; 
```

##### Schematic Additions

To implement this in the schematic, no changes were necessary in the architecture schematic.  The ORI function is inherent in the ALU.

![Schematic Diagram](https://github.com/KevinCabusora/CE5/blob/master/Schematic_ORI.png?raw=true "Image")

Using Appendix B in the textbook, I then filled in the table for the ori instruction.

![Table](https://github.com/KevinCabusora/CE5/blob/master/Functionality_Table_With_ORI.png?raw=true "Image")

##### Waveform; Sanity Check

In testing the testbench, which was changed to include the ORI function.  We now have extended the waveform to 50ns to represent 5 clock cycles.  At t = 30-40ns, the ORI function is clearly present, and the SW function is still the conclusion of the program.  Therefore, the program is correct.

![Waveform With ORI Function](https://github.com/KevinCabusora/CE5/blob/master/Waveform_with_ORI.PNG?raw=true "Image")

### Documentation

I used Appendix B and p. 300 of the textbook to help me with the Tables.

I used a Markdown Table Generator to make these tables.

C3C Jason Pluger helped me understand the purpose of adding the ORI function to the program.

C3C Kyle Jonas helped me understand the changes I should make in the mips.vhd to implement the ORI.  He also assisted in the suggestion that I use the actual name "mips.tb" for the testbench for the mips_waveform txt file to work in isim.




