# VLSI Design Course

## Objective

The objective of VLSI physical design for ASICs is to convert a logical RTL design into a physical layout suitable for fabrication. This process ensures the circuit's functionality aligns with design constraints, performance goals, and manufacturing standards.

## Table of Contents
#### 1) Installation Instructions
#### 2) DAY 1

**Introduction to RISCV ISA and GNU Compiler Toolchain**
+ Introduction to Basic Keywords
+ Labwork for RISCV Toolchain
+ Integer Number Representation

#### 3) DAY 2
+ Application Binary Interface
+ Labwork using ABI Function Calls

## Installation Instructions (MacOS with ARM Processors)

```
brew tap riscv-software-src/riscv
brew install riscv-tools
```
The second command will take about 1 Hr to complete (Depending on your network speed), and may appear stuck. The installer will work, please have patience. 

<br>

## DAY 1 
<details>
<summary> Day 1 Lab Work</summary>

Writing C Program using Nano
```nano p1.c```
### Code 1: Sum of numbers from 1 to N:

Code to sum the numbers from 1 to N:

```c
#include<stdio.h>

int main(){
	int i, sum=0, n=111;
	for (i=1;i<=n; ++i) {
	sum +=i;
	}
	printf("Sum of numbers from 1 to %d is %d \n",n,sum);
	return 0;
}
```
To exit the editor, press `Ctrl + X` and `y` to save the file.

To compile for host system (Apple Silicon M2), using GCC
```
gcc p1.c -o p1.o
./p1.out

```
To compile for RISC-V

```
riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o p1.o p1.c
```
<img width="1680" alt="image" src="https://github.com/TejasKaranji/pes_asic_class/assets/36126927/9729e14c-9fda-409f-b08a-ead86eecdf0b">

using ```ls``` to check that the compiled file exists:

<img width="369" alt="image" src="https://github.com/TejasKaranji/pes_asic_class/assets/36126927/69825fac-8576-4b6b-9f1c-40e121d9d3ff">

To execute compiled code:
```
spike pk p1.o
```

<img width="350" alt="image" src="https://github.com/TejasKaranji/pes_asic_class/assets/36126927/74215dc4-9df0-44d6-9f42-99318aca7b42">

To show assembly code:
```
riscv64-unknown-elf-objdump -d p1.o | less
```

With -O1 optimization:

<img width="1680" alt="image" src="https://github.com/TejasKaranji/pes_asic_class/assets/36126927/bb2d103a-f1c6-4dd9-a21e-62e7496f0c4d">

<br>With -Ofast optimization:

<img width="1680" alt="image" src="https://github.com/TejasKaranji/pes_asic_class/assets/36126927/4d68b415-dda1-4ca1-82d9-3dae7a16245f">

***Using Spike to simulate and debug:***

To simulate:
```
spike pk p1.c
```
 <img width="352" alt="image" src="https://github.com/TejasKaranji/pes_asic_class/assets/36126927/aa7a9e66-5b41-4378-b26f-2dec22fdc223">

To Debug: 
```
spike -d pk p1.c
```
Press `ENTER` to show registers line by line<br>
Press `q` to exit the debugger


Contents of the register are shown as in the image
<img width="1680" alt="image" src="https://github.com/TejasKaranji/pes_asic_class/assets/36126927/a9712785-91ea-4692-8703-a1804c1c69a3">

### Integer number representation

- Range of Unsigned numbers : [0, (2^n)-1 ]
* Range of signed numbes : Positive : [0 , 2^(n-1)-1]
                         Negative : [-1 to 2^(n-1)]

Unsigned 64-Bit number:

```
#include <stdio.h>
#include <math.h>

int main(){
	unsigned long long int max = (unsigned long long int) (pow(2,64) -1);
	unsigned long long int min = (unsigned long long int) (pow(2,64) *(-1));
	printf("lowest number represented by unsigned 64-bit integer is %llu\n",min);
	printf("highest number represented by unsigned 64-bit integer is %llu\n",max);
	return 0;
}
```

Output:

<img width="1680" alt="image" src="https://github.com/TejasKaranji/pes_asic_class/assets/36126927/af294f51-d737-48a6-8134-9ecba09d5dd7">

Signed 64-Bit Number

```
#include <stdio.h>
#include <math.h>

int main(){
	long long int max = (long long int) (pow(2,63) -1);
	long long int min = (long long int) (pow(2,63) *(-1));
	printf("lowest number represented by signed 64-bit integer is %lld\n",min);
	printf("highest number represented by signed 64-bit integer is %lld\n",max);
	return 0;
}
```
Output:
<img width="1680" alt="image" src="https://github.com/TejasKaranji/pes_asic_class/assets/36126927/a885d455-bba9-410e-a43d-bbf90386e8f8">
</details>

## DAY 2
<details>
	<summary> Day 2 Lab Work</summary>

 - ABI names for registers serve as a standardized way to designate the purpose and usage of specific registers within a software ecosystem. These names play a critical role in maintaining compatibility, optimizing code generation, and facilitating communication between different software components.


 *** Lab work using ABI Function calls ***

Code:
``` c
 #include <stdio.h>
  
  extern int load(int x, int y);
  
  int main()
  {
    int result = 0;
    int count = 9;
    result = load(0x0, count+1);
    printf("Sum of numbers from 1 to 9 is %d\n", result);
  }
```
Compiled assembly file:

``` s
.section .text
.global load
.type load, @function
load:
add a4, a0, zero
add a2, a0, a1
add a3, a0, zero
loop:
add a4, a3, a4
addi a3, a3, 1
blt a3, a2, loop
add a0, a4, zero
ret
```
## BASICS :

Instructions that act on signed or unsigned integers are called Base Integer Instructions
There are 47 Base Integer Instructions present in RISC-V ISA

### Types of Instruction based on encoding format

1. **R-Type (Register-Type):**
   - These instructions operate on registers and have a fixed format for their operands.
   - Examples: ADD, SUB, AND, OR, XOR, SLL, SRL, SRA, SLT, SLTU

2. **I-Type (Immediate-Type):**
   - These instructions have an immediate operand and one register operand.
   - Examples: ADDI, SLTI, SLTIU, XORI, ORI, ANDI, SLLI, SRLI, SRAI, LB, LH, LW, LBU, LHU, JALR

3. **S-Type (Store-Type):**
   - These instructions are used for storing values from registers to memory.
   - Examples: SB, SH, SW

4. **B-Type (Branch-Type):**
   - These instructions perform conditional branching based on comparisons.
   - Examples: BEQ, BNE, BLT, BGE, BLTU, BGEU

5. **U-Type (Upper Immediate-Type):**
   - These instructions have a larger immediate field for encoding larger constants.
   - Examples: LUI, AUIPC

6. **J-Type (Jump-Type):**
   - These instructions are used for unconditional jumps and function calls.
   - Examples: JAL

## Labwork using ABI Function Calls

### Algorithm for C Program using ASM
- Incorporating assembly language code into a C program can be done using inline assembly or by linking separate assemblers from memory

To store 64 bits of data from mem to reg, we use 8*8bit stores ie., m[0],m[1]......m[7].

RISC-V uses Little Endian format to store the data ie., Least significant Byte is stored in m[0]

Ibly files with your C code.
- When you call an assembly function from your C code, the C calling convention is followed, including pushing arguments onto the stack or passing them in registers as required.
- The program executes the assembly function, following the assembly instructions you've provided.

![image](https://github.com/TejasKaranji/pes_asic_class/assets/36126927/b3c7de8a-bffe-4560-9d1e-00df5323dbbe)

C Code is written seperately from assembly code. In the assermbly file, declare functions with signatures that match the calling conventions of the platform.
</details>

## Day 3
<details>
	<summary> Day 3: Introduction to Verilog RTL Design and synthesis</summary>

 ## Installation (MacOS with ARM Processors)

Installation of iVerilog and GTKWave can be done using MacPorts.

1. Install the right version of MacPorts from macports.org (OS Specific)
2. Once the tool is installed, open terminal and type `sudo port -v install iverilog`
3. `brew tap homebrew/cask`
4. `brew install gtkwave --cask`
5. If the OS complains about the app being unverified when you try to run it, use the command: `open -a gtkwave xxxx.vcd`

## Labs:
** Simulation
1. Clone the Repo <break> `git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git`
2. cd into the cloned repo <break> `cd sky130RTLDesignAndSynthesisWorkshop/verilog_files`
3.  

</details>
