
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


