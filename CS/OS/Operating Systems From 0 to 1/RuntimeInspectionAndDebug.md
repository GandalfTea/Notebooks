

## Runtime inspection and debug.

&nbsp;

A _debugger_ is a program that allows for inspection of a running program. It can stop a program at a specific point, called a _breakpoint_ and analyze the state of the program at that point. 

&nbsp;

We will be using ___GDB___, GNU Debugger for debugging our kernel. ___gbd___ can do four main things:

* Start the program and specify anything that might affect it's behavoir.
* Stop the program on specific conditions.
* Examine what happened when the program is stopped.
* Change things in your program, so you can experiment with correcting the effects of a bug.

&nbsp;

#### A sample program

A simple one will do fine:
```c
#include <stdio.h>

int main(int argc, char * argv[]) {
	printf("Hello World!\n");
	return 0;
}
```

&nbsp;


We compile it with debugging information using the option `-g`:
```
$ gcc -m32 -g hello.c -o hello
```

&nbsp;

We start _gdb_ with the program as argument:
```
$ gbd hello
```

&nbsp;


### Statitic inspection of a program


Before running the program, _gdb_ loads it into memory. A lot of inportant info can be inspected here. The commands presented here can also be used when the program is running.

&nbsp;


#### _info targer/ info file/ info files_

This command prints the information of the target being deugged. A _target_ is the debugging program.

```
(gdb) info target
```
```
Symbols from "/tmp/hello".
Local exec file:
`/tmp/hello`, file type elf32-i386.
Entry point: 0x8048310
0x08048154 - 0x08048167 is .interp
0x08048168 - 0x08048188 is .note.ABI-tag
0x08048188 - 0x080481ac is .note.gnu.build-id
0x080481ac - 0x080481cc is .gnu.hash
0x080481cc - 0x0804821c is .dynsym
0x0804821c - 0x08048266 is .dynstr
0x08048266 - 0x08048270 is .gnu.version
0x08048270 - 0x08048290 is .gnu.version_r
0x08048290 - 0x08048298 is .rel.dyn
0x08048298 - 0x080482a8 is .rel.plt
0x080482a8 - 0x080482cb is .init
0x080482d0 - 0x08048300 is .plt
0x08048300 - 0x08048308 is .plt.got
0x08048310 - 0x080484a2 is .text
0x080484a4 - 0x080484b8 is .fini
0x080484b8 - 0x080484cd is .rodata
0x080484d0 - 0x080484fc is .eh_frame_hdr
0x080484fc - 0x080485c8 is .eh_frame
0x08049f08 - 0x08049f0c is .init_array
0x08049f0c - 0x08049f10 is .fini_array
0x08049f10 - 0x08049f14 is .jcr
0x08049f14 - 0x08049ffc is .dynamic
0x08049ffc - 0x0804a000 is .got
0x0804a000 - 0x0804a014 is .got.plt
0x0804a014 - 0x0804a01c is .data
0x0804a01c - 0x0804a020 is .bss
```

The raports displayed include:

* Path of a symbol file. A _symbol_ file is the file that contains the debugging information. Usually this is the same file as the binary, but it is common to separate between an executable binary and its debugging information into 2 files. In this example, it is the line:
```
Sumbols from "/tmp/hello".
```

* Path of the debugging program and the file:
```
Local exec file:
`/temp/hello`, file type elf32-i386.
```

* Entry point of debugging program:
```
Entry point: 0x8048310
```

* List of sections and ending addresses. In the example, the ramaining output.


&nbsp;

#### _main info sections_


This gives extra information on program sections, specifically the file offset and the flags for each section.

```
(gdb) main info sections
```
```
Exec file:
`/tmp/hello`, file type elf64-x86-64.
[0] 0x00400238->0x00400254 at 0x00000238: .interp ALLOC LOAD READONLY DATA HAS_CONTENTS
[1] 0x00400254->0x00400274 at 0x00000254: .note.ABI-tag ALLOC LOAD READONLY DATA HAS_CONTENTS
[2] 0x00400274->0x00400298 at 0x00000274: .note.gnu.build-id ALLOC LOAD READONLY DATA HAS_CONTENTS
[3] 0x00400298->0x004002b4 at 0x00000298: .gnu.hash ALLOC LOAD READONLY DATA HAS_CONTENTS
[4] 0x004002b8->0x00400318 at 0x000002b8: .dynsym ALLOC LOAD READONLY DATA HAS_CONTENTS
[5] 0x00400318->0x00400355 at 0x00000318: .dynstr ALLOC LOAD READONLY DATA HAS_CONTENTS
[6] 0x00400356->0x0040035e at 0x00000356: .gnu.version ALLOC LOAD READONLY DATA HAS_CONTENTS
[7] 0x00400360->0x00400380 at 0x00000360: .gnu.version_r ALLOC LOAD READONLY DATA HAS_CONTENTS
....remaining output omitted....
```

Here, we can see that the section with _LOAD_ flag are from the _LOAD_ segment. The command can be comined with section flags for filtered output.

_ALLOBJ_ displays sections for all loaded object files, including shared libraries. 
_section names_ displays only named sections.


&nbsp;

It can also be fileterd by type:
```
(gdb) main info sections .text .data .bss
```
```
Exec file:
`/tmp/hello`, file type elf64-x86-64.
[13] 0x00400430->0x004005c2 at 0x00000430: .text ALLOC LOAD READONLY CODE HAS_CONTENTS
[24] 0x00601028->0x00601038 at 0x00001028: .data ALLOC LOAD DATA HAS_CONTENTS
[25] 0x00601038->0x00601040 at 0x00001038: .bss ALLOC
```

_section-flags_ displays only sections with specified flags. Note that the flags are specific to _gdb_. Currently, it supports:

_ALLOC_ will have space allocated in the process when loaded. Set for all sections except those containing debug info.

_LOAD_ will be loaded from the file into the child process memory. Set for pre-initialized code and data, clear for .bss sections.

_RELOC_ need to be relocated before loading.

_READONLY_ cannot be modified by the child process.

_CODE_ will contain executable code only.

_DATA_ will contain only data (no exeutable)

_ROM_ will reside in ROM

_CONSTRUCTOR_ contains data for constructor/destructor lists.

_HAS_CONTENTS_ is not empty.

_NEVER_LOAD_ instruction to the linker to not output the section.

_COFF_SHARED_LIBRARY_ notification to the linker that the section contains COFF shared library information. COFF is an object file format, similar to ELF. While ELF is the file format for the executable binary, COFF is the format for an object file.

_IS_COMMON_ contains common symbols.


We can restrict the output to only show only specific flags:

```
(gdb) main info sections CODE
```
```
Exec file:
'/tmp/hello', file type elf64-x86-64.
[10] 0x004003c8->0x004003e2 at 0x000003c8: .init ALLOC LOAD READONLY CODE HAS_CONTENTS
[11] 0x004003f0->0x00400420 at 0x000003f0: .plt ALLOC LOAD READONLY CODE HAS_CONTENTS
[12] 0x00400420->0x00400428 at 0x00000420: .plt.got ALLOC LOAD READONLY CODE HAS_CONTENTS
[13] 0x00400430->0x004005c2 at 0x00000430: .text ALLOC LOAD READONLY CODE HAS_CONTENTS
[14] 0x004005c4->0x004005cd at 0x000005c4: .fini ALLOC LOAD READONLY CODE HAS_CONTENTS
```

&nbsp;

### _info functions_

Lists all function names and their loaded address. 

```
(gdb) info functions
```
```
All defined functions:
File hello.c:
int main(int, char **);
Non-debugging symbols:
0x00000000004003c8 _init
0x0000000000400400 puts@plt
0x0000000000400410 __libc_start_main@plt
0x0000000000400430 _start
0x0000000000400460 deregister_tm_clones
0x00000000004004a0 register_tm_clones
0x00000000004004e0 __do_global_dtors_aux
0x0000000000400500 frame_dummy
0x0000000000400550 __libc_csu_init
0x00000000004005c0 __libc_csu_fini
0x00000000004005c4 _fini
```

&nbsp;

#### _info variables_


Lists all global and static variables.


If we add an `int` variable _i_ to the example program, we would have:

```
(gdb) info variables
```
```
All defined variables:
File hello.c:
int i;
Non-debugging symbols:
0x00000000004005d0 _IO_stdin_used
0x00000000004005e4 __GNU_EH_FRAME_HDR
0x0000000000400708 __FRAME_END__
0x0000000000600e10 __frame_dummy_init_array_entry
0x0000000000600e10 __init_array_start
0x0000000000600e18 __do_global_dtors_aux_fini_array_entry
0x0000000000600e18 __init_array_end
0x0000000000600e20 __JCR_END__
0x0000000000600e20 __JCR_LIST__
0x0000000000600e28 _DYNAMIC
0x0000000000601000 _GLOBAL_OFFSET_TABLE_
0x0000000000601028 __data_start
0x0000000000601028 data_start
0x0000000000601030 __dso_handle
0x000000000060103c __bss_start
0x000000000060103c _edata
0x000000000060103c completed
0x0000000000601040 __TMC_END__
0x0000000000601040 _end
```

&nbsp;

#### _disassemble / disas_

Displays the assembly code of the executable file.

It can be filtered by functions:
```
(gdb) disassemble main
```
```
Dump of assembler code for function main:
0x0804840b <+0>: lea ecx,[esp+0x4]
0x0804840f <+4>: and esp,0xfffffff0
0x08048412 <+7>: push DWORD PTR [ecx-0x4]
0x08048415 <+10>: push ebp
0x08048416 <+11>: mov ebp,esp
0x08048418 <+13>: push ecx
0x08048419 <+14>: sub esp,0x4
0x0804841c <+17>: sub esp,0xc
0x0804841f <+20>: push 0x80484c0
0x08048424 <+25>: call 0x80482e0 <puts@plt>
0x08048429 <+30>: add esp,0x10
0x0804842c <+33>: mov eax,0x0
0x08048431 <+38>: mov ecx,DWORD PTR [ebp-0x4]
0x08048434 <+41>: leave
0x08048435 <+42>: lea esp,[ecx-0x4]
0x08048438 <+45>: ret
End of assembler dump.
```

It would be more useful if source is included:
```
(gdb) disassemble /s main
```
From my PC, this flag was changed for `/m`.

```
Dump of assembler code for function main:
hello.c:
4 {
	0x0804840b <+0>: lea ecx,[esp+0x4]
	0x0804840f <+4>: and esp,0xfffffff0
	0x08048412 <+7>: push DWORD PTR [ecx-0x4]
	0x08048415 <+10>: push ebp
	0x08048416 <+11>: mov ebp,esp
	0x08048418 <+13>: push ecx
	0x08048419 <+14>: sub esp,0x4
5 		printf("Hello World!\n");
	0x0804841c <+17>: sub esp,0xc
	0x0804841f <+20>: push 0x80484c0
	0x08048424 <+25>: call 0x80482e0 <puts@plt>
	0x08048429 <+30>: add esp,0x10
6 		return 0;
	0x0804842c <+33>: mov eax,0x0
7 }
	0x08048431 <+38>: mov ecx,DWORD PTR [ebp-0x4]
	0x08048434 <+41>: leave
	0x08048435 <+42>: lea esp,[ecx-0x4]
	0x08048438 <+45>: ret
End of assembler dump.
```


If you also add the flag `/r`, you can show raw instructions in hex.

```
(gdb) disassemble /rs main
```
Again, in my case, this was `/rm`.

```
Dump of assembler code for function main:
hello.c:
4 {
	0x0804840b <+0>: 8d 4c 24 04 lea ecx,[esp+0x4]
	0x0804840f <+4>: 83 e4 f0 and esp,0xfffffff0
	0x08048412 <+7>: ff 71 fc push DWORD PTR [ecx-0x4]
	0x08048415 <+10>: 55 push ebp
	0x08048416 <+11>: 89 e5 mov ebp,esp
	0x08048418 <+13>: 51 push ecx
	0x08048419 <+14>: 83 ec 04 sub esp,0x4
5 		printf("Hello World!\n");
	0x0804841c <+17>: 83 ec 0c sub esp,0xc
	0x0804841f <+20>: 68 c0 84 04 08 push 0x80484c0
	0x08048424 <+25>: e8 b7 fe ff ff call 0x80482e0 <puts@plt>
	0x08048429 <+30>: 83 c4 10 add esp,0x10
6 		return 0;
	0x0804842c <+33>: b8 00 00 00 00 mov eax,0x0
7 }
	0x08048431 <+38>: 8b 4d fc mov ecx,DWORD PTR [ebp-0x4]
	0x08048434 <+41>: c9 leave
	0x08048435 <+42>: 8d 61 fc lea esp,[ecx-0x4]
	0x08048438 <+45>: c3 ret
End of assembler dump.
```


A function in a specific file can also be specified:

```
(gdb) disassemble /rm 'hello.c'::main
```

&nbsp;

#### _x_

Examines the content of a given memory range.

To examine the raw content in main:
```
(gdb) x main
```

Without arguments, the command only prints the content of a single memory address. In this case, the starting memory address.

We specify a range like this:
```
(gdb) x/20b main
```	
```
0x804840b <main>: 0x8d 0x4c 0x24 0x04 0x83 0xe40xf0 0xff
0x8048413 <main+8>: 0x71 0xfc 0x55 0x89 0xe5 0x510x83 0xec
0x804841b <main+16>: 0x04 0x83 0xec 0x0c
```

`/20b` means it prints 20 bytes from where `main` starts in memory.

It is formated from 2 pieces, the _repeated count_ which is 20 and the _format letter_ which is _b_.

The format letter can be:

TODO: Table


&nbsp;

#### _print / p_

It prints an expression. It can be a global variable, local variable, function, register, number, etc.


&nbsp;

### Runtime inspection of a program

&nbsp;

#### _run_

Starts running the program.
```
(gdb) r
```
```
Starting program: /tmp/hello
Hello World!
[Inferior 1 (pricess 1002) exited normaly]
```

&nbsp;

#### _break / b_

Sets a breakpoint at a location in the high-level source code. When _gdb_ reached the breakpoint, it stops to examine the state.

A breakpoint can be set on a file line:
```
(gdb) b 3
```
```
Breakpoint 1 at 0x400535: file hello.c, line 3.
```
When we run:
```
(gdb) r
```
```
Starting program: /tmp/hello
Breakpoint 1, main (argc=1, argv=0x7ffffffffdfb8) at hello.c:5
5	printf("Hello World!\n");
```


_gdb_ only stopped at line 5 because it need to reach an executable line. 

Line of code is not very reliable. You can also stop at functions:
```
b main
```

Sometimes the debugging program does not contain debug info. You can bypass this by printing the function, getting the address, then adding a breakpoint at the address:
```
(gdb) print main
```
```
$3 = {int (int, cahr**)} 0x600526 <main>
```
```
b *0x400526
```

If there are more files in the program, you can add the name of the file and then specify either the line, address or function:

```
b hello.c:3
b hello.c:main
```

&nbsp;

#### _next / n _


Executes the current line and stops at the next line. When the current line is a function call, steps over it.


After setting a breakpoint at _main_, it runs the program and stops at the first _printf_:
```
(gdb) r
```
```
Starting program: /tmp/hello
Breakpoint 1, main (argc=1, argv=0x7ffffffffdfb8) at hello.c:5
5	printf("Hello World!\n");
```
Proceed to the nest statement using the `next` command:
```
(gdb) n
```
```
Hello World!
6	return 0
```
It proceed to the next line, in this case 6, and runs line 5.

&nbsp;

#### _stop / s_

Executes the current line and stops at the next line. When the current line is a function call, steps into it to the first line in the called funciton.

Suppose we add another function _add_:
```c
#include <stdio.h>

int add(int a, int b) {
	return a + b;
}


int main(int argc, char * argv[]) {
	add(1,2);
	printf("Hello World!\n");
	return 0;
}
```

If at the first breakpoint you use `s`, you step into the function.
```
Starting program: /tmp/hello
Breakpoint 1, main (argc=1, argv=0xffffd154) at hello.c:11
11	add(1,2);
```
```
(gdb) s
```
```
add (a=1, b=2) at hello.c:6
6	return a + b;
```

&nbsp;

#### _ni_

At core, _gdb_ works on assembly instructions. Because of the debugginf info available, it can map high level code lines to assembly instructions. Line by line debugginf is just a execution of assembly instructions of a line when moving from the current line to the next.
This command executes the _one_ assembly instruction that belongs to the current line until all assembly instructions are executed, it will not move to the next line. If the current instruction is a call, step over to the next instruction.

```
(gdb) disassemble /m main
```
```
Dump of assembler code for function main:
hello.c:
4 {
	0x0804840b <+0>: lea ecx,[esp+0x4]
	0x0804840f <+4>: and esp,0xfffffff0
	0x08048412 <+7>: push DWORD PTR [ecx-0x4]
	0x08048415 <+10>: push ebp
	0x08048416 <+11>: mov ebp,esp
	0x08048418 <+13>: push ecx
	0x08048419 <+14>: sub esp,0x4
5 		printf("Hello World!\n");
	0x0804841c <+17>: sub esp,0xc
	0x0804841f <+20>: push 0x80484c0
	0x08048424 <+25>: call 0x80482e0 <puts@plt>
	0x08048429 <+30>: add esp,0x10
6 		return 0;
=> 	0x0804842c <+33>: mov eax,0x0
7 }
	0x08048431 <+38>: mov ecx,DWORD PTR [ebp-0x4]
	0x08048434 <+41>: leave
	0x08048435 <+42>: lea esp,[ecx-0x4]
	0x08048438 <+45>: ret
End of assembler dump.
```
```
(gdb) r
```
```
Starting program: /tmp/hello
Breakpoint 1, main (argc=1, argv=0x7ffffffffdfb8) at hello.c:5
5	printf("Hello World!\n");
```
```
(gdb) ni
```
```
0x0804841f  5		printf("Hello World!"\n);
```
```
(gdb) ni
```
```
0x08048424  5		printf("Hello World!"\n);
```
```
(gdb) ni
```
```
Hello World!
0x08048429  5		printf("Hello World!"\n);
```

Upon entering `ni`, _gdb_ executes current instruction and displays the _next_ instruction. That's why from the output, _gdb_ only displays 3 addresses: 0x0804841f, 0x08048424, 0x080429. The instruction at 0x0804841c, hwhich is te first instruction of _printf_, is not displayed because it is the first instruction that _gdb_ stopped at. Assume that _gdb_ stopped at the first instruction of _printf_ at 0x0804841c, the current instruciton can be displayed using _x_ command:
```
(gdb) x/i $eip
```
```
=> 0x0804841c <main+17>:sub	esp,0xc
```

&nbsp;

#### _si_

Similar to _ni_, the command executes the current assembly instruction in the current line. The difference is that if there is a function call, this one steps into the function at the first line.


&nbsp;

#### _until_

This command executes until the next line is greater than the current line.

Suppose we have a function that executes a long loop:
```c
#include <stdio.h>

int add1000(){
	int total = 0;

	for (int i = 0; i < 1000; i++){
		total += i;
	}

	printf("Done adding!\n");

	return total;
}

int main(int argc, char *argv[]) {
	add1000(1,2);
	printf("Hello World!\n");
	return 0;
}
```
Using the `next` command, we need to use it 1000 times to finish the for loop. A faster way is to use `until`:
```
(gdb) b add1000
```
```
Breakpoint 1 at 0x804811: file hello.c, line 4.
```
```
(gdb) r
```
```
Starting program : /tmp/hello
Breakpoint 1, add1000 () at hello.c:4.
4	int total = 0;
```
Executing here, it stops at line 5 because line 5 is bigger than line 4.
```
(gdb) until
```
```
5	for(int i = 0; i < 1000; ++i){
```
Executing here, stops at line 6 because it is bigger than line 5.
```
(gdb) until
```
```
6	total += 1;
```
Executing here, it stops at 5 since the loop is continuing.
```
(gdb) until
```
```
5	for(int i = 0; i < 1000; ++i){
```
Here, it stops at line 8 because it kept executing past line 5, as it is is less than line 6. 
```
(gdb) until
```
```
8	printf("Done adding!\n");
```


You can also pass `until` an argument, to stop at a specific line:
```
(gdb) until 8
```


&nbsp;

#### _finish_


This command executes until the end of a function, at which point it displays the return value. It is a more convenient for of `until`.

Using the previous example code, you can call `finish` instead of `until`:

```
(gdb) finish
```
```
Run till exit from #0 add1000 () at hello.c:4
Done adding!
0x08048466 in main (argc=11, argc=0xffffd154) at hello.c:15
15	add1000(1, 2);
Value returned is $1 = 499500
```
&nbsp;


#### _bt_


This command prints the _backtrace_ of all stack frames. A _backtrace_ is a list of currently active functions:

```c
void d(int d) { };
void c(int c) { d(0);}
void b(int b) { c(1);}
void a(int a) { b(2);}

int main(int argc, char *argv[]) {
	a(3);
	return 0;
}
```

```
(gdb) b a
```
```
Breakpoint 1 at 0x08048404: file hello.c, line 9.
```

Navigate to the middle and try the command:
```
(gdb) bt
```
```
#0	d (d=0) at hello.c:3
#1	0x080483eb in c (c=1) at hello.c:5
#2	0x080483fb in b (b=2) at hello.c:7
#3	0x0804840b in a (a=3) at hello.c:9
#4 	0x0804841b in main (argc=1, argv=0xffffd154) at hello.c:13
```

Most recent calls are on top. In this case, `d` is the most recent function. 

&nbsp;

#### _up_


This command goes up one frame earlier the current frame.

From the previous example, we can go up to _c_:

```
#0	d (d=0) at hello.c:3
#1	0x080483eb in c (c=1) at hello.c:5
#2	0x080483fb in b (b=2) at hello.c:7
#3	0x0804840b in a (a=3) at hello.c:9
#4 	0x0804841b in main (argc=1, argv=0xffffd154) at hello.c:13
```
```
(gdb) up
```
```
#1	0x080483eb in c (c=1) at hello.c:3
3 void b(int b) { c(1); }
```

&nbsp;

#### _down_

Similar to `up`, this command goes down one frame from the current frame.


&nbsp;

#### _info registers_

Lists the current values in commonly used registers. This is a command commonly used to debug assembly code and when writing operating system code.


```
(gdb) info registers
```
```
eax 	0xf7faddbc -134554180
ecx 	0xffffd0c0 -12096
edx 	0xffffd0e4 -12060
ebx 	0x0 0
esp 	0xffffd0a0 0xffffd0a0
ebp 	0xffffd0a8 0xffffd0a8
esi 	0xf7fac000 -134561792
edi 	0xf7fac000 -134561792
eip 	0x804841c 0x804841c <main+17>
eflags 	0x286 [ PF SF IF ]
cs 	0x23 35
ss 	0x2b 43
ds 	0x2b 43
es 	0x2b 43
fs 	0x0 0
gs 	0x63 99
```

&nbsp;

### 6.4 How debuggers work: A brief introduction

&nbsp;

#### 6.4.1 How breakpoints work

When a programmer places a breakpoint somewhere in the code, what happens is that the first opcode of the first instruction is replaced  with another instruction, `int 3` with opcode `CCh`.
```
   83	|   ec   |   0c		    cc   |  ec  |  0c
----------------------------  -->  ---------------------
       sub esp, 0x4			   int 3
```


Because `int 3` is only 1 byte long, it makes it efficient for debugging. When it is executed, the OS calls its breakpoint interrupt handler. The handler checks what process reached a breakpoint, pauses it and notifies the debugger it has paused a debugging section. Because the debugged program is only paused, the debugger has access to it's internal state. The debugger then replaces the `int 3` trigger with the original opcode and executes the instruction normally.

```
   cc	|   ec   |   0c		    83   |  ec  |  0c
----------------------------  -->  ---------------------
           int 3			 sub esp,0x4

```


&nbsp;


It is easier to see this in sction. First, we add a `int 3` instruction where we need _gdb_ to stop:

```c
#include <stdio.h>

int main(int argc, char *argv[]) {
	asm("int 3");
	printf("Hello World!\n");
	return 0;
}
```
`int 3` precedes `printf`, so the debugger stops at `printf`. 

Compile with the intel syntax:
```
$ gcc -masm=intel -m32 -g hello.c -o hello
```

Then run in _gdb_:
```
$ gdb hello
```
```
(gdb) r
```

```
Starting program: /tmp/hello
Program recieved signal SIGTRAP, Trace/breakpoint trap.
main (argc=1, argv=0xffffd154) at hello.c:6
6	printf("Hello World!\n");
```

&nbsp;

#### 6.4.2  Single Stepping

A debugger simply places anothet `int 3` opcode in the next instruction. This enables instruction by instruction debugging. Similarly, source line by line debugging is just the placement of the very first opcode in the two statements with two `int 3` opcodes.

&nbsp;

#### 6.4.3 How a debugger understands high level source code


_DWARF_ is a file format used by many debuggers to support high level source code debugging. The file contains information that maps between entries in the exeutable binary with source files. A program entity can either be data or code. A ___DIE___, or _Debugging Information Entry_ is a description of a program entry. It consists of a tag, which separates the entity that the DIE describes, and a list o attributes that describe the entry. The two attributes that enable source-level debugging are:

* Where the entiy appears in the source file: which file and which line.
* Where the entry appears in the executable binary: the memory address that it is loaded in at runtime. With the correct address, _gdb_ can retrieve correct value for a data entry, or place a correct breakpoint. 

```

hello.c							DIE
------------------------------------------	   --------------------	
   Line 1  #include <stdio.h>				...
   Line 2						...
-> Line 3  int main(int argc, char *argv[])	->  	main in hello.c at
   Line 5  .............				0x804840b in hello
   Line 6  .............				...
							...
						   ---------------------

							| ^
							v |

						   hello ()
						   -------------------
						   ... 8d 4c 24 04 e4 f0
						   ff 71 fc

```

In addition to DIE's, there is another binary-to-source mapping names _line umber table_. It maps between a line in the source code and the memory address at which it starts in the executable binary.

Address matching between the image layout of the ELF binary and the address wherer it is loaded is very important, as debug information relies on the correct loading address at runtime. That is, it assumes that the address recorded in the binary image at compile time are the same as runtime. Whithout this correlation, debugging is useless as it confuses lines.

&nbsp;


You can see the debugging information if you _readelf_ the binary file:

```c
#include <stdio.h>

int main(int argc, char *argv[]){
	printf("Hello World!\n");
	return 0;
}
```

Compile with debug info:
```
$ gcc -m32 -g hello.c -o hello
```

Read the binary, more specifically the line number table:
```
$ readef -wL hello
```

`-w` prints all the debug information. In combination with `-L`, only the line number table is displayed.


```
Decoded dump of debug contents of section .debug_line:
CU: hello.c:
File name 		Line number 	Starting address
hello.c 			  6 		0x804840b
hello.c 			  7 		0x804841c
hello.c 			  9 		0x804842c
hello.c 			 10 		0x8048431
```


_CU_ stands for _Compilation Unit_, a separate compiled source file.
_File name_ displays the filename of the current compilation unit.
_Line number_ is the line number in the source file that is not empty. In the example, line 8 is an empty line, so it does not appear.
_Starting address_ is the memory address where the line actually starts in the executable binary.



&nbsp;

To lookat the DIE information, run:
```
$ readelf -wi hello
```

`-wi` lists all the DIE entries. This is one typical DIE entry:

```
<0><b>: Abbrev Number: 1 (DW_TAG_compile_unit)
   <c>   DW_AT_producer   : (indirect string, offset: 0xe): GNU C11 5.4.0 20160609 -masm=intel -m32 
   <10>    DW_AT_language  : 12 (ANSI C99)
   <11>    DW_AT_name      : (indirect string, offset: 0xbe): hello.c
   <15>    DW_AT_comp_dir  : (indirect string, offset: 0x97): /tmp
   <19>    DW_AT_low_pc    : 0x804840b
   <1d>    DW_AT_high_pc   : 0x2e
   <21>    DW_AT_stmt_list : 0x0
```


The `<0> ` at the beginning indicated the current nesting level of a DIE entry. 0 is the outer-most level DIE, with it's entry in the compilation unit. This means subsequent DIE entries with a higher nesting level are all children of this tag.

The numbers in the `<10>` breakets are in hex format and indicate the offset into _.debug_info_ section. When an attribute references to another attribute, the offset is used to precisely identity the referenced attribute.

The `DW_AT_` prefix are the attributes attached to a DIE that describe an entry. Most notable are:

_DW_AT_name_     
_SW_AT_camp_dir_ : the finelame of the compilation unit and the directory where the compilation occured. This leads _gdb_ to the place of the source code.
_DW_AT_low_pc_   
_DW_AT_high_pc_ : the start and end of current entry. _low_pc_ is the starting address. _high_pc_ is the size of the compilation unit. When adding them together, it results the end of he entity. 

In this example, code compiled from _hello.c_ starts at 0x804840b and ends at 0c804840b + 0x2e = 0x8048439. To make sure, we can verify with _objdump_.


```
int main(int argc, char *argv[])
{
  804840b: 	8d 4c 24 04 		lea 	ecx,[esp+0x4]
  804840f: 	83 e4 f0 		and 	esp,0xfffffff0
  8048412: 	ff 71 fc 		push 	DWORD PTR [ecx-0x4]
  8048415: 	55 			push 	ebp
  8048416: 	89 e5 			mov 	ebp,esp
  8048418: 	51  			push 	ecx
  8048419: 	83 ec 04 		sub 	esp,0x4

printf("Hello World\n");
  804841c: 	83 ec 0c 		sub 	esp,0xc
  804841f: 	68 c0 84 04 08 		push 	0x80484c0
  8048424: 	e8 b7 fe ff ff 		call 	80482e0 <puts@plt>
  8048429: 	83 c4 10 		add 	esp,0x10

return 0;
  804842c: 	b8 00 00 00 00 		mov 	eax,0x0
}
  8048431: 	8b 4d fc 		mov 	ecx,DWORD PTR [ebp-0x4]
  8048434: 	c9 			leave
  8048435: 	8d 61 fc 		lea 	esp,[ecx-0x4]
  8048438: 	c3 			ret
  8048439: 	66 90 			xchg 	ax,ax
  804843b: 	66 90  			xchg 	ax,ax
  804843d: 	66 90 			xchg ax,ax
  804843f: 	90 			nop
```

`main` starts at 804840b and ends at 8048439, riht after the `ret` instruction at 80484438. The instructions after that are just padding bytes inserted by _gcc_ for allignment.


`Abbrev Number: 1` displays the abbreviation form of a tag. An abbreviation is the form of a DIE. `-wi` displays the DIEs with their values. To show only the abbreviation, you can use `-wa`. In this case, the output would be:

```
Contents of the .debug_abbrev section:
  Number TAG (0x0)
   1 	DW_TAG_compile_unit [has children]
    DW_AT_producer 	DW_FORM_strp
    DW_AT_language 	DW_FORM_data1
    DW_AT_name 		DW_FORM_strp
    DW_AT_comp_dir 	DW_FORM_strp
    DW_AT_low_pc 	DW_FORM_addr
    DW_AT_high_pc 	DW_FORM_data4
    DW_AT_stmt_list 	DW_FORM_sec_offset
    DW_AT value: 0 	DW_FORM value: 0
.... more abbreviations ....
```

We can also say that abbreviation is a type of a DIE, as it represents the structure of a particular DIE. Many share the same abbreviation, or structure, thus being the same type. Abbreviation improve encoding efficiency (reducr binary size) becasuse each DIE does not need to carry their structure information as pairs of attribute-value, but simply refers to an abbreviation for correct decoding.

Here are all the DIEs represented as a tree:


TODO: picture



`DW_TAG_subprogram` representa a function such as `main`. It's children are the DIEs of _argc_ and _argv_. 

If more than one compilation exists in the executable binary, the DIE entries are sorted according to the compilation order. For example, suppose we have another _test.c_ source file and compile it together with hello:

```
$ gcc -masm=intel -m32 -g test.c hello.c -o hello
```
```
<0><b>: Abbrev Number: 1 (DW_TAG_compile_unit)

   <c> DW_AT_producer 	: (indirect string, offset: 0x0): GNU C11 5.4.0 20160609-masm=intel -m32 -mtune=generic -march=i686 -g -fstack-protector-strong
	<10> DW_AT_language 	: 12 	(ANSI C99)
	<11> DW_AT_name 		: (indirect string, offset: 0x64): test.c
	<15> DW_AT_comp_dir 	: (indirect string, offset: 0x5f): /tmp
	<19> DW_AT_low_pc 	: 0x804840b
	<1d> DW_AT_high_pc 	: 0x6
	<21> DW_AT_stmt_list 	: 0x0
<1><25>: Abbrev Number: 2 (DW_TAG_subprogram)
	<26> DW_AT_external 	: 1
	<26> DW_AT_name 	: bar
	<2a> DW_AT_decl_file 	: 1
	<2b> DW_AT_decl_line 	: 1
	<2c> DW_AT_low_pc 	: 0x804840b
	<30> DW_AT_high_pc 	: 0x6
	<34> DW_AT_frame_base 	: 1 byte block: 9c (DW_OP_call_frame_cfa)
	<36> DW_AT_GNU_all_call_sites: 1
....after all DIEs in test.c listed....
<0><42>: Abbrev Number: 1 (DW_TAG_compile_unit)
	<43> DW_AT_producer 	: (indirect string, offset: 0x0): GNU C11 5.4.0 20160609-masm=intel -m32 -mtune=generic -march=i686 -g -fstack-protector-strong
	<47> DW_AT_language 	: 12 (ANSI C99)
	<48> DW_AT_name 	: (indirect string, offset: 0xc5): hello.c
	<4c> DW_AT_comp_dir 	: (indirect string, offset: 0x5f): /tmp
	<50> DW_AT_low_pc 	: 0x8048411
	<54> DW_AT_high_pc 	: 0x2e
	<58> DW_AT_stmt_list 	: 0x35
....then all DIEs in hello.c are listed....
```
