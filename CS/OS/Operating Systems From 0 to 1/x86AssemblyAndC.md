
## x86 Assembly and C

In this chapter we explore assembly language, and how it connects to C. 


### 4.1  objdump

_objdump_ is a programs that displays information about objet files. Later it will be used to debug. Now we use it to examine how high level source code maps to assembly code.

Suppose we have an executable binary named _hello_ compiled from _hello.c_ that printsd _Hello World_. We can use `objdump -d` to only display assembled contents of executable sections. 

```
$ objdump -d hello
```

A _section_ is a block of memoty that contains either program code or data. A code section can be executed by the CPU while a data one cannot. With this command, non-executable sections such as _.data_ and _.bss_, debug sections, etc, are not displayed. 

On the other hand,
```
$ objdump -D hello
```
displays assembly contents of all sections.    

The output overruns the terminal screen. To make it easier to read, send all output to `less` :

```
$ objdump -d hello | less
```

To mix source code and assembly, the binary must be compiled using `-g` option to include source code in it, then add `-S` option :

```
$ objdump -S hello | less
```


     
The default syntax used by _objdump_ is AT&T syntax. To chnge it to the Intel syntax do :

```
$ objdump -M intel -D hello | less
```

When using the `-M` option, the `-D` or `-d` must be explicitly supplied. 

   

We will also write a 32-bit kernel, therefore we will need to compile a 32-bit binary and examine it in 32-bit mode :

```
$ objdump -M i386,intel -D hello | less
```

`-M i386` tells _objdump_ to display assembly content using the 32-bit layout. 

      
### 4.2 Reading the output


At the start of te output, the file format is displayed :

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

There is an optional forth collumn that is a comment, appears when there is a reference to an address :

```
lea r12,[rip+0x2008ee] # 600e10 <__frame_dummy_init_array_entry>
```

it to inform that the reference address `[rip+0x2008ee]` is 0x600e10, where the variable `__frame_dummy_init_array_entry` is.


A dissasembled section might also contain _labels_. A lable is a name given to an assembly instruction. It specifies the purpose of the block to the human reader. For example, the `.text` contain such lables to denote where the code starts. The below example contains two labels, `_start` and `_deregister_tm_clones`. The `_start` function starts at address 4003e0, to the left of the function name. Right below the label we can seee the instruction at address 4003e0. The label is simply a name of a memory address. 

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

### 4.3 Intel Manuals


The best way to understand assembly is to understand the underlying computer architecture and what the machine does. To do so, we must rely on information provided by vendors. To understand Intel's instruction set, we need the document : _Intel 64 and IA-32 architectures software developer's manual combined volumes 2A, 2B, 2C and 2D: Instructions set reference, A-Z_. The document can be retrieved here.

TODO: Put link here. 

Chapter 1: brief information about manual and notations used.
Chapter 2: in-dept explanation of the anatomy aof an assembly instruction.
Chapter 3 - 5: every instruction of the _x86_64_ architecture.
Chapter 6: safer mode extensions. 


The first volume, _Intel 64 and IA-32 Architecture Software Developer's Manual Volume 1: Basic Architecture_ describes the basic architecture and programming environment of Intel Processors. Here, chapter 5 gives the summary of all intel instructions. 

Read section 1.3 in volume 2, excclude sections 1.3.5 and 1.3.7.


### 4.4 Experiment with assembly code


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


### 4.5 Anatomy of an Assembly Instruction

This is a simpler introduction to the in-depth view of instruction format before reading chapter 2 from the reference manual. 

All assembly instructions are a fixed size series of bits. The length depends on how complicated it is. Still, they all share a common format describet in the picture below. The parts are :

TODO: Photo

        
___Instruction Prefixes___ appear at the beginning of an instruciton. They are optional because in practice, a prefix is just another assembly instruction inserted before another assembly instruction. Instructions with 2 or 3-byte opcodes include prefixes by default. 

       
___Opcode___ is a unique number that identifies the instruction. Each upcode is given a readable name, e.g. _add_ is 04. When a CPU sees 04 in it's instruction cache, it knows to call the instruction _add_. Opcode can be 1,2 or 3 bytes long and include additional 3-bit field in the _ModR/M_ byte when needed. 

#### Example 4.5.1:

For this code:  
```
jmp [0x1234]
```
the machine code is :
```
ff 26 34 12
```
The `0xff` is the opcode.        
      

___ModR/M___ specifies operands of an instruction. Operand can either be a register, a memory location or immediate value. This component is made from 3 parts :

* ___mod___ field, or _modifier_ field, is combined with _r/m_ field for a total of 5 bits of information to encode 32 possible values : 8 registers and 24 addresses.
* ___reg/opcode___ field encodes either a register operand, or extends the _Opcode_ field with 3 more bits.
* ___r/m___ field encodes either a register operand or can be combined with _mod_ field to encode an addressing mode. 

The following tables list all possible 256 values of ___ModR/M___ byte and how each value maps to an addressing mode and a register, in 16-bit and 32-bit modes.

TODO: Tables


How to read the table :
Have a code you want to look up. Look up the byte value to get the coresponding operands in the row and column. 

        
#### Example 4.5.2:

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

      
#### Example 4.5.3:
```
add eax, ecx
```
Machine code :
```
66 01 c8
```

The interesting feature of this instruction is that `0x66` is not the opcode. `0x01` is he opcode. The `0x66` is an optional prefix. If the CPU is sqwitched to 32-bit mode, because of this prefix, the instruction operands are limited to 16-bit width.    

If the CPU is in a 16-bit environment, then 32-bit is considered non-standard and there is a need for a prefix to be executed at this width. Otherwise, if there is none, they are executed at 16-bit. 

The `0xc8` is the _ModR/M_ byte. Lookup in the table at _c8_ value, the first operand is _ax_, the column then gives the second as _cx_. 

Why is the first operand in the row and the second in the column?

```
 mod  | reg/opcode |    r/m
1 | 1 | 0 | 0 | 1  | 0 | 0 | 0 |
```
This is the ModR/M byte.

The _mod_ field divides addressing modes into 4 different categories. Further combined with the _r/m_ field, exactly one addressing mode can be selected from one of the 24 rows. If an instruction only required one operand, the column can be ignored. Then the _reg/opcode_ field provides an extra register or different variants, if an instruction requires one.

   
   
   
___SIB___ is _Scale-Index-Base_ byte. This byte encodes ways to calculate the memory position into an elemenent of an array. SIB is based on the formula for calculationg an effective address :
```   
Effective address = scale * index + base 
```
  
_Index_ is an offset into an array.      
_Scale_ is a factor of _index_. It is one of the values 1,2,4,8, any other is invalid. If you use 1, the offset must be calculated manually. For example, to get the address of the _n^th_ element in an array where each element is 12-bytes long, scale must be set to 1 because the length is 12 instead of 1,2,4 or 8 and a compiler must calculate the offset :
```
Effective address = 1*(12*n) + base
```

Why use SIB?

In this example, an additional `mul` instruction must be executed to get the offset which consumes more than 1 byte, while SIB only consumes 1 byte. If this is scales, it can be detrimental to te performance of the CPU. 
       
   
The values 2,4 and 8 are not random. They map to 16-bit (2 bytes), 32-bit (4 bytes) and 64-bit (8 bytes).
          
     
_Base_ is the starting address.
 
Below is the table listing all 256 values of SIB byte :

TODO: SIB Table


     
#### Example 4.5.4:


Instruction :

```
jmp [eax*2 + ebx]
```

Code :
```
00000000 67 ff 24 43
```

The initial `0x67` is a predefined prefix for address-size override. After it comes `0xff` and the ModR/M byte `0x24`. The value of the ModR/M suggests that there exists a SIB byte, witch is `0x43`. 

Lookup in te SIB table, the row tells us that `eax` is scaled by 2 and the column tells that the bases to be added is in `ebx`. 

   
        
___Displacement___ is the offset from the start of the base index. 

       
####Example 4.5.5:

Instruction :
```
jmp [0x1234]
```

Code :
```
ff 26 34 12
```
`0x1234` which is generated as `34 12` in raw machine code is the _displacement_ and stands right next to the ModR/M byte `0x26`. 

       
#### Example 4.5.6

Instruction :
```
jmp [eax * 4 + 0x1234]
```

Code :
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
       
    
#### Example 4.5.7

If the example uses another register. Instruction :
```
jmp [eax * 4 + eax + esi]
```
The SIB becomes `0x86`. 
`34 12 00 00` is the displacement. This is 4 bytes in size, equivalent to 32-bit. 

         
     
___Immediate___ When an instruction accepts a fixed value, eg. 0x1234, as an operand, this optional field holds the value. It is different from displacement because the value is not necessarly used as offset, but an arbitrary value of anything. 

      
#### Example 4.5.8

Instruction:
```
mov eax, 0x1234
```
Code:
```
66 b8 34 12 00
```

`0x66` is operand-sized override prefix. Similar to the address-size override prefix, this prefic enables operand-size to be non-default.     
`0xb8` is one of the opcodes for _mov_.    
`0x1234` is the value to be stores in register _eax_.


        
### 4.6 Understand an instruction in detail. 
