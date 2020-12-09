# [CS 61C - Great Ideas in Computer Architecture (Machine Structures)](https://cs61c.org/fa20/)
A study guide for success.

# The Six Great Ideas in Computer Architecture
1. Abstraction (Layers of Representation/Interpretation) 
2. Moore’s Law 
3. Principle of Locality/Memory Hierarchy 
4. Parallelism 
5. Performance Measurement & Improvement 
6. Dependability via Redundancy

## Module 1 - Number Representation

![bits](https://i.pinimg.com/originals/00/2d/7e/002d7e638fb463fb7a266f5ffc7ac47d.gif)

* Ain't no such thing as free lunch. Bits can represent *ANYTHING*. We've seen it represent `int`, `float`, `char` (ASCII), and even RISC-V binary instruction! 
* Nature is *analog* and computers are known to be *discrete*, yet we will learn how we try to mimic *analog-ness* with different types of representions and abstractions of the computer hardware throughout the course.
* Remember that N bits represebts at most <img src="https://latex.codecogs.com/gif.latex?2%5En"> things in decimal.
* A byte is 8 bits (in hex, this would be two hex numbers e.g. 0xA5)
* An **overflow** occurs when we step outside the binary odometer when doing arithmetic (addition, subtraction, multiplication, division, etc)

Here is a list of charts and diagram that are helpful, but even more helpful if it is memorized:

| Decimal | Hex | Binary |
|:-------:|:---:|:------:|
|    0    | 0x0 | 0b0000 |
|    1    | 0x1 | 0b0001 |
|    2    | 0x2 | 0b0010 |
|    3    | 0x3 | 0b0011 |
|    4    | 0x4 | 0b0100 |
|    5    | 0x5 | 0b0101 |
|    6    | 0x6 | 0b0110 |
|    7    | 0x7 | 0b0111 |
|    8    | 0x8 | 0b1000 |
|    9    | 0x9 | 0b1001 |
|    10   | 0xA | 0b1010 |
|    11   | 0xB | 0b1011 |
|    12   | 0xC | 0b1100 |
|    13   | 0xD | 0b1101 |
|    14   | 0xE | 0b1110 |
|    15   | 0xF | 0b1111 |

| Bit Index | Value |
|:---------:|:-----:|
|     0     |   1   |
|     1     |   2   |
|     2     |   4   |
|     3     |   8   |
|     4     |   16  |
|     5     |   32  |
|     6     |   64  |
|     7     |   128 |

| Hex Index | Value |
|:---------:|:-----:|
|     0     |   1   |
|     1     |   16  |
|     2     |   256 |
|     3     |  4096 |

Here lies the different type of number representations.

### Unsigned
* C: `unsigned int`, `uint8_t`, `uint32_t`, ...
* Only non-negative numbers (represents only 0 and positive numbers)
* Range: <img src='https://latex.codecogs.com/gif.latex?0%20%5Cleq%20x%20%5Cleq%202%5En%20-%201'>

### Sign and Magnitude
* MSB is set to the sign, (0 is positive, 1 is negative)
* Drawback is that now we have **two zeroes**! (-0 and +0)
* Range: <img src='https://latex.codecogs.com/gif.latex?-2%5E%7Bn-1%7D-1%20%5Cleq%20x%20%5Cleq%202%5E%7Bn-1%7D-1'>
* Rarely used today except in signal processors

### One's Complement
* Positive has leading 0s and negatives have leading 1s. Want to flip the sign? Then flip the bits!
* Drawback is still **two zeroes**! (-0 as 0xFFFF and +0 as 0x0000)
* Range: <img src='https://latex.codecogs.com/gif.latex?-2%5E%7Bn-1%7D-1%20%5Cleq%20x%20%5Cleq%202%5E%7Bn-1%7D-1'> (Notice that is the same as sign and magnitude)

### Two's Complement
* C: `int`, `int8_t`, `int32_t`, ... (Two's complement is the standard implementation of integers in hardware today)
* MSB determines the sign, but the number is really on a bias with MSB set to negative.
* Positive numbers are easy to parse, negative numbers use a trick: flip the bits and add 1!. You can easily switch between signs this way!
* Only **one zero**!
* Range: <img src='https://latex.codecogs.com/gif.latex?-2%5E%7Bn%20-%201%7D%20%5Cleq%20x%20%5Cleq%202%5E%7Bn%20-%201%7D-1'>

### Bias Encoding
* Same as two's complement, but just that we set a bias with a number we choose.
* Drawbacks: Not as clear what the bias can be, which is why we choose the MSB for Two's Complement for convenience.

### Floating Point (IEEE 754)
* Mantissa: Always starts with 1
* Exponent: In a bias of -127
* Radix (Base): Always 2 since we are working in binary
* Binary Point: Where the decimal point is
* Additional things we need to consider:
    * Now we have *underflow*. Sometimes negative exponent is larger than represented in 8-bit exponent field 
    *
* Range: 1.2 x 10-38 to as large as 3.4 x 1038
* Two Zeros: 
    * +0: `0b0 00000000 00000000000000000000000`
    * -0: `0b1 00000000 00000000000000000000000`

## Module 2 - C Programming

![C](https://upload.wikimedia.org/wikipedia/commons/thumb/3/35/The_C_Programming_Language_logo.svg/1200px-The_C_Programming_Language_logo.svg.png)
### Pointers

## Module 3 - RISC-V ISA

![RISC-V](https://pbs.twimg.com/profile_images/1095867248982425600/XIoYfG8t_400x400.png)

Mostly should be comfortable territory if we have done the projects and labs properly.

### RISC-y Tips
* Refer to the green card. RISC-V is simple and has only 47 instructions. Pretty amazing what you can do with just 47 instructions!
* In this class and in RV32, a word is 4 bytes (32 bits)
* RISC-V is in **Little Endian** (*One little, two little, three little... Endian*)
    * Memory stored/filled from LSB to MSB
    * ```s
      #sp stores 0x00C2
      lw t0 0(sp)
      #memory:
      # |  3  |  2  |  1  |  0  |
      # | 0x0 | 0x0 | 0xC | 0x2 |
      ```
    * This is convenient, but note that for strings it will show backwards in a debugger like `venus`. Don't get confused with this!
* Be wary of sign extension. Instructions like `lb` will end up sign extending because registers are always set to 32 bits. We also have `lbu` that will load to a register unsigned. Notice that we do not have `sbu`, think about why that is!
* ***DON'T FORGET CALLING CONVENTION!*** When we throw a party, we make sure to put everything back where it's supposed to be. When we raid a fridge, we make it unnoticable to the next person who opens it. When in doubt of the specifics, refer to the calling convention notes.
    1. Put arguments in a place where function can access
them 
    2. Transfer control to function 
    3. Acquire (local) storage resources needed for function 
    4. Perform desired task of the function 
    5. Put return value in a place where calling code can
access it and restore any registers you used; release
local storage 
    6. Return control to point of origin, since a function can
be called from several points in a program
* Just a few more notes about calling/register convention:
    1. Preserved across function call
        * Caller can rely on values being unchanged
        * sp, gp, tp, “saved registers” s0- s11 (s0 is also fp)
    2. Not preserved across function call
        * Caller cannot rely on values being unchanged
        * Argument/return registers a0-a7,ra, “temporary registers” t0-t6
* Converting to binary code? Just refer to the green card. Be aware that `SB` and `UJ` Type bits are scrambled for the immediate. This is done on purpose because it makes sense on the hardware level. If ever translating, just always double check.
* Pseudoinstructions just make it easier for hand made assembly development. Some here to name a few:
    * `j Label` :   `jal x0, Label`
    * `li t0, 61` :   `addi t0, t0, 61`
    * `mv t0, t1` :   `addi t0 t1 x0`
* **Load from** Memory to Register
* **Store** from Register **to** Memory
* Branches require immediate... That means changing the code doesn't change the funciontionality of branching unless the label was shifted. This means branching is `PC + imm` and it's relative addressing, not absolute.
* C vs. RISC-V:
    * In C, we worry about three things in memory:
        * Static: Variables declared once per program, cease to
exist only after execution completes - e.g., C globals 
        * Heap: Variables declared dynamically via malloc 
        * Stack: Space to be used by procedure during execution;
this is where we can save register values
    * In RISC-V, there are four things to worry about:
        * Stack starts in high memory and grows down (function calls)
        * RV32 programs (text segment) in low end
        * static data segment (constants and other static variables) above text
for static variables
            * RISC-V convention global pointer (gp) points to static
            * RV32 gp = `0x1000_0000`
        * Heap above static for data structures that grow and shrink; grows
up to high addresses

## Module 4 - CALL
**C**ompiling, **A**ssembling, **L**inking, Loading (CAL, go bears!)

![CALL](https://www.oreilly.com/library/view/programming-embedded-systems/0596009836/httpatomoreillycomsourceoreillyimages70224.png)

Here we start connecting Low Level Programming with RISC-V ISA with higher level languages such as C and even up to Python!

* Intepretation is slow! But gives tradeoff in faster development (e.g. Python). Ain't no free lunch!
* We did this in 61A with Scheme (e.g. write an interpreter for Scheme in Python)
* Slow, but allows us to emulate things, such as `venus`. Also allows things like compatibility/emulation (e.g. x86->ARM with the newer M1 Macs, play console games on PC hardware, etc...)

### Compiling
* Output may contain pseudoinstructions! (It's not machine understandable yet until we assemble)
* Sometimes, we can skip the compilation process if we are hand authoring assembly code!

### Assembling
* Takes in assembly code (`foo.s`) and outputs object files (`foo.o`) (this is *true* assembly)
* Reads and Uses Directives
    * `.text`: Subsequent items put in user text segment (machine code)
    * `.data`: Subsequent items put in user data segment (source file data in binary)
    * `.globl sym`: Declares `sym` global and can be referenced from other files
    * `.string str`: Store the string `str` in memory and null-terminate it 
    * `.word w1…wn`: Store the n 32 bit quantities in successive memory words
* **Replace Pseudo-instructions**
* Produce Machine Language
* Creates Object File
    * object file header: size and position of the other pieces of the object file 
    * text segment: the machine code 
    * data segment: binary representation of the static data in the source file
    * relocation information: identifies lines of code that need to be fixed up later 
    * symbol table: list of this file’s labels and static data that can be referenced 
    * debugging information 
* Also includes:
    * Symbol Table
        * Labels: function calling
        * Data: Anything in the `.data` section; variables which may be accessed across files
    * Relocation Table
        * List of “items” whose address this file needs 
        * Any absolute label jumped to : `jal`, `jalr`
        * Any piece of data in the static section
* Need two passes of assembling to solve the conflict of forward reference. (e.g. we branch ahead)

### Linking
* `gcc` usually compiles and stops up to here to produce the `./a.out` to be loaded into memory.
* Input: Object code files, information tables (e.g., `foo.o`, `libc.o` for RISC-V) 
* Output: Executable code (e.g. `./a.out`)
* Combines several object (`.o`) files into a single executable (“linking”)
* Enable separate compilation of files
* This is a statically linked approach
* Linking steps:
    1. Take **text** segment from each `.o` file and put them together
    2. Take **data** segment from each `.o` file, put them together, and concatenate this onto end of text segments
    3. Resolve references
        * Go through relocation table and handle each entry
        * Fill in all absolute addresses
            1. PC-relative address: Don't need to relocate! (`beq`, `bne`, `jal`, `auipc`, `add`)
            2. Absolute function address: Always relocate (`auipc`, `jal`)
            3. External function address: Always relocate (`auipc`, `jal`)
            4. Static data reference: Always relocate (`lui`, `addi`)

### Loading
* Finally, we can run our executable `./a.out`!
* Reads executable file’s header to determine size of text and data segments 
* Creates new address space for program large enough to hold text and data segments, along with a stack segment 
* Copies instructions + data from executable file into the new address space 
* Copies arguments passed to the program onto the stack 
* Initializes machine registers
* Jumps to start-up routine that copies program’s arguments from stack to registers & sets the PC

## Module 5 - Synchronous Digital Systems

![circuits](https://i.pinimg.com/originals/ef/81/3b/ef813b29546dcfaee3e5aaca3f408dfe.jpg)

Two Types of Circuits
* Combinational Logic
* State elements

### Transistors
**D**an **G**arcia **S**ays
Drain, Gate, Source
* They are the foundation of computers
* on or off
    * if voltage is lower or higher than a source, switch on or off appropriately.

### State Circuits
* Accumulator
    * Just add and accumulate the number of bits.
    * We need to add a state element to keep track of variables, thus a transistor is held between the feedback loop
* Registers
    * They are essentially n flip-flops in parallel! (32 flip-flops make a 32 bit register)
    * **D** is “data”, **Q** is “output”, **clk** is for clock
    * Registers are triggered by the *rising edge* of a clock cycle!
        * Once we hit rising edge, we sample whatever is reading into Data. 
        * Q is just the output of the flip-flop/register
        * Expect *delays*, nothing is instant (e.g. propogation delays)
* Pipelining 
    * setup time: input must be stable before rising edge of a clock
    * hold time: how long input is stable after rising edge
    * clk-to-q: how long it takes for output to change
    * maximum clock frequency
        * `Frequency = 1 / period`
        * `Max Delay = CLK-to-Q Delay + CL Delay + Setup Time`
    * Add registers to speed up, because you get less delays. We will see why this is the case when we pipeling our RISC-V datapath!
* Finite State Machine (FSM)
    * state transition diagram
    * This is where our other theoretical and mathematical CS background comes into play! Nothing different
    * General Model:
        * Collection of CL blocks separated by registers. 
        * Registers may be back-to-back and CL blocks may be back-to-back. 
        * Feedback is optional. 
        * Clock signal(s) connects only to clock input of registers.

### Combinational Logic
* Just know your boolean logic and boolean gates!

![gates](https://d3i71xaburhd42.cloudfront.net/f8c9cdd0da46e1631a811b9c0c91ce26a0a34b75/2-Figure1-1.png)

![boolalg](https://studytronics.weebly.com/uploads/4/4/3/7/44372217/793892584.gif)

## Module 6 - RISC-V CPU Datapath, Control, Pipelining

![cpu logic](https://www.pcgamesn.com/wp-content/uploads/2019/09/intel-cpu-architecture.jpg)

Doing well on the CPU design project probably means we are very familiar with the RISC-V datapath, control, and pipelining. But a few more tips to help us on trickier concepts and questions!

### Datapath
* Five Stages of the Datapath:
    * Stage 1: Instruction Fetch (IF)
    * Stage 2: Instruction Decode (ID) 
    * Stage 3: Execute (EX) - ALU (Arithmetic-Logic Unit) 
    * Stage 4: Memory Access (MEM) 
    * Stage 5: Write Back to Register (WB)
* Remember that circuits are either executed by logic gates, or ROM (Read Only Memory) usually for debugging

### Pipelining
* Hazards
    1. Structural hazard
        * A required resource is busy (e.g. needed in multiple stages)
            * Solution: Stall 
            * Solution: Add more hardware.
        * Realize that we do not have any regfile hazards because we are essentially using caches. Usually L1d and L1i as we have seen from performance project. This is the "Add more hardware" solution!
    2. Data hazard
        * Data dependency between instructions
            * Solution: Stall (Bubbling or `nop`, `nop` is better as we just tell the CPU to skip a beat and do nothing)
        * Need to wait for previous instruction to complete its data read/write
            * Solution: Forwarding. Brute force and feed it directly to the datapath rather than having a register stuck in between
        * Solution: We can also rearrange code to prevent stalls from happening
    3. Control hazard
        * Flow of execution depends on previous instruction (usually a problem with branches, where we have to flush out instructions for branch not taken)
            * Solution: We use branch prediction!
* Iron Law of Processor Performance: CPI = Cycles / Instruction = (Time / Program) / ((Instruction / Program) (Time / Cycle))

## Module 7 - Caches

![cache](https://lh3.googleusercontent.com/proxy/uAZ4DjFTVZINqwJEA5jfv9g96foWR_Je_ANO8lMet4ileir6_pOXNdDiEZ9IvPe3wQdzDZEfOT91onZpJVNmMlq00m1yIApBL3Kt7D1JsOnPmZ0g2jreojJlQOdLKmbZi8SYovfAQTGyX8hV8IUTcHFt1gewak5YnVHdiJTpkJ5VrQxkZB9nZc9Y-1Y6iji-)

| Name | Abbreviation | Size |
|:----:|:------------:|:----:|
| Kibi | Ki           | 2^10 |
| Mebi | Mi           | 2^20 |
| Gibi | Gi           | 2^30 |
| Tebi | Ti           | 2^40 |
| Pebi | Pi           | 2^50 |
| Exbi | Ei           | 2^60 |
| Zebi | Zi           | 2^70 |
| Yobi | Yi           | 2^80 |

* Revolves on the concept of temporal and spatial locality! 
* Cache is controlled by hardware, there is no software interface for use to interact with

### Direct Mapped Cache

### Fully Associative Cache

### Set Associative Cache

## Module 8 - Virtual Memory

![VM](https://www.enterprisestorageforum.com/imagesvr_ce/6466/VirtualMemory.png)

## Module 9 - Flynn's Taxonomy

![simd](https://software.intel.com/content/dam/develop/external/us/en/images/37207-183287.gif)

## Module 9 - Thread Level Parallelism

![threads](https://cdn.wccftech.com/wp-content/uploads/2020/11/Intel-AMD-CPU-Vs-Apple-M1-Feature.jpg)

## Module 10 - Map Reduce Spark

![spark](https://upload.wikimedia.org/wikipedia/commons/f/f3/Apache_Spark_logo.svg)

## Module 11 - Conclusion


Sources: Notes and study guide compiled from notes, lectures, and discussions publicly availiable.

![61c](https://cs61c.org/assets/icon-small.png)