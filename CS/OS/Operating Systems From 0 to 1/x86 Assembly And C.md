
## x86 Assembly and C

In this chapter we explore assembly language, and how it connects to C. 

&nbsp;


* [objdump](#4.1)
* [Reading the output](#4.2)
* [Intel Manuals](#4.3)
* [Experiment with assembly code](#4.4)
* [Anatomy of an Assembly Instruction](#4.5)
* [Understand an instruction in detail](#4.6)
* [Example: `jmp` instruction](#4.7)
* [Examine Compiled Data](#4.8)
	* [Fundamental Data Types](#4.8.1)
	* [Pointer Data Types](#4.8.2)
	* [Bit Field Data Type](4.8.3)
	* [String Data Members](#4.8.4)
* [Examining Compiled Code](#4.9)
	* [Data Transfer](#4.9.1)
	* [Expressions](#4.9.2)
	* [Stack](#4.9.3)
	* [Automatic Variables](#4.9.4)
	* [Function Call and Return](#4.9.5)
	* [Loop](#4.9.6)
	* [Conditional](#4.9.7)



&nbsp;

### 4.1  objdump <a name="4.1"></a>

_objdump_ is a programs that displays information about object files. Later it will be used to debug. Now we use it to examine how high level source code maps to assembly code.

Suppose we have an executable binary named _hello_ compiled from _hello.c_ that printsd _Hello World_. We can use `-d` to only display assembled contents of executable sections. 

```
$ objdump -d hello
```
&nbsp;


A _section_ is a block of memory that contains either program code or data. A code section can be executed by the CPU while a data one cannot. With the `-d` command, non-executable sections such as _.data_ and _.bss_, debug sections, etc, are not displayed. On the other hand, `-D` displays assembly contents of all sections:
```
$ objdump -D hello
```

&nbsp;


To mix source code and assembly, the binary must be compiled using `-g` option to include source code in it, then add `-S` option :

```
$ objdump -S hello
```

&nbsp;
     
The default syntax used by _objdump_ is AT&T syntax. To chnge it to the Intel syntax do :

```
$ objdump -M intel -D hello
```

When using the `-M` option, the `-D` or `-d` must be explicitly supplied. 

   &nbsp;

We will also write a 32-bit kernel, therefore we will need to compile a 32-bit binary and examine it in 32-bit mode :

```
$ objdump -M i386,intel -D hello
```

`-M i386` tells _objdump_ to display assembly content using the 32-bit layout. 

&nbsp;
      
      
### 4.2 Reading the output <a name="4.2"></a>


At the start of the output, the file format is displayed :

```
hello : file format elf64-x86-64
```

After that, there is a series of dissasembled sections :

```
Dissasembly of section .interp:
...
Dissasembly of section .note.ABI-tag:
...
Dissasembly of section .note.gnu.build-id:
...

etc.
```

Each dissasembly section displays its actual content, a sequence of assembly instructions, with the following format :

```
4004d6:		55			push   rbp
```

`4004d8`   - address of assembly instruction : 0x4004d6.    
`55`	   - instruction in raw hex values : 0x55.       
`push rbp` - assembly instruction. Depending on the section, might be meaningfull or meaningless. Depends on weather they are in a _.text_ section or _.data_. _objdump_ does not distinguish between the two.       

&nbsp;

There is an optional forth collumn that is a comment, appears when there is a reference to an address :

```
lea r12,[rip+0x2008ee] # 600e10 <__frame_dummy_init_array_entry>
```

To inform the reader that the reference address `[rip+0x2008ee]` is 0x600e10, where the variable `__frame_dummy_init_array_entry` is.

&nbsp;

A dissasembled section might also contain ___labels___. A lable is a name given to an assembly instruction. It specifies the purpose of the block to the human reader. For example, the `.text` contain such lables to denote where the code starts. The below example contains two labels, `_start` and `_deregister_tm_clones`. The `_start` function starts at address 4003e0, to the left of the function name. Right below the label we can seee the instruction at address 4003e0. The label is simply a name of a memory address. 

```
00000000004003e0 <_start>:
   4003e0:	31  ed			xor     ebp,ebp
   4003e2:	49  89  d1		mov     r9,rdx
   4003e5:	5e			pop	rsi
   .
   .
   .


0000000000400410 <deregister_tm_clones>:
   400410:	b8 3f 10 60 00 		mov	eax,0x60103f
   400415:	55			push	rbp
   400416:	48 2d 38 10 60 00	sub	rax,ox601038
   .
   .
   .
```


&nbsp;

### 4.3 Intel Manuals  <a name="4.3"></a>


The best way to understand assembly is to understand the underlying computer architecture and what the machine does. To do so, we must rely on information provided by vendors. To understand Intel's instruction set, we need the document : _Intel 64 and IA-32 architectures software developer's manual combined volumes 2A, 2B, 2C and 2D: Instructions set reference, A-Z_. The document can be retrieved [here](https://github.com/GandalfTea/Notebooks/tree/master/CS/OS/Operating%20Systems%20From%200%20to%201/Intel%20Manuals).

Chapter 1: brief information about manual and notations used.       
Chapter 2: in-dept explanation of the anatomy aof an assembly instruction.      
Chapter 3 - 5: every instruction of the _x86_64_ architecture.       
Chapter 6: safer mode extensions.       

&nbsp;

The first volume, _Intel 64 and IA-32 Architecture Software Developer's Manual Volume 1: Basic Architecture_ describes the basic architecture and programming environment of Intel Processors. Here, chapter 5 gives the summary of all intel instructions. 

Read section 1.3 in volume 2, excclude sections 1.3.5 and 1.3.7.

&nbsp;

### 4.4 Experiment with assembly code  <a name="4.4"></a>


To fully understand, you need to write code and see the code as hex numbers. For this purpose we use _nasm_ assembler to write a few lines of assembly code and see the generated code. 

Suppose we want to see the machine code for this:
```
jmp eax
```
We use an editor such as Emacs to create a new file, write the code and save it as _text.asm_. Then, in the terminal:
```
$ nasm -f bin test.asm -o test
```
The `-f` specifies the format of the output, ex. ELF. In this case, the format is _bin_, meaning binary. We can examine the output using this command :

```
$ hd test
```
`hd` short for _hexdump_ is a program that displays the content of a file in hex format. Output :

```
00000000   66  ff  e0			|f..|
00000003   
```

The file only consists of 3 bytes : `66`, `ff`, `e0` which is the equivalent of `jmp eax`. 

&nbsp;

If we were to use _elf_ format :

```
$ nasm -f elf text.asm -o test
```

It would be more challanging to understand because of all the noise :

```
00000000 7f 45 4c 46 01 01 01 00 00 00 00 00 00 00 00 00 |.ELF............|
00000010 01 00 03 00 01 00 00 00 00 00 00 00 00 00 00 00 |................|
00000020 40 00 00 00 00 00 00 00 34 00 00 00 00 00 28 00 |@.......4.....(.|
00000030 05 00 02 00 00 00 00 00 00 00 00 00 00 00 00 00 |................|
00000040 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 |................|
00000060 00 00 00 00 00 00 00 00 01 00 00 00 01 00 00 00 |................|
00000070 06 00 00 00 00 00 00 00 10 01 00 00 02 00 00 00 |................|
00000080 00 00 00 00 00 00 00 00 10 00 00 00 00 00 00 00 |................|
00000090 07 00 00 00 03 00 00 00 00 00 00 00 00 00 00 00 |................|
000000a0 20 01 00 00 21 00 00 00 00 00 00 00 00 00 00 00 | ...!...........|
000000b0 01 00 00 00 00 00 00 00 11 00 00 00 02 00 00 00 |................|
000000c0 00 00 00 00 00 00 00 00 50 01 00 00 30 00 00 00 |........P...0...|
000000d0 04 00 00 00 03 00 00 00 04 00 00 00 10 00 00 00 |................|
000000e0 19 00 00 00 03 00 00 00 00 00 00 00 00 00 00 00 |................|
000000f0 80 01 00 00 0d 00 00 00 00 00 00 00 00 00 00 00 |................|
00000100 01 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 |................|
00000110 ff e0 00 00 00 00 00 00 00 00 00 00 00 00 00 00 |................|
00000120 00 2e 74 65 78 74 00 2e 73 68 73 74 72 74 61 62 |..text..shstrtab|
00000130 00 2e 73 79 6d 74 61 62 00 2e 73 74 72 74 61 62 |..symtab..strtab|
00000140 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 |................|
00000160 01 00 00 00 00 00 00 00 00 00 00 00 04 00 f1 ff |................|
00000170 00 00 00 00 00 00 00 00 00 00 00 00 03 00 01 00 |................|
00000180 00 74 65 73 74 2e 61 73 6d 00 00 00 00 00 00 00 |.disp8-5.asm....|
00000190
```

It is better to use binary to experiment.

Note : using bin format puts _nasm_ by defaut into 16-bit mode. To enable 32-bit code to be generated, we must add this line at the beginning of the _nasm_ source file:
```
bits 32
```

&nbsp;

### 4.5 Anatomy of an Assembly Instruction  <a name="4.5"></a>

This is a simpler introduction to the in-depth view of instruction format before reading chapter 2 from the reference manual. 

All assembly instructions are a fixed size series of bits. The length depends on how complicated it is. Still, they all share a common format describet in the picture below. The parts are :

<img src="https://i.imgur.com/cs6RiNS.png" alt="img" style="zoom: 10%;" />

&nbsp;
        
___Instruction Prefixes___ appear at the beginning of an instruciton. They are optional because in practice, a prefix is just another assembly instruction inserted before another assembly instruction. Instructions with 2 or 3-byte opcodes include prefixes by default. 

       
&nbsp;

___Opcode___ is a unique number that identifies the instruction. Each upcode is given a readable name, e.g. _add_ is 04. When a CPU sees 04 in it's instruction cache, it knows to call the instruction _add_. Opcode can be 1,2 or 3 bytes long and include additional 3-bit field in the _ModR/M_ byte when needed. 

&nbsp;

#### Example:

For this code:  
```
jmp [0x1234]
```
the machine code is :
```
ff 26 34 12
```
The `0xff` is the opcode.        
      

&nbsp;

___ModR/M___ specifies operands of an instruction. Operand can either be a register, a memory location or immediate value. This component is made from 3 parts :

* ___mod___ field, or _modifier_ field, is combined with _r/m_ field for a total of 5 bits of information to encode 32 possible values : 8 registers and 24 addresses.
* ___reg/opcode___ field encodes either a register operand, or extends the _Opcode_ field with 3 more bits.
* ___r/m___ field encodes either a register operand or can be combined with _mod_ field to encode an addressing mode. 

The following tables list all possible 256 values of ___ModR/M___ byte and how each value maps to an addressing mode and a register, in 16-bit and 32-bit modes.

<img src="https://i.imgur.com/Rg2XtD7.png" alt="img" style="zoom: 10%;" />
<img src="https://i.imgur.com/o78P6cE.png" alt="img" style="zoom: 10%;" />


How to read the table :
Have a code you want to look up. Look up the byte value to get the coresponding operands in the row and column. 

&nbsp;
        
#### Example:

An instruction uses this addressing mode:
```
jmp [0x1234]
```
The machine code is:
```
ff 26 34 12
```
`0xff` is the opcode.    
The `0x26` is the _ModR/M_ byte. Looking up in the 16-bit table, the first operand is in the row equivalent to a _disp16_, which means a 16-bit offset. Since the instruction does not have a second operand, the column can be ignored. 

&nbsp;
      
#### Example:
```
add eax, ecx
```
Machine code :
```
66 01 c8
```

The interesting feature of this instruction is that `0x66` is not the opcode. `0x01` is he opcode. The `0x66` is an optional prefix. If the CPU is sqwitched to 32-bit mode, because of this prefix, the instruction operands are limited to 16-bit width. If the CPU is in a 16-bit environment, then 32-bit is considered non-standard and there is a need for a prefix to be executed at this width. Otherwise, if there is none, they are executed at 16-bit. 

The `0xc8` is the _ModR/M_ byte. Lookup in the table at _c8_ value, the first operand is _ax_, the column then gives the second as _cx_. 

&nbsp;

Why is the first operand in the row and the second in the column?

```
 mod  | reg/opcode |    r/m
1 | 1 | 0 | 0 | 1  | 0 | 0 | 0 |
```
This is the ModR/M byte.

The _mod_ field divides addressing modes into 4 different categories. Further combined with the _r/m_ field, exactly one addressing mode can be selected from one of the 24 rows. If an instruction only required one operand, the column can be ignored. Then the _reg/opcode_ field provides an extra register or different variants, if an instruction requires one.

   
   
&nbsp;
   
___SIB___ is _Scale-Index-Base_ byte. This byte encodes ways to calculate the memory position into an elemenent of an array. SIB is based on the formula for calculationg an effective address :
```   
Effective address = scale * index + base 
```
  
___Base___ is the starting address.
___Index___ is an offset into an array.          
___Scale___ is a factor of _index_. It is one of the values 1,2,4,8, any other is invalid. If you use 1, the offset must be calculated manually. For example, to get the address of the _n^th_ element in an array where each element is 12-bytes long, scale must be set to 1 because the length is 12 instead of 1,2,4 or 8 and a compiler must calculate the offset :
```
Effective address = 1*(12*n) + base
```

&nbsp;

Why use SIB?

In this example, an additional `mul` instruction must be executed to get the offset which consumes more than 1 byte, while SIB only consumes 1 byte. If this is scaled, it can be detrimental to te performance of the CPU. 

The values 2,4 and 8 are not random. They map to 16-bit (2 bytes), 32-bit (4 bytes) and 64-bit (8 bytes).
 
Below is the table listing all 256 values of SIB byte :

<img src="https://i.imgur.com/FWc0EoW.png" alt="img" style="zoom: 10%;" />

&nbsp;
     
#### Example:


Instruction :

```
jmp [eax*2 + ebx]
```
```
00000000 67 ff 24 43
```

The initial `0x67` is a predefined prefix for address-size override. After it comes `0xff` and the ModR/M byte `0x24`. The value of the ModR/M suggests that there exists a SIB byte, which is `0x43`. 

Lookup in te SIB table, the row tells us that `eax` is scaled by 2 and the column tells that the bases to be added is in `ebx`. 

   
&nbsp;
        
___Displacement___ is the offset from the start of the base index. 

&nbsp;       
       
#### Example:

Instruction :
```
jmp [0x1234]
```
```
ff 26 34 12
```
`0x1234` which is generated as `34 12` in raw machine code is the _displacement_ and stands right next to the ModR/M byte `0x26`. 

&nbsp;
       
#### Example

Instruction :
```
jmp [eax * 4 + 0x1234]
```
```
67 ff 24 85 34 12 00 00
```
`0x67` is an address-size override prefix. It changes from default 16-bit to non-default 32-bit.    
`0xff` is the opcode.      
`0x24` is the ModR/M byte. The value suggests that a SIB follows.       
`0x85` is the SIB byte. According to the table, the byte can be destructed into bits as follows :      
```
  SS  |     R/m   |    REG
1 | 0 | 0 | 0 | 0 | 1 | 0 | 1 
```
The total bits combine into `10000101` which is `0x85` in hex. By value, if a register after the displacement is not specified, it is set to EBP register, and thus the 6^th column (bit pattern 101).
      
&nbsp;
    
#### Example:

If the example uses another register. Instruction :
```
jmp [eax * 4 + eax + esi]
```
The SIB becomes `0x86`. 
`34 12 00 00` is the displacement. This is 4 bytes in size, equivalent to 32-bit. 

         
&nbsp;
     
___Immediate___ When an instruction accepts a fixed value, eg. 0x1234, as an operand, this optional field holds the value. It is different from displacement because the value is not necessarly used as offset, but an arbitrary value of anything. 

&nbsp;
      
#### Example:

Instruction:
```
mov eax, 0x1234
```
```
66 b8 34 12 00
```

`0x66` is operand-sized override prefix. Similar to the address-size override prefix, this prefic enables operand-size to be non-default.        
`0xb8` is one of the opcodes for _mov_.     
`0x1234` is the value to be stores in register _eax_.      


&nbsp;
        
### 4.6 Understand an instruction in detail.  <a name="4.6"></a>


In the instruction reference manual, from chapter 3 onward every x86 instruction is documented in detail. Before looking, we must know the writing conventions. 

Every instruction has the following common structure:
```
Opcode	    Instruction 	Op/En	    64/32-bit Mode       CPUID		Description		Feature flag.
```
&nbsp;

___Opcode___  There can be more than one opcode for an instruction. In this column can be other notations aside hexadecimal numbers. For example `/r` indicates that the _ModR/M_ byte contains a _reg_ operand and an _r/m_ operand. The details are listed in the Intel Manual. 

&nbsp;
      
___Instruction___ gives the assembly instruction that can be used. They are more than just a rename of the opcode as they might include specific propertied for the instruction. For example, `re18` represents a relative address from 128 bytes before the end of te instrucion to 127 bytes after the end of the instruction. Similarly `rel16/re132` also represent addresses, but with the operand size of 16/32-bit instead of 8-bit like `re18`.

&nbsp;
     
___Op/En___ is short for _Operand/Encoding_. It specifies how a _ModR/M_ byte encodes the operands that an instruction requires. If a variant of an istruction requires operands, then an additional table names _Instruction Operand Encoding_ is added for explaining operand encoding, with the following structure :
```
Op/En  | Operand 1   | Operand 2   | Operand 3    | Operand 4
```
The operands can be readable, or writable, or both. The symbol _r_ means redable, _w_ means writable. For example, when Operand 1 field contains _ModRM:r/m(r)_, it means it's only readable. 

&nbsp;
      
___64/32-bit mode___ indicates whether the opcode sequence is supported in a 64-bit and possibly 32-bit mode. 

&nbsp;
       
___CPUID Feature Flag___ indicates a particular CPU feature must be available to enable the instruction. 

&nbsp;

___Compat/Leg Mode___  Many instructions do not have this CPUID field, instead it is replaced with Compat/Leg Mode, standing for _Compatibility or Legacy Mode_.  This mode enables 64-bit variants of instructions to run normally in 16 or 32-bit mode. 

&nbsp;

___Description___ specifies the purpose of the instructions and how it works in detail. 

&nbsp;

___Operation___ is pseudo-code that implements an instruction.

&nbsp;

___Flags affected___ lists the possible changes to system flags in EFLAGS register. 

&nbsp;

___Exceptions___ list the possible errors that can occur during execution. They fall into one of the following categories :
* Protected Mode Exception
* Real-Address Mode Exception
* Virtual-8086 Mode Exeption
* Floating-Point Exception
* SIMD Floating-Point Exception
* Compatibility Mode Exception
* 64-bit Mode Exception. 

For our OS, we only use Protected Mode Exceptions and Real-Address Mode Exceptions. 


&nbsp;

### 4.7 Example: `jmp` instruction.  <a name="4.7"></a>


This is the opcode table:

<img src="https://i.imgur.com/Nx1eJLw.png" alt="img" style="zoom: 10%;" />


Each row lists a vriant of the `jmp` instruction. The first vatiant has the opcode `EB cb` with the equivalent form `jmp rel8`. Here, `rel8` means 128-bit offset, counting from the end of the instruction. The end of an instruction is the next byte after the last byte of the instruction. Consider this assembly code :

```
jmp main
jmp main2
jmp main
main2:
  jmp 0x1234
``` 

Generated the machine code :
```
	   main				main2
	   |				|
Address | 00 | 01 | 02 | 03 | 04 | 05 | 06 | 07 | 08 | 09 |
Opcode  | eb | fe | eb | 02 | eb | fa | e9 | 2b | 12 | 00 |
```

The first `jmp main` instruction is generated into `eb fe` and copies the address 00 and 01. The end of the first `jmp main` is at address 02, past the last byte witch is located at address 01.  The value `fe` is equivalent to -2, since `eb` opcode uses only a byte (8 bits) for relative addressing. The offset is -2 and the end address of the first `jmp main` is 02, adding them together we get 00 which is the destination address for jumping to. 

&nbsp;

Similarly, the `jmp main2` instruction is generated into `eb 02` witch means the offset is +2. The end address is at 04, adding them together we get the destination address 06, witch is the start instruction marked by the lable `main2`

The same rule is applied to _rel16_ and _rel32_ encoding. In the example, the code `jmp 0x1234` uses _rel16_ (meaning 2-byte offset) and is generated into `e9 2b 12`. As table 4.7.1 shows, the `e9` opcode takes a `cw` operand, witch is a 2-byte offset. Notice that the offset value is `2b 12` while it is supposed to be `32 12`. This is not wrong. _rel8/rel16/rel32_ id an offset, not an address. An offset is a distance from a point. Since no lable is given but a number, the offset is calculated from the start of a program. In this case, the start is address 00. The end of `jmp 0x1234` is the address 09, therefore the offset is calculated as : 0x1234 - 0x9 = 0x122b.

&nbsp;

The jump instruction with the opcode `FF /4` enables jumpindg to a near absolute address stored in general-purpose register or memory. In short _absolute indirect_. The symbol `/4` is the column with digit 4 in table 4.5.1. For example :

```
jmp [0x1234]
```
```
ff 26 34 12
```

Since this is 16-bit code, we use table 4.5.1. ModR/M value 26 means `disp16`, witch means a 16-bit offset from the current index, which is the base address stores in DS register. In this case, `jmp [0x1234]` is implicitly understood as jmp [ds:0x1234], which means the destination address is 0x1234 bytes away from the start of a data segment. 

&nbsp;

The jmp instruction with opcode `FF /5` enables jumping to a _far absolute_ address stored in a memory location (as opposed to `/4` which means stored in a register). In short, _a far pointer_. In order to generate it, the keyward `far` is needed to tell `nasm` we are using a far pointer :

```
jmp far [eax]
```
generated into :
```
67 ff 28
```

Since `28` is the value in the 5th column of the table 4.5.2 that refers to [eax], we successfully geeratedan instruction for a far jump. After the CPU runs the instrunction, the program counter _eip_ and code segment register _cs_  is set to the memory address, stored in the memory locaition that _eax_ points to, and the CPU starts fetching code from the new address in _cs_ and _eip_. As an example :

<img src="https://i.imgur.com/j65QpKw.png" alt="img" style="zoom: 10%;" />

The far address consumes total of 5 bytes for a 16-bot segment and 32-bit address, which is encoded as m16:32 from table 4.7.1.

As can be seen above, the blue part is a segment address, loaded into _cs_ register with value 0x5678. The red part is the memory address within that segment, loaded into _eip_  register with value 0x1234 and start execution from there. 

&nbsp;

The jmp instruction with `EA` opcode jump to a direct absolute address :

Instruction:
```
jmp 0x5678:0x1234
```
```
ea 34 12 78 56
```

The address 0x5678 (78 56):0x1234 (34 12) is right next to the opcode, ulkine _FF /5_.

&nbsp;

### 4.8 Examine Compiled Data  <a name="4.8"></a>


This section examines how data definition in C maps to its assembly form. The generated code is extracted from the _.bss_ section. That means the assembly code displayed has _no_.

The code-assembly listing is not random, but based on chapter 4 of Volume 1, _Data Type_. The chapter lists fundamental data types that x86 hardware operated on. 

The _objdump_ command used will be :
```
$ objdump -z -M intel -S -D -j .data -j .bss <object file> | less
```

&nbsp;

#### 4.8.1 Fundamental Data Types <a name="4.8.1"></a>

The basic types are based on sizes, each is twice as big as the previous one :
* 1 byte (8 bits)    
* 2 bytes (16 bits)    
* 4 bytes (32 bits)    
* 8 bytes (64 bits)    
* 16 bytes (128 bits)    

<img src="https://i.imgur.com/1tgm0aR.png" alt="img" style="zoom: 10%;" />


From the Manual., section 4.1.1, volume 1:

_Words, doublewords, and quadwords do not need to be aligned in memory on natural boundries. The natural boundries are even-number addresses, evenly divisible by four and addresses evenly divisable by eight, respectivly. To improve the performance of programs, data structures (especially stacks) should be aligned on natural boundries whenever possible. The reason for this is that the processor requires two memory addresses to make an unaligned memory access; aligned access requires only one memory access. A word or doubleword operand that crosses a 4-byte boundry or a quadword operand that crosses an 8-byte boundry is consideres unaligned and requires twoo separate memory bus cycles for access._

_Some instrucitons that operate on double quadwords require memoty operands to be aligned on natural boundry. Those instrucitons generate a general-protection exception (`#GP`) if an unaligned operand is specified. A natural boundty for a double quadword is any address evenly divisable by 16. Other instructions that operate on double quadwords permit unaligned access (without exception). However, aditional memory bus cycles are required to access unaligned data from memory._


&nbsp;

In C, the following primitives map to the fundamental types. Must unclude the _stdint.h_.

```c
# include <stdint.h>

uint8_t byte = 0x12;
uint16_t word = 0x1234;
uint32_t dword = 0x12345678;
uint64_t qword = 0x123456789abcdef;
unsigned __int128 dqword1 = (__int 128) 0x123456789abcdef;
unsigned __int128 dqword2 = (__int128) 0x123456789abcdef << 64;

int main(int argc, char *argv[]) {
	return 0;
}
```

Assembly :

```
0804a018 <byte>:
  804a018: 	12 00 			adc 	al,BYTE PTR [eax]
0804a01a <word>:
  804a01a: 	34 12 			xor 	al,0x12
0804a01c <dword>:
  804a01c: 	78 56 			js 	804a074 <_end+0x48>
  804a01e: 	34 12 			xor 	al,0x12
0804a020 <qword>:
  804a020: 	ef 			out 	dx,eax
  804a021: 	cd ab 			int 	0xab
  804a023: 	89 67 45 		mov 	DWORD PTR [edi+0x45],esp
  804a026: 	23 01 			and 	eax,DWORD PTR [ecx]
0000000000601040 <dqword1>:
  601040: 	ef 			out	dx,eax
  601041: 	cd ab 			int 	0xab
  601043: 	89 67 45 		mov 	DWORD PTR [rdi+0x45],esp
  601046: 	23 01 			and 	eax,DWORD PTR [rcx]
  601048: 	00 00 			add 	BYTE PTR [rax],al
  60104a: 	00 00 			add 	BYTE PTR [rax],al
  60104c: 	00 00 			add 	BYTE PTR [rax],al
  60104e: 	00 00 			add 	BYTE PTR [rax],al
0000000000601050 <dqword2>:
  601050: 	00 00 			add 	BYTE PTR [rax],al
  601052: 	00 00 			add 	BYTE PTR [rax],al
  601054: 	00 00 			add 	BYTE PTR [rax],al
  601056: 	00 00 			add 	BYTE PTR [rax],al
  601058: 	ef 			out 	dx,eax
  601059: 	cd ab 			int 	0xab
  60105b: 	89 67 45 		mov 	DWORD PTR [rdi+0x45],esp
  60105e: 	23 01 			and 	eax,DWORD PTR [rcx]
```

_gcc_ generates the variables as seen above. Since this is data section, the assembly listing carries no meaning. When _byte_ is declared with _uint8_t_, gcc guarantees that the size is always 1 byte. You might notice the extra `00` next to `12`, this is normal as _gcc_ adds extra _padding bytes_. To make it easier to see, we can look at the _readelf_ output of the _.data_ section :
```
$ readelf -x .data hello
```


Output:
```
Hex dump of section `.data`:
  0x00601020  00000000  00000000  00000000  00000000  ...................
  0x00601030  12003412  78563412  efcdab89  67452301  ..4.xV4........gE#.
  0x00601040  efcdab89  67452301  00000000  00000000  ....gE#............
  0x00601050  00000000  00000000  efcdab89  67452301  ...............gE#.
```

___byte___ : 0x00601030 : 1200      
___word___ : 0x00601030 : 3412       
___dword___ : 0x00601030 : 77563412       
___qword___ : 0x00601030 : efcdab89 67452301        
___dqword1___ : 0x00601040 : efcdab89 67452301 00000000 00000000          
___dqword2___ : 0x00601050 : 00000000 00000000 efcdab90 67452301         


Variables are alocated space depending on their type and in the declaration order. Intel is a _little-endian machine_ which means smaller addresses hold bytes with smaller value, larger hold larger value.  For example, 0x1234 is displayed as `34 12`, `34` apears first at address 0x601032, then `12` at 0x601033. The decimal values within a byte is unchanges, so we see `34 12` instead of `43 21`. 


&nbsp;

If _char_ is already 1 byte, why bother with _int8_t_? Because `char` is not guaranteed to always be 1 byte but a minimum of 1 byte. In C, a byte is the size of a _char_, which is defined as the smallest unit. There are hardware devices where the smallest addresable unit is 16-bit or even larger, which means _char_ is 2 bytes in size. A byte in such a platform is actually 2 units of 8-bit bytes. 

&nbsp;

Not all architectures support the double quadword type. _gcc_ does provide support for 128-bit number if the CPU is 64-bit. By specifying a variable of type `__int128` or `unsigned __int128`, we get a 128-bit variable. 

The data types in C that map to the fundamental types are called _unsigned numbers_. Other than numerical calculation, they can also be used as a tool for structuring data in memory. We will see this application later in the book. 


&nbsp;

In all the examples above, smaller numbers can be cast into bigger data types, but if you try to cast a bigger number into a smaller type, 2 things happen :
1. The value is grater than the max value supported by the smaller type, so it needs to truncating the size and causing incorrect value. 
2. The value is smaller than the max value, so it fits. 

However, the value might be unknown until runtime. It is best to not let implicit conversions to the compiler. It can cause subtle bugs.


&nbsp;

#### 4.8.2 Pointer Data Types <a name="4.8.2"></a>


Pointers are variables that hold memory addresses. x86 works with two types of pointers :

* _Near Pointer_ is a 16/32-bit offset withing a segment, also called _effective address_.
* _Far Pointer_ is also an offset like a near pointer but with an explicit segment selector. 


TODO: Photo 

C only provides support for near pointers, since far pointers are platform dependent, such as x86.  In applicatoin code, you can assume that the address of current segment starts at 0, so the offset is actually any memory address from 0 to max address. 

```c
#include <stdint.h>

int8_t i = 0;
int8_t *p1 = (int8_t *) 0x1234;
int8_t *p2 = &i;

int main(int argc, char *argv[]) {
	return 0;
}
```

Assembly :
```
0000000000601030 <p1>:
  601030: 	34 12 			xor 	al,0x12
  601032: 	00 00 			add 	BYTE PTR [rax],al
  601034: 	00 00 			add 	BYTE PTR [rax],al
  601036: 	00 00 			add 	BYTE PTR [rax],alÎ
0000000000601038 <p2>:
  601038: 	41 10 60 00 		adc 	BYTE PTR [r8+0x0],spl
  60103c: 	00 00 			add 	BYTE PTR [rax],al
  60103e: 	00 00 			add 	BYTE PTR [rax],al

Disassembly of section .bss:
0000000000601040 <__bss_start>:
  601040: 	00 00 			add 	BYTE PTR [rax],al
0000000000601041 <i>:
  601041: 	00 00 			add 	BYTE PTR [rax],al
  601043: 	00 00 			add 	BYTE PTR [rax],al
  601045: 	00 00 			add 	BYTE PTR [rax],al
  601047: 	00 			.byte 0x0
```

Pointer `p1` holds a direct address with the value 0x1234. `p2` holds the address of the variable `i`.  Note that both pointers are 8 bytes in size.

&nbsp;

#### 4.8.3 Bit Field Data Type <a name="4.8.3"></a>


A _bit field_ is a cintiguous sequence of bits. They allow data structures at bit level. For example, a 32-bit data can hold multiple bit fields that represent multiple different pieces of information, such as bits 0-4 specifies the size of the data structure, bit 5-6 specifies permissions ans so on. 

TODO: Image

```c
struct bit_field {
	int data1:8;
	int data2:8;
	int data3:8;
	int data4:8;
};


struct bit_field2 {
	int data1:8;
	int data2:8;
	int data3:8;
	int data4:8;
	char data5:4;
};


struct normal_struct {
	int data1;
	int data2;
	int data3;
	int data4;
};


struct normal_struct ns = {
	.data1 = 0x12345678,
	.data2 = 0x9abcsed0,
	.data3 = 0x12345678,
	.data4 = 0x9abcdef0,
};


int i = 0x12345678;


struct bit_field bf = {
	.data1 = 0x12,
	.data2 = 0x34,
	.data3 = 0x56,
	.data4 = 0x78
};


struct bitfield2 bf2 = {
	.data1 = 0x12,
	.data2 = 0x34,
	.data3 = 0x56,
	.data4 = 0x78,
	.data5 = 0xf
};

int main(int argc, char *argv[]){
	return0;
}

```
Assembly :

```
0804a018 <ns>:
  804a018:	78 56 			js 	804a070 <_end+0x34>
  804a01a: 	34 12 			xor 	al,0x12
  804a01c: 	f0 de bc 9a 78 56 34 	lock fidivr WORD PTR [edx+ebx*4+0x12345678]
  804a023: 	12
  804a024: 	f0 de bc 9a 78 56 34 	lock fidivr WORD PTR [edx+ebx*4+0x12345678]
  804a02b: 	12
0804a028 <i>:
  804a028: 	78 56 			js 	804a080 <_end+0x44>
  804a02a: 	34 12 			xor 	al,0x12
0804a02c <bf>:
  804a02c: 	12 34 56 		adc 	dh,BYTE PTR [esi+edx*2]
  804a02f: 	78 12 			js 	804a043 <_end+0x7>
0804a030 <bf2>:
  804a030: 	12 34 56 		adc 	dh,BYTE PTR [esi+edx*2]
  804a033: 	78 0f 			js 	804a044 <_end+0x8>
  804a035: 	00 00 			add 	BYTE PTR [eax],al
  804a037: 	00 			.byte 0x0
```
The sample code creates 4 variables : `ns`, `i`, `bf`, `bf2`. The definition of `normal_struct` and `bit_field`  both specify 4 integers. `bit_field` specifies additional information next to its member name, separated by a colon, `.data1 : 8`. This information is the bit width of each group. It means that even if it is defines as `int`, `.data1` only consumes 8 bit of information. If aditional information is added two things might happen :
1. If the new data fits after .data, which are 24-bits, then the total size of `bit_field` is still 4 bytes, or 32-bits. 
2. If the new data does not fit, the remaining bits are still allocated in new storage, without the previous bits. 

&nbsp;

In the example, 4 data members each can access 8 bits of information, together can access 4 bytes of the integer first declared by `.data1`. As can be seen by the assembly code, the values of `bf` fallow natural order as written in the C code : `12 34 56 78`. In contrast, the value of `i` is a number as a whole, so it is subject to the rule of little endianess ans thus contains the value `78 56 34 12`. Note that the final byte in `bs`is `804a02f`, but next to it is the number `12`. This `12` does not belong to the `bf`. _objdump_ is just being confused that `78` is an opcode. `78` coresponds to `js` instruction, and it requires an operand. For that reason, _objdump_ grabs whatever next byte it finds after `78`. A better tool to view assembly is `gbd`, which will ne used in later chapters. 

Unlike in `bs`, each data member in `ns` is fully allocated as an integer, 4 bytes each, 16 total. As we can see, a bit field and normal struct are different: bit field structure data at the bit level, while normal structu works at byte level. 

&nbsp;

Finally, the struct `bf2` is the same as `bf`, but it contains one more data member : `.data5` defined as _char_.  For this reason, another 4 bytes are allocated just for `.data5`, even though it can only access 4 bits of information. The final values of `bf2` are : `12 34 56 78 0f 00 00 00`. The reamining 3 byes must be accessed by the mean of a pointer, or casting to another data type that can fully access 4 bytes.

&nbsp;

#### 4.8.4 String Data Members  <a name="4.8.4"></a>


Strings are defined different in x86 than in C. IN x86, strings are _continuous sequence of bits, bytes, words or doublewords_. C defines as an array of 1-byte chatacters with a zero as the last element, making a _null-terminated array_. This implies that strings in  x86 are arrays, not C strings. A programmer can define an array of bytes, words or doublewords with _char_ or _uint8_t_, _short_ or _uint16_t_. An array of bits can be implemented as an array of bytes or words that operates at bit level.

```c
#include <stdint.h>

uing8_t a8[2] = {0x12, 0x34};
uint16_t a16[2] = {0x1234, 0x5678};
uint32_t a32[2] = {0x123456789abcdef0, 0x123456789abcdefo};

int main(int argc, char *argv[]) {
	return 0;
}
```

Assembly :
```
0804a018 <a8>:
  804a018: 	12 34 00 		adc 	dh,BYTE PTR [eax+eax*1]
  804a01b: 	00 34 12 		add 	BYTE PTR [edx+edx*1],dh
0804a01c <a16>:
  804a01c: 	34 12 			xor 	al,0x12
  804a01e: 	78 56 			js 	804a076 <_end+0x3a>
0804a020 <a32>:
  804a020: 	78 56 			js 	804a078 <_end+0x3c>
  804a022: 	34 12 			xor 	al,0x12
  804a024: 	f0 de bc 9a f0 de bc 	lock fidivr WORD PTR [edx+ebx*4-0x65432110]
  804a02b: 	9a
0804a028 <a64>:
  804a028: 	f0 de bc 9a 78 56 34 	lock fidivr WORD PTR [edx+ebx*4+0x12345678]
  804a02f: 	12
  804a030: 	f0 de bc 9a 78 56 34 	lock fidivr WORD PTR [edx+ebx*4+0x12345678]
  804a037: 	12
```

Even if `a8` is an array with 2 elements, each 1 byte long, it still allocated 4 bytes. Again _gcc_ pads with 0 to ensure natural allignment. The actual value is `12 34 00 00`, `a8[0]` being `12` and `a8[1]` being `34`. 


`a16` has 2 elements, each 2 bytes long. Since 2 elements equal 4 bytes, there is no padding.  The value is `34 12 78 56`, with `a16[0]` being `34 12` and `a16[1]` being `78 56`. 

&nbsp;

Note that _objdump_ is confused again, as the opcode is `de`, calling `fidivr` that requires another operand, only the values `78, 56, 34, 12, f0, de, bc, 9a` belong to `a32`, the rest being garbage. `a32[0]` is `78 56 34  12` and `a32[1]` is `f0 de bc 9a`. 

&nbsp;

Finally, `a64` has 2 elements, 8 bytes each. The total size is 16 bytes, which is the natural allignment, therefore no padding is added. The values for `a62[0]` and `a64[1]` are the same : `f0 de bc 9a 78 56 34 12`.
      
`a8`  :    		     12 | 34
`a16` : 		  32 12 | 78 56
`a32` : 	    78 56 34 12 | f0 de bc 9a
`a64` : f0 de bc 9a 78 56 34 12 | f0 de bc 9a 78 56 34 12 

&nbsp;

Concerning multi-dimentional arrays :


```c
#include <stdint.h>

uint8_t a2[2][2] = {
	{0x12, 0x34},
	{0x56, 0x78}
};

uint8_t a3[2][2][2] = {
	{{0x12, 0x34},
	 {0x56, 0x78}},
	{{0x9a, 0xbc},
	 {0xde, 0xff}},
};

int main(int argc, char *argv[]) {
	return 0;
}
```


Assembly :

```
0804a018 <a2>:
  804a018: 	12 34 56 		adc 	dh,BYTE PTR [esi+edx*2]
  804a01b: 	78 12 			js 	804a02f <_end+0x7>
0804a01c <a3>:
  804a01c: 	12 34 56 		adc 	dh,BYTE PTR [esi+edx*2]
  804a01f: 	78 9a 			js 	8049fbb <_DYNAMIC+0xa7>
  804a021: 	bc 			.byte 0xbc
  804a022: 	de ff 			fdivrp st(7),st
```

Technically all arrays are translates into flat bytes. A 2x2 array is allocated with 4 bytes, and a 2x2x2 with 8 bytes. 

&nbsp;

### 4.9 Examining Compiled Code <a name="4.9"></a>


To examine how the compiler turns high level code into assembly, we will use the following command :
```
$ objdump --bo-show-raw-insn -M intel -S -D <file>
```
`-S` is to demonstrate the connection between high level and low level code and `--no-show-raw-insn` is added to omit the opcodes for clarity.

&nbsp;

#### 4.9.1 Data Transfer <a name="4.9.1"></a>

Once data is allocated, they must be accessible and writable. Data transfer instructions move data between memory and registers and between registers.

```c
#include <stdint.h>

int32_t i = 0x12345678;

int main(int argc, char * argv[]) {
	int j = i;
	int k = 0xabcdef;

	return 0;
}
```
Assembly :

```
#include <stdint.h>

int32_t i = 0x12345678;
int main(int argc, char *argv[]) {

   80483db: 	push 	ebp
   80483dc: 	mov 	ebp,esp
   80483de: 	sub 	esp,0x10

int j = i;

   80483e1: 	mov 	eax,ds:0x804a018
   80483e6: 	mov 	DWORD PTR [ebp-0x8],eax

int k = 0xabcdef;

   80483e9: 	mov 	DWORD PTR [ebp-0x4],0xabcdef
return 0;

   80483f0: 	mov 	eax,0x0
}
   80483f5: 	leave
   80483f6: 	ret
   80483f7: 	xchg 	ax,ax
   80483f9: 	xchg 	ax,ax
   80483fb: 	xchg 	ax,ax
   80483fd: 	xchg 	ax,ax
   80483ff: 	nop
```
The general data movement is performed with the `mov` instructions. Despite the name, it actually copies data from one destination to another.

&nbsp;

````
80483dc:	mov	ebp,esp
```` 
This line moves data from the `esp` to the register `ebp`. This instruction has the opcode _89_. 

&nbsp;

```
80483e1: 	mov 	eax,ds:0x804a018
80483e6: 	mov 	DWORD PTR [ebp-0x8],eax
```
These lines copy data from one memory location (the `i` variable) to another (`j`). There is only need of `mov`, one copies the data from a memory to a register, and another from the register to the destination location. 

&nbsp;

``` 
80483e9: 	mov 	DWORD PTR [ebp-0x4],0xabcdef
```` 
This line copies an immediate value into memory. 

&nbsp;

```
80483f0: 	mov 	eax,0x0
``` 
This line copies immediate data into a register. 

&nbsp;

#### 4.9.2 Expressions <a name="4.9.2"></a>


Expression :

&nbsp;

```c
int add = i + j;
```
```
80483e1:	mov	edx,DWORD PTR [ebp+0x8]
80483e4:	mov`	eax,DWORD PRT [ebp+0xc]
80483e7:	add	eax,edx
80483e9:	mov	DWORD PTR [ebp-0x34], eax
```
Variables `i` and `j` are stored in `eax` and `edx` respectively, then added together using the `add` instruction, and the final result is stores into `eax`. Then, the result is saved into the local variable `add`, which is at location [ebp-0x34].

&nbsp;

```c
int sub = i - j;
```
```
80483ec:	mov	eax,DWORD PTR [ebp+ox8]
80483ef:	sub	eax,DWORD PTR [ebp+0xc]
80483f2:	mov	DWORD PTR [ebp-0x30], eax
```
Similar to the `add`, there is an instruction for subtraction called `sub`. Here, _gcc_ translates woth `eax` reloaded with `i` and `eax` carrying the result from the previous addition. Then, `j` is subrtacted from `i`. After that, the value is saved into the variable `sub` at location `[ebp-0x30]`.

   
&nbsp;   
   
```c
int mul = i + j;
```
```
80483f5:	mov	eax,DWORD PTR [ebp+0x8]
80483f8:	imul	eax,DWORD PTR [ebp+0xc]
80483fc:	mov	DWORD PTR [ebp-0x34], eax
```

`eax` is reloded, since is carries the previous calculation. `imul` performs signed multiplication. `eax` is reloded with `i`, then multiplied with `j` and stores the result back in `eax`. Then the result is moved into variable `mul` at location `[ebp-0x34]`.

&nbsp;

```c
int div = i / j;
```
```
80483ff:	mov	eax,DWORD PTR [ebo+0x8]
8048402:	cdq	
8048403:	idiv	DWORD PTR [ebp+0xc]
8048406:	mov	DWORD PTR [ebp-0x30], eax
```
Similar to `imul`, `ivid` performs signed division. But different from _imul_, only takes one operand :
1. `i` is reloded into eax.
2. `cdq` converts the double word value in `eax` into a quadword value stred in the pair of registers `edx:eax` by copying the signed (bit 31) of the value in `eax` into every bit positoin in `edx. The pair `edx:eax` is the dividend, which is the variable `i`, and the operand to `idiv` is the divisor, the variable `j`. 
3. The result is stores into the pait `edx:eax` registers, with the quotient in `eax` and remainder in `edx`. The quotient is stored in the variable `div` at location `[ebp-ox30].

&nbsp;

```c
int mod = i % j;
```
```
8048409:	mov	eax,DWORD PTR [ebp+0x8]
804840c:	cdq	
804840d:	idiv	DWORD PTR [ebp+0xc]
8048410:	mov	DWORD PTR [ebp-0x2c], edx
```

The same `idiv` instruction also perforn the modulo operation, since it also calculated a remainder and stores in the variable `mod`, at location `[ebp-0x2c]`.

&nbsp;

```c
int neg = -i;
```
```
8048413:	mov	eax,DWORD PTR [ebp+0x8]
8048416:	neg	eax
8048418:	mov	DWORD PTR [ebp-0x28], eax
```

`neg` replaces the value of operand (destination operand) with it's complement (this operation is equivalent to subtractiong the operand from 0). The value `i` in `eax` is replaced with `-i` using `neg`, the new value is stored in the variable `neg` at `[ebp-0x28]`.

&nbsp;

```c
int and = 1 & j;
```
```
804841b:	mov	eax,DWORD PTR [ebp+0x8]
804841e:	and	eax,DWORD PTR [ebp+0xc]
8048421:	mov	DWORD PTR [ebp-0x24], eax
```

`and` performs a bitwise AND operation on two operands and stores the result in the variable `and` at `[ebp-0x24]`.

&nbsp;

```c
int or = i | j;
```
```
8048424:	mov	eax,DWORD PTR [ebp+0x8]
8048427:	or	eax,DWORD PTR [ebp+0xc]
804842a:	mov	DWORD PTR [ebp-0x20],eax
```
`or` performs a bitwise OR on two operands and stores the result in the variable `or` at `[ebp-0x20]`.

&nbsp;

```c
int xor = i ^ j;
```
```
804842d:	mov	eax,DWORD PTR [ebp+0x8]
8048430:	xor	eax,DWORD PTR [ebp+0xc]
8048433:	mov	DWORD PTR [ebp-0x1c], eax
```
Performs a bitwise XOR and stores the result in `xor` at `[ebp-0x1c]`.

&nbsp;

```c
int not = ~i;
```
```
8048436:	mov	eax,DWORD PTR [ebp+0x8]
8048439:	not	eax
804843b:	mov	DWORD PTR [ebp-0x18], eax
```
Performs a bitwise NOT (1 is set to 0, and each 0 is set to 1) and stores the result in the variable `not` at `[ebp-ox18]`.

&nbsp;

```c
int shl = i << 8;
```
```
804843e:	mov	eax,DWORD PTR [ebp+0x8]
8048441:	shl	eax, 0x8
8048444:	mov	DWORD PTR [ebp-ox14], eax
```

`shl` (_shift logical left_) shifts the bits in the destination operand to the left by the number of bits specified in the source operand. In this case `eax` stres `i` and `shl` shifts `eax` by 8 bits to the left. The result is stored in `shl` at `[ebp-0x14]`. 
Visual representation of shift :

TODO: Photo of shift.

&nbsp;

```c
int shr = i >> 8;
```
```
8048447:	mov	eax,DWORD PTR [ebp+0x8]
804844a:	sar	eax, 0x8
804844d:	mov 	DWORD PTR [ebp-0x10], eax
```

`sar` is similar to `shl/sal` but shifts bits to the right and extends the sign bit. For right shift, `shr` and `sar` are different instructions. `shr` differs from `sar` in that it does not extend the sign bit. The result sis stores in variable `shr`. 

To visualise:

TODO: IMAGE OF SAR

Notice that initialy, the sign bit is 1, but after 1-bit and 10-bit shifts, the shifted-out bits are filled with zeros. With `sar`, the sign bit (most important bit) is preserved. That is, if the sign bit is 0, the new bits always get the value 0, if it is 1, new bits are always 1. 

&nbsp;

```c
char equal1 = (i == j);
```
```
8048450:	mov	eax,DWORD PTR [ebp+0x8]
8948453:	cmp	eax,DWORD PTR [ebp+0xc]
8048456:	sete	al
8048459:	mov	BYTE PTR [ebp-0x41],al
```

`cmp` compares variable `i` and `j`. `sete` stores the value 1 to the `al` register if the comparison is equal, or 0 otherwise. The general name for variants of `set` instruction is calles _SETcc_. The suffix _cc_ dnotes the condition being tested for _EFLAGS_ register. The result is stored in variable `equal1`. 

&nbsp;

```
int equal2 = (i == j);
```
```
804845c:	mov	eax,DWORD PTR [ebp+0x8]
804845f:	cmp	eax,DWORD PTR [ebp+0xc]
8048462:	sete 	al
8048465:	movzx	eax,al
8048468:	mov	DWORD PTR [ebp-0xc],eax
```

This also searches for equality, difference being the _int_ type. The `movzx` instuction, variant of `mov` copies the result inro a destintion operand and fills the remaining bytes with 0. In this case, since `eax` is 4 bytes wide, after copying the first byte in `al`, the remaining bytes of `eax` are filled with 0 to endure the `eax` arries the same value as `al`.

```
eax before move:
12 | 34 | 56 | 78 |

after movzx eax, al:
00 | 00 | 00 | 78 |
```

&nbsp;

```c
char greater = (i > j);
```
```
804846b:	mov	eax,DWORD PTR [ebp+0x8]
804846e:	emp	eax,DWORD PTR [ebp+0xc]
8048471:	setg	al
8048474:	mov	BYTE PTR [ebp-0x40],al
```
Similar to equality, but used `setg` for greater comparison instead.

&nbsp;

```c
char less = (i < j);
```
```
8048477:	mov	eax,DWORD PTR [ebp+0x8]
804847a:	cmp	eax,DWORD PTR [ebp+0xc]
804847d:	setl	al
8048480:	mov	BYTE PTR [ebp-ox3f],al
```
`setl` does less comparison. 

&nbsp;

```c
char greater_equal = (i >= j);
```
```
8048483:	mov	eax,DWORD PTR [ebp+0x8]
8048486:	cmp	eax,DWORD PTR [ebp+0xc]
8048489:	setge	al
804848c:	mov	BYTE PTR [ebp-0x3e],al
```
Use `setge` for greater or equal comparison. 

&nbsp;

```c
char less_equal = (i <= j);
```
```
804848f:	mov	eax,DWORD PTR [ebp+0x8]
8048492:	cmp	eax,DWORD PTR [ebp+0xc]
8048495:	setle	al
8048498:	mov	BYTE PTR [ebp-0x3d],al
```
Use `setle` for less than or equal comparison. 

&nbsp;

```c
int logical_and = (i && j);
```
```
804849b:	cmp	DWORD PTR [ebp+0c8], 0x0
804849f:	je	80484ae <expr+0xd3>
80484a1:	cmp	DWORD PTR [ebp+0xc], 0x0
80484a5:	je	80484ae <expr+0xd3>
80484a7:	mov	eax,0x1
80484ac:	jmp	80484b3 <expr+0xd8>
80484ae:	mov	eax, 0x0
80484b3:	mov	DWORD PTR [ebp-0x8], eax
```

Logical AND is one of the syntaxes that is made entirely in software with simpler instrucitons. The algorithm is simple:
1. Check if `i` is 0 with the instrucition at : 0c804849b.     
    a. If true, jump to 0x80484ae and set `eax` at 0.      
    b. Set the variable `logical_and` to 0, as is the next instruction after : 0x8044ae.      
     
2. If `i` is not 0, check if `j` is 0 with instruction at: 0c80484a1.  
    a. If true, jump to 0x80484ae and set `eax` to 0.    
    b. Set the variable `logical_and` to 0, as is the next instruction after : 0x80484ae.    

3. If both `i` and `j` are not 0, the result is certenly 1.
    a. Set acordingly with instruction : ox80484a7.
    b. Jump to the instruction at 0x80484b3 to set variable `logical_and` at [ebp-ox8] o 1.


&nbsp;

```c
int logical_or = (i || j);
```
```
80484b4:	cmp	DWORD PTR [ebp+0x8],0x0
80484ba:	jne	80484c2 <expr+0xe7>
80484bc:	cmp	DWORD PTR [ebp+0xc],0x0
80484c0:	je	80484c9 <ebp+0xc>
80484c2:	mov	eax,0x1
80484c7:	jmp	80484ce <exp+0xf3>
80484c9:	mov	eax,0x1
80484ce:	mov	DWORD PTR [ebp-0x4],eax
```
Similar to above.

&nbsp;

```c
++i;
--i;
```
```
80484d1:	and	DWORD PTR [ebp+0x8],0x1
80484d5:	sub	DWORD PTR [ebp+0x8],0x1
```

Made from existing instructions. There are specific instrucitons for this called `inc` and `dec` that cause a _partial flag register stall_, this happend when an instruction modifies a part of the flag register and the following instruction is dependent on the outcome of the flags. The manual sugests these should be replaced with `sub` and `add`.

&nbsp;

```c
int i1 = i++;
```
```
80484d9:	mov	eax,DWORD PRT [ebp+0x8]
80484dc:	lea	edx,[eax+0x1]
80484df:	mov	DWORD PTR [ebp+0x8],edx
80484e2:	mov 	DWORD PTR [ebp-0x10],eax
```

`i` is copied into `eax` at 0x80484d9. The value of `eax` + `0x1` is copied into `edx` as an _effective address_ at 0x80484dc. The `lea` (_load effective address_) instruction copies a memory address into a register. According to volume 2, the source operand is a memoty address specified with one of the processors addresing mode. This means, the source operand must be specified by the addressing modes defined in 16-bit/32-bit ModR/M byte tables. After loading the incremented value into `edx`, the value of `i` is incresead by 1 at 0x80484df. The previous `i` is stores back at `i1` at [ebp-0x8] by instruction 0x80484e2.

&nbsp;

```c
int i2 = ++i;
```
```
80484e5:	add	DWORD PTR [ebp+0x8],0x1
80484e9:	mov	eax, DWORD PTR [ebp+0x8]
80484ec:	mov	DWORD PTR [ebp=0xc],eax
```
The primary differences are:
* `add` is used instead of `lea` to increse `i` directly.
* new value is stored in `i2`.
* only 3 instructions long instead of 4.

The prefix-increment syntax is faster than the suffic-increment one.

&nbsp;

```c
int i3 = i--;
```
```
80484ef:	mov	eax,DWORD PTR [ebp+0x8]
80484f2:	lea	edz,[eax-0x1]
80484f5:	mov	DWORD PTR [ebp+0x8],edx
80484f8:	mov	DWORD PTR [ebp-0x8],eax
```

Similar to `i++`.

&nbsp;

```c
int i4 = --i;
```
```
80484fb:	sub	DWORD PTR [ebp+0x8],0x1
80484ff:	mov	eax,DWORD PTR [ebp+0x8]
8048502:	mov	DWORD PTR [ebp-0x4],eax
```
Similar to `++i`.


&nbsp;

#### 4.9.3 Stack  <a name="4.9.3"></a>

A stack is a contiguous array of memory locations that holds a collection of data. When a new element is addeed, the stack _grows down_ in memory towards lesser addresses, and _shrinks up_ towards greater addresses when an element is removed. x86 uses the `esp` register to point to the top of the stack, newest element. A stack can be anywhere in main memory. x86 provides two operations for manipulating stacks:
  * `push` adds a new element ontop of stack.
  * `pop` removed top element.

TODO: Image.

&nbsp;

#### 4.9.4 Automatic Variables <a name="4.9.4"></a>

Local variables are variables that exist within a scope delimited by braces. The most common scope is a function but there can be unnamed code blocks.

```c
void foo() {
	int a;
	int b;
}
```
`a` and `b` are local variables that belong to `foo`.

```
int foo() {
	int i;
	
	{
		int a = 1;
		int b = 2;
		{
			return i = a + b;
		}
	}
}
```
`a` and `b` are local ariables to the unnamed code block. 

&nbsp;

When a local variable is created, it is pushed on the stack. When it goes out of scope, it is popped. When an argument is passed from a caller to a callee, it is pushed onto the stack, when it is returned it is popped. Because they are automatically alocated, they are called ___automatic variables___. 

A base frame pointer points to the start of the current function frame, and is kept in the `ebp` register. Whenever a function is called, it is allocated with it's own dedicated storage on stack, called a ___stack frame___. Here are placed all local variables and arguments. 

&nbsp;

When a function needs a local variable or an argument, it uses `ebp` to access the variable:
* All local variables are allocated after the `ebp` pointer. To access them, a number is subtracted from the pointer to reach the location of the variable. 
* All arguments are allocated before the `ebp` pointer, thus we need to add a number to the pointer to access them.
* The `ebp` itself points to the return address of the caller.

TODO: Image

&nbsp;

#### Example:

```c
int add(int a, int b) {
	int i = a + b;
	return i;
}
```
```
#include <stdint.h>

int add(int a, int b) {
		80483db:	push	ebp
		80483dc:	mov	ebs,esp
		80483de:	sub	esp,0x10

	int i = a + b;
		80483e1:	mov	edx,DWORD PTR [ebp+0x8]
		80483e4:	mov	eax,DWORD PTR [ebp+0xc]
		80483e7:	add	eax,edx
		80483e9:	mov	DWORD PTR [ebp-0x4],eax
	return i;
		80483ec:	mov	edx,DWORD PTR [ebp-0x4]
}
		80483ef:	leave
		80483f0:	ret
```


[ebp-0x4] is the local variable `i`, since it is allocated after `ebp`, with the length of 4 bytes (an int). `a` and `b` are arguments that can be accessed with `ebp`:
* [ebp+0x8] accesses `a`.
* [ebp+0xc] accesses `b`.


The rule is that, the closer a variable on the stack is to `ebp`, the closer it is to the function name.

TODO: IMage

&nbsp;

#### 4.9.5 Function Call and Return <a name="4.9.5"></a>


```c
#include <stdio.h>

int add(int a, int b) {
	int local = 0x12345;

	return a + b;
}

int main(int argc, char *argv[]) {
	add(1,1);
	
	return 0;
}
```

For every function call, _gcc_ pushed arguments on the stack in reverse order with the _push_ instruction. That is, in reverse order than C, to ensure the relative order between arguments. Then, _gcc_ generates a _call_ instruction, that pushes a return address before transferring control to `add` function.


```
080484f2 <main>:
int main (int argc, char *argv[]) {
	80483f2:	push	ebp
	80483f3:	mov	ebp,esp

	   add(1,2);
	80483f5:	push	0x2
	80483f7:	push	0x1
	80483f9:	call	80483db <add>
	80483fe:	add	esp,0x8

	   return 0;
	8048401:	mov	eax,0x0
}
	8048406:	leave
	8048407:	ret
```
Upon finishing the `add` function, the stack does 2 pop instructions by adding `0x8` to the stack pointer `esp`. At the end, a `leave` instruciton is executed and main return with a `ret` instruction. A `ret` instruction transfers program execution back to the caller, to the instruction right after the `call` instructoin. The reason it can do this is that the return address implicitly pushed by the `call` instruction is the address right after the `call` instruction.

At the end of a function, _gcc_ places a `leave` instruction to clean up all allocated space for local variables and pop the frame pointer. 


&nbsp;

#### 4.9.6 Loop <a name="4.9.6"></a>

It is simply resetting the instruction pointer to an already executed instruciton and start all over again. It is a `jmp` instruction. 

```c
#include <stdio.h>

int main(int argc, char *argv[]){
	for (int i = 0; i < 10; i++) {
	}
	return 0;
}	
```
```
#include <stdio.h>
int main(int argc, char *argv[]){
	80483db:	push	ebp
	80483dc:	mov	ebp,esp
	80483de:	sub	esp,0x10

	  for (int i = 0; i < 10; i++) {
	80483e1:	mov	DWORD PTR [ebp-0x4],0x0
	80483e8:	jmp	80483ee <main+0x13>
	80483ea:	add DWORD PTR [ebp-0x4],0x1
	80483ee:	cmp	DWORD PTR [ebp-0x4], 0x9
	80484f2:	jle	80483ea <main+0xf>
	  }
	  return 0;
	80483f4:	b8 00 00 00 00		mov	eax,0x0
}
	80483f9:	c9			leave
	80483fa:	c3			ret
	80483fb:	66 90			xchg	ax,ax
	80483fd:	66 90			xchg	ax,ax
	80483ff:	90			nop
```

&nbsp;

Initializing `i` to 0 :
```
80483e1:	mov	DWORD PTR [eb-0x4],0x0
```

&nbsp;

Compare `i` to 10 by using `jle` and compare it to 9. If true, jump to 0x80483ea.
```
80483e8:	jmp	80483ee <main+0x13>
. . .
80483ee:	cmp	DWORD PTR [ebp-0x4], 0x9
80483f2:	jle	80483ea <main+0xf>
```

&nbsp;

Increment by 1:
```
80483ea:	add	DWORD PTR [ebp-0x4],0x1
```

&nbsp;

#### 4.9.7 Conditional <a name="4.9.7"></a>

Conditionals are just another aplication of the `jmp` instruction. 

```c
#include <stdio.h>

int main(int argc, char *argv[]){
	int i = 0;
	
	if (argc){
		i = 1;
	} else {
		i = 0;	
	}

	return 0;
}
```
```
int main(argc, char *argv[]){
	80483db:	push	ebp
	80483dc:	mov	ebp,esp
	80483de:	sub	esp,0x10

	   int i = o;
	80483e1:	mov	DWORD PTR [ebp-0x4],0x0
	
	   if(argc){
	80483e8:	cmp	DWORD PTR [ebp+0x8],0x0
	80483ec:	je	80483f7 <main+0x1c>
	  
	 	i = 1;
	80483ee:	mov DWORD PTR [ebp-0x4],0x1
	80483f5:	jmp	80483fe <main+0x23>

		} else {
		     i = 0;
	80483f5:	mov	DWORD PTR [ebp-0x4],0x0

	   	}
		return 0;
	80483fe:	mov	eax,0x0
	}
	8048403:	leave
	8048404:	ret
```

First, it compares to see if `argc` is False, or 0. If true, continues to 0x80383ee for copying 1 to `i`. Else, it jumps to the else at 0x80483f7. Else branch compares to see if condition is true.


