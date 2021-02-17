
## The Anatomy Of A Program


Each program contains data and code. The hardware however, cannot distinguish between the two, so there is also _metadata_ to tell it which region of memory should be protected or modified, what to run, etc. When a program is compiled, it is stored in an _object_ file, which is just a block of binary. This can be combined with other object files to create an _executable_ file, that can run on a specific operating system.

___readelf___ is a program designed to display the _ELF_ metadata of a binary file, object or executable. It stands for _Executable and Linkable Format_, and is located at the top of the file, to provide the OS with info regarding main memory and execution. It lists sections of code and data, together with the memory addresses and other information. 

It is composed of :

* An _ELF headher_ : Describes file's organization.
* A _program header table_ : Array of fixed size structures that describes segments of executable.
* A _section header table_ : Fized size array that describes sections of executable.
* _Segments and sections_ : The code and data information, divided into segments.
   A _segment_ is comprised of sections and is directly loaded by an OS at runtime.
   A _section_ is a block of binary:

    * Code or Data
    * Metadata


TODO: Photo.

Later, we will compile out kernel as an ELF executable with GCC, and explicitly specify how segments are created and where they are loaded in memory using a _linker script_, a text file to instruct how a linker should generate binary. 



### 5.1 Reference Documents

The ELF specifications for x86 are maintained on Github. You can access them here.

TODO: Link https://github.com/hjl-tools/x86-psABI/wiki/X86-psABI


### 5.2 ELF header

To see the information of the ELF header:
```
$ readelf -h hello
```

The output:

```
ELF Header:
  Magic: 	7f 45 4c 46 02 01 01 00 00 00 00 00 00 00 00 00
  Class: 				ELF64
  Data: 				2's complement, little endian
  Version: 				1 (current)
  OS/ABI: 				UNIX - System V
  ABI Version: 				0
  Type: 				EXEC (Executable file)
  Machine: 				Advanced Micro Devices X86-64
  Version: 				0x1
  Entry point address: 			0x400430
  Start of program headers: 		64 (bytes into file)
  Start of section headers: 		6648 (bytes into file)
  Flags: 				0x0
  Size of this header: 			64 (bytes)
  Size of program headers: 		56 (bytes)
  Number of program headers: 		9
  Size of section headers: 		64 (bytes)
  Number of section headers: 		31
Section header string table index:  28
```



___Magic___ displays the raw bytes that uniquely addresses a file in an ELF extcutable. Each byte gives a brief information :
`7f 45 4c 46` Predefined values. The first byte is always `7F`, the remaining 3 bytes represent the string _ELF_.     
`02` Class field. Table below.    
`01` Data field. Table below.      
`01` Version field. Table below.      
`00` _OS/ABI_ field. Table below.       
`00 00 00 00 00 00 00 00 ` Padding bytes. Used for proper alignment and reserved for future use.        

A _Class_ byte in the Magic field specifies the class or capacity of a file. Possible values:
```
Value	   Description
------------------------
  0	  Invalid class
  1 	  32-bit objects
  2   	  64-bit objects
```

A _Data_ byte in the Magic field specifies the data encoding of the processor-specific data in the object file. Possible values:
```
Value	      Description
-------------------------------
  0	  Invalid data encoding
  1	  Little endian, 2's complement
  2	  Big endian, 2's complement
```

A _Version_ byte in the Magic specifies the ELF header version number :
```
Value	  Description
-----------------------
  0	Invalid version
  1	Current version
```

A _OS/ABI_ byte in a Magic field specifies the target OS _ABI_        

___Type___ identifies the object file type.         
```
Value	   Description
-----------------------
  0	 No file type
  1	 Realocatable file
  2	 Executable file
  3	 Shared object file
  4	 Core file
0xff00	 Processor specific, lower bound
0cffff	 Processor specific, upper bound
```

The values `0xff00` and `0xffff` are reserved for the processor to define additional file types.

___Machine___ specifies the required architecture, eg. x86_64, MIPS, SPARC, etc. 

___Version___ specifies he version number of the current _object file_ (not the ELF headher version).

___Entry point address___ specifies the first code to be executed. The address of the _main_ function is the default, but it can be any function if explicitly specified to gcc. 


___Start of program headers___ is the offset of the program header table, in bytes. This number is 64 bytes, meaning the 65th byte is the start address of the program header table. As an example, if your program is loaded at address 0x10000 in memory, the start address is 0x10000 where the Magic fiels is, with the initial value 0x7f. The start of the program header would be 0x10000 + 0x40 = 0x10040.

___Start of section headers___ is the offset of the section header table in bytes.

___Flags___ hold processor-specific flags associated with the file. In a x86 machine, _EFLAGS_ register is set according to this value. 

___Size of this header___ specifies the total size of the ELF header in bytes. Here it is 64 bytes, equivalent to the _Start of program header_. Thise are not necessarily equivalent, as the program header might be placed away from the ELF header.


___Size of program headers___ specifies the size of each program header in bytes.

___Number of program headers___ specifies the total number of program headers.

___Size of section headers___ specifies the size of each section header in bytes.

___Number of section headers___ I wonder what this is.

___Section header string table index___ specifies the index of the header in the section header  that points to the section that holds all null-terminated strings.



### 5.3 Section header table


The smaller code or data sections must satisfy the following conditions :

* Every section has one header describing it but, section headers may exist without a section.
* Each section occupied one contiguous (possible empty) sequence of bytes within a file. There should be no two regions of bytes that are the same section.
* Sections in a file may not overlap. No byte is in two sections.
* There is no need for the sections to cover every byte in a file. There may be inactive space.

To get all the headers from an executable binary, use the command :
```
$ readelf -S hello
```

Sample output :
```
There are 31 section headers, starting at offset 0x19c8:
Section Headers:
   [Nr] Name 		Type 		Address 	Offset
	Size 		EntSize 	Flags Link  Info Align
[ 0] 			NULL 		0000000000000000 00000000
	0000000000000000 0000000000000000 	0 	0 	0
[ 1] 	.interp 	PROGBITS 	0000000000400238 00000238
	000000000000001c 0000000000000000    A    0    0     1
[ 2] 	.note.ABI-tag 	NOTE 		0000000000400254 00000254
	0000000000000020 0000000000000000    A    0    0     4
[ 3] 	.note.gnu.build-i NOTE 0000000000400274 00000274
	0000000000000024 0000000000000000    A    0    0     4
[ 4] 	.gnu.hash 	GNU_HASH 	0000000000400298 00000298
	000000000000001c 0000000000000000    A    5    0     8
[ 5] 	.dynsym 	DYNSYM 		00000000004002b8 000002b8
	0000000000000048 0000000000000018    A    6    1     8
[ 6] 	.dynstr 	STRTAB 		0000000000400300 00000300
	0000000000000038 0000000000000000    A    0    0     1
[ 7] 	.gnu.version 	VERSYM 		0000000000400338 00000338
	0000000000000006 0000000000000002    A    5    0     2
[ 8] 	.gnu.version_r 	VERNEED 	0000000000400340 00000340
	0000000000000020 0000000000000000    A    6    1     8
[ 9] 	.rela.dyn 	RELA 		0000000000400360 00000360
	0000000000000018 0000000000000018    A    5    0     8
[10] 	.rela.plt 	RELA 		0000000000400378 00000378
	0000000000000018 0000000000000018    AI   5    24    8
[11] 	.init 		PROGBITS 	0000000000400390 00000390
	000000000000001a 0000000000000000    AX   0    0     4
[12] 	.plt 		PROGBITS 	00000000004003b0 000003b0
	0000000000000020 0000000000000010    AX   0    0     16
[13] 	.plt.got 	PROGBITS 	00000000004003d0 000003d0
	0000000000000008 0000000000000000    AX   0    0     8
[14] 	.text 		PROGBITS 	00000000004003e0 000003e0
	0000000000000192 0000000000000000    AX   0    0     16
[15] 	.fini 		PROGBITS 	0000000000400574 00000574
	0000000000000009 0000000000000000    AX   0    0     4
[16] 	.rodata 	PROGBITS 	0000000000400580 00000580
	0000000000000004 0000000000000004    AM   0    0     4
[17] 	.eh_frame_hdr 	PROGBITS 	0000000000400584 00000584
	000000000000003c 0000000000000000    A    0    0     4
[18] 	.eh_frame 	PROGBITS 	00000000004005c0 000005c0
	0000000000000114 0000000000000000    A    0    0     8
[19] 	.init_array 	INIT_ARRAY 	0000000000600e10 00000e10
	0000000000000008 0000000000000000    WA   0    0     8
[20] 	.fini_array 	FINI_ARRAY 	0000000000600e18 00000e18
	0000000000000008 0000000000000000    WA   0    0     8
[21] 	.jcr 		PROGBITS 	0000000000600e20 00000e20
	0000000000000008 0000000000000000    WA   0    0     8
[22] 	.dynamic 	DYNAMIC 	0000000000600e28 00000e28
	00000000000001d0 0000000000000010    WA   6    0     8
[23] 	.got 		PROGBITS 	0000000000600ff8 00000ff8
	0000000000000008 0000000000000008    WA   0    0     8
[24] 	.got.plt 	PROGBITS 	0000000000601000 00001000
	0000000000000020 0000000000000008    WA   0    0     8
[25] 	.data 		PROGBITS 	0000000000601020 00001020
	0000000000000010 0000000000000000    WA   0    0     8
[26] 	.bss 		NOBITS 		0000000000601030 00001030
	0000000000000008 0000000000000000    WA   0    0     1
[27] 	.comment 	PROGBITS 	0000000000000000 00001030
	0000000000000034 0000000000000001    MS   0    0     1
[28] 	.shstrtab 	STRTAB 		0000000000000000 000018b6
	000000000000010c 0000000000000000         0    0     1
[29] 	.symtab 	SYMTAB 		0000000000000000 00001068
	0000000000000648 0000000000000018 	  30   47    8
[30] 	.strtab 	STRTAB 		0000000000000000 000016b0
	0000000000000206 0000000000000000 	  0    0     1
Key to Flags:
W (write), A (alloc), X (execute), M (merge), S (strings), l (large)
I (info), L (link order), G (group), T (TLS), E (exclude), x (unknown)
O (extra OS processing required) o (OS specific), p (processor specific)
```


The first line summarizes the total number of sections in the file and the address where it starts. 
```
There are 31 section headers, starting at offset 0x19c8:
```
Then, all the sections are listed in the format :
```
[Nr]	Name		Type		Address		    Offset
	Size		EntSize		Flags  Links  Info  Align
```

___Nr___ is the index of each section.
___Name___ is the name of each section.
___Type___ is the type of each section.
___Address___ the starting _virtual_ address of each section. 
___Offset___ is the distance in bytes from the first byte of a file to the start of a section.
___Size___ size in bytes of each section.
___EntSize___ Some sections hold a table of fixes-size entries. For each section, this member gives the size in bytes of each entry. The number is 0 if the section does not hold a table of fixed-size entries.
___Flags___ describes attributes of a section. Flags together with a type define the purpose of a section. Two sections can of the same type but serve different purposes. For example, _.text_ and _.data_ sections are the same type. But, _.data_ holds the initialized data of a program and is given read and write permission, but not executable, while _.text_ holds executable instructions of a program.

ELF gives information to enable an OS with such protection mechanism. However, running on bare metal, nothing can prevent from doing anything. Our OS could execute _.data_ sections and modify _.text_.


TODO: Section Flags Table


___Link and Info___ are numbers that reference the indexes of sections, symbol table entries, hash table entries. ___Link___ field only holds the index of a section, while ___Info___ field holds an index of a section, a symbol table entry or hash table entry, depending on the type of section.

Later, when witing the OS, we will handcraft the kernel image by explicitly linking the object files produced by gcc through a linker script. We will specify the memory layout of sections by specifying at what address they will appear in the final image. 


___Align___ is a value that enforces the offset of a section should be divisible by the value. Only 0 and positive interal powers of 2 are allowed. Values 0 and 1 mean the section has no alignment constraint. 


Example


Output of the _.interp_ section :
```
[Nr]	Name		 Type		 Address 	    Offset
	Size		 EntSize	 Flags  Links  Info  Align

[ 1]	.interp		 PROGIBITS	 0000000000400238  00000238
	000000000000001c 0000000000000000  A      0	 0     1	
```

_Nr_ is 1.
_Type_ is _PROGBITS_, meaning it's part of the program.
_Address_ is 0x000000000400238, meaning program is loading into this virtual memory at runtime.
_Offset_ is 0x00000238 bytes into the file.
_Size_ is 0x0000000000001c in bytes.
_EntSize_ is 0, meaning no fixes-size entry.
_Flags_ are A (Allocatable), meaning it consumes memory at runtime.
_Info_ and _Link_ are 0 and 0, meaning this section links to no other section or enty in any table.
_Align_ is 1, meaning no alignment.

Example 2

```
[14] .text		PROGBITS	00000000004003e0  000003e0
     0000000000000192	00000000000000	   AX     0     0    16
```
_Nr_ is 14.
_Type_ is _PROGBITS_.
_Address_ is 0x000000000004003e0.
_Offset_ is 0x000003e0 bytes into the file.
_Size_ is 00000000000000192 in bytes.
_EntSIze_ is 0.
_Flags_ are A (Allocatable) and X (Executable), meaning it consumes memory and can be executed as code at runtime.
_Info_ and _Link_ are 0 and 0, meaning no connections.
_Align_ is 16, meaning the starting address of the section should be divisible by 16, or 0x10, which it is: 0x3e0/0x10 = 0x3e.


### 5.4 Understand Section in-depth

We will be examining the contents of each section as a hexdump with the command :
```
$ readelf -x <section name | section number> <file>
```
For section with index 25 in the file _hello_:
```
$ readelf -x 25 hello
```
or using the name:
```
$ readelf -x .data hello
```
If a section contains strings, the flag _-x_ can be replaced with _-p_.


___NULL___ marks a section header as inactive and does not have an associated section. It is always the first entry of the section header table and means the actual information starts from 1.

Example

```
[Nr]	Name		  Type		Address		  Offset
	Size		  EntSize	Flags  Link  Info  Align
[ 0]			  NULL		000000000000000  00000000
	00000000000000000 0000000000000		 0     0    0
```
Exaining this section, we find that it is empty:
```
Section '' has no data to dump.
```



___NOTE___ marks a section with special information that other programs will check for compatibility, conformance. For example, by a vendor or a system builder.

Example

```
[Nr]	Name		  Type		Address		  Offset
	Size		  EntSize	Flags  Link  Info  Align

[ 2]	.note.ABI-tag	  NOTE		0000000000400254 00000254
	0000000000000020  0000000000000   A      0     0     4

[ 3]	.note.gnu.build-i NOTE		0000000000400274 00000274
	0000000000000024  0000000000000   A      0     0     4
```

Examining the second section with the command:
```
$ readelf -x 2 hello
```
```
Hex dump of section '.note.ABI-tag':
  0x00400254 040000000 10000000 01000000 474e5500 ............GNU.
  0x00400264 000000000 02000000 06000000 20000000 ............ ...
```

___PROGBITS___ indicates a section holding the main content of the program, either code or data.

Example

```
[Nr]	Name 		 Type 		  Address 	    Offset
	Size 		 EntSize 	  Flags  Link  Info  Align

[ 1] 	.interp 	 PROGBITS 	  0000000000400238 00000238
	000000000000001c 0000000000000000   A      0     0     1

...
[11] 	.init 		PROGBITS 	  0000000000400390 00000390
	000000000000001a 0000000000000000   AX     0     0     4

[12] 	.plt 		PROGBITS 	  00000000004003b0 000003b0
	0000000000000020 0000000000000010   AX     0     0     16

[13] 	.plt.got 	PROGBITS 	  00000000004003d0 000003d0
	0000000000000008 0000000000000000   AX     0     0     8

[14] 	.text 		PROGBITS 	  00000000004003e0 000003e0
	0000000000000192 0000000000000000   AX     0     0     16

[15] 	.fini 		PROGBITS 	  0000000000400574 00000574
	0000000000000009 0000000000000000   AX     0     0     4

[16] 	.rodata 	PROGBITS 	  0000000000400580 00000580
	0000000000000004 0000000000000004   AM     0     0     4

[17] 	.eh_frame_hdr 	PROGBITS 	  0000000000400584 00000584
	000000000000003c 0000000000000000   A      0 	 0     4

[18] 	.eh_frame 	PROGBITS 	  00000000004005c0 000005c0
	0000000000000114 0000000000000000   A      0     0     8

...
[23] 	.got 		PROGBITS 	  0000000000600ff8 00000ff8
	0000000000000008 0000000000000008   WA     0     0     8

[24] 	.got.plt 	PROGBITS 	  0000000000601000 00001000
	0000000000000020 0000000000000008   WA     0     0     8

[25] 	.data 		PROGBITS 	  0000000000601020 00001020
	0000000000000010 0000000000000000   WA     0     0     8

[27] 	.comment 	PROGBITS 	  0000000000000000 00001030
	0000000000000034 0000000000000001   MS     0 	 0     1
```

For out operating system, we need the following sections :

___.text___ holds all the compiled code.     
___.data___ holds initialized data of the program. Since the data is initialized with values, _gcc_ allocated the section with actual bytes in the executable binary.
___.rodata___ holds read-only data.
___.bss___, short for _Block Started by Symbol_, holds uninitialized data of the program. No space is allocated for this section in the executable binary. It is allocated when program is loaded in memory.

There are other sections that are mainly needed for dynamic linking between programs. For such features, the OS must be a runtime envirionment. Because we work on bare metal, we are creating such an environment For simplicity we will not include those sections.


___SYMTAB___ and ___DYNSYM___ hold symbol tables. A _symbol table_ is an array of entries that describe symbols in a program. A _symbol_ is a name assigned to an entity in a program. The type of the entity is also the sype of the symbol. Possible types on entity:

Example

```
[Nr]	Name 		 Type 		  Address 	    Offset
	Size 		 EntSize 	  Flags  Link  Info  Align

[ 5]	.dynsym		 DYNSYM		  00000000004002b8 000002b8
	0000000000000048 000000000000018    A      6     1     8

...
[29]	.symtab		 SYMTAB		  0000000000000000 00001068
	0000000000000648 000000000000018	   30	 47    8
```
To show the simbol table:
```
$ readelf -s hello
```
Output has 2 symbol tables, coresponding to two sections above.
``` 
Symbol table '.dynsym' contains 4 entries:
    Num:      Value        Size Type     Bind    Vis      Ndx Name
     0: 0000000000000000     0 NOTYPE    LOCAL   DEFAULT  UND
     1: 0000000000000000     0 FUNC 	 GLOBAL  DEFAULT  UND puts@GLIBC_2.2.5 (2)
     2: 0000000000000000     0 FUNC 	 GLOBAL  DEFAULT  UND __libc_start_main@GLIBC_2.2.5 (2)
     3: 0000000000000000     0 NOTYPE 	 WEAK 	 DEFAULT  UND __gmon_start__

Symbol table '.symtab' contains 67 entries:
    Num:      Value 	   Size Type 	 Bind 	 Vis 	  Ndx Name
     ..........................................
     59: 0000000000601040    0 NOTYPE 	 GLOBAL  DEFAULT  26 _end
     60: 0000000000400430    42 FUNC 	 GLOBAL  DEFAULT  14 _start
     61: 0000000000601038    0 NOTYPE 	 GLOBAL  DEFAULT  26 __bss_start
     62: 0000000000400526    32 FUNC 	 GLOBAL  DEFAULT  14 main
     63: 0000000000000000    0 NOTYPE    WEAK    DEFAULT  UND _Jv_RegisterClasses
     64: 0000000000601038    0 OBJECT    GLOBAL  HIDDEN   25 __TMC_END__
     65: 0000000000000000    0 NOTYPE    WEAK    DEFAULT  UND _ITM_registerTMCloneTable
     66: 00000000004003c8    0 FUNC      GLOBAL  DEFAULT  11 _init
```
___TLS___ is the symbol associated with _Thread-Logal-Storage_.
___Num___ is the index of the table entry.
___Value___ is the virtual memory address where the symbol is located.
___Size___ is the size of the entry.
___Type___ is a symbol type:
 	_NOTYPE_ :  means type is not specified.
 	_OBJECT_ :  means the symbol is a data object. In C, any variable definition is of type _OBJECT_
 	_FUNC_ : the symbol is associated with a function or executable code.
 	_SECTION_ : assicoated with a section, exists primarily for realocation.
 	_FILE_ : the symbol is the name of a source file associated with an executable binary.
 	_COMMON_ : the symbol labels an uninitialized variable. This is when, in C, a global variable is declared but not initialized, or initialized with an external keyward. Those variables are in _.bss_.

___Bind___ is the scope of the symbol.



__Local__ are symbols only visible in the object file that defined them.In C, the _static_ keyward marks a symbol as local to only the file that defines it.

Example

```c
static int global_static_var = 0;

static void local_func() {}

int main(int argc, char *argv[]) {
	static int local_static_var = 0;
	return 0
}
```

After compiling, we get the static variables listed as local symbols :
```
$ gcc -m32 hello.c -o hello
```
```
$ readelf -s hello
```
```
Symbol table '.dynsym' contains 5 entries:
   Num:  Value       Size Type     Bind     Vis    Ndx Name
    0:  00000000       0 NOTYPE    LOCAL   DEFAULT   UND
    1:  00000000       0 FUNC      GLOBAL  DEFAULT   UND puts@GLIBC_2.0 (2)
    2:  00000000       0 NOTYPE    WEAK    DEFAULT   UND __gmon_start__
    3:  00000000       0 FUNC      GLOBAL  DEFAULT   UND __libc_start_main@GLIBC_2.0 (2)
    4:  080484bc       4 OBJECT    GLOBAL  DEFAULT   16 _IO_stdin_used

Symbol table '.symtab' contains 72 entries:
   Num:  Value       Size Type     Bind     Vis    Ndx Name
    0: 00000000         0 NOTYPE   LOCAL    DEFAULT  UND
......... output omitted .........
   38: 0804a020         4 OBJECT   LOCAL    DEFAULT  26 global_static_var
   39: 0804840b         6 FUNC     LOCAL    DEFAULT  14 local_func
   40: 0804a024         4 OBJECT   LOCAL    DEFAULT  26 local_static_var.1938
......... output omitted .........
```


__Global__ are symbols that are accesible by other object files when linking together. Those are non-static functions and non-static global data. The _extern_ modifier marks a symbol as externally defined but accessible in the final exeutable binary, making it _GLOBAL_.

Example

```
   Num:  Value       Size Type     Bind     Vis    Ndx Name
    66: 080483e1       10 FUNC     GLOBAL  DEFAULT 14 main
```

__WEAK__ are symbols whose definition can be redefined. Normally, a symbol with multiple definitions is marked as an error by the compiler. When a definition is marked as _WEAK_, this is not the case.

Example

```c
#include <stdio.h>


__attribute__((weak)) int add(int a, int b) {
	printf("warning: function is not implemented.");
	return 0;
}


int main(int argc, char *argv[]) {
	printf("add(1,2) is %d\n", add(1,2));
	return 0;
}
```

`__attribute__((weak))` is a function attribute. A _function attribute_ is extra information for the compiler, noting that it should be handled differently then a normal function. In this case, it adds a weak function, who's implementation can be changed at link time. Attributes are a function of the compiler, not of C.

If we do not supply a different definition in a different file, the default is applied, printing the warning message.

However, if we define it like this in a file names _math.c_:
```c
int add(int a, int b) {
	return a + b;
}
```
and compile the files together:
```
$ gcc math.c hello.c -o hello
```
no message is printed and the correct value is returned. 


___Vis___ is the visibility of the symbol. The values are :
	
TODO: Table


___Ndx___ is the index of the section that the symbol is in. it also has those special values :

TODO: TABLE


___Name___ is the symbol name. 


Example :

```
Num:		Value   Size   Type      Bind   Vis    Ndx   Name
 62: 0000000000400526     32   FUNC     GLOBAL DEFAULT  14   main
```

`main` is the 62nd entry in the table, starts at address ox0000000000400526, consumes 32 bytes, is a function, is in global scope, is visible to oter files that use it, is inside the 14th section, which is _.text_.


___STRTAB___ holds a table of null-terminated strings, called _string table_. The first and last byte in this section is always a NULL character. It exists because a string can be reused by other sections to represent symbol and section names, so a program like _readelf_ or _objdump_ can display various objects in a program, e.g. variables, functions, section names, in human-readable text instead of raw hex address.

Example

```
[Nr]	Name 		 Type 		  Address 	    Offset
	Size 		 EntSize 	  Flags  Link  Info  Align

[28]	.shstrtab	 STRTAB		  0000000000000000 000018b6
	000000000000010c 000000000000000	   0     0     1

[30]	.strtab		 STRTAB		  0000000000000000 000016b0
	0000000000000206 000000000000000 	   0     0     1
```
_.shstrtab_ holds all the section names.
_.strtab_ holds the symbols e.g. variable names, function names, struct names, etc. It does not hold fixes-size null-terminated C strings, they are kept in the _.rodata_ section.

Reading the section:
```
$ readelf -p 29 hello
```
```
String dump of section '.shstrtab':
	[ 	 1] .symtab
	[ 	 9] .strtab
	[   	11] .shstrtab
	[ 	1b] .interp
	[ 	23] .note.ABI-tag
	[ 	31] .note.gnu.build-id
	[ 	44] .gnu.hash
	[ 	4e] .dynsym
	[	56] .dynstr
	[ 	5e] .gnu.version
	[ 	6b] .gnu.version_r
	[ 	7a] .rela.dyn
	[ 	84] .rela.plt
	[ 	8e] .init
	[ 	94] .plt.got
	[ 	9d] .text
	[ 	a3] .fini
	[ 	a9] .rodata
	[ 	b1] .eh_frame_hdr
	[ 	bf] .eh_frame
	[ 	c9] .init_array
	[ 	d5] .fini_array
	[ 	e1] .jcr
	[ 	e6] .dynamic
	[ 	ef] .got.plt
	[ 	f8] .data
	[ 	fe] .bss
	[      103] .comment
```
The implementation of a string tabele is a contiguous array of null-terminated strings. The index of the string is the position of the first character in the array. For example, in the above dump, _.symtab_ is at index 1, NULL is at index 0. The length of _.symtab_ is 7, plus the NULL character, meaning 8 bytes in total. So, _.strtab_ starts at index 9, and so on.

TODO: Tables

The output of _.strtab_:
```
String dump of section '.strtab':
	[ 	  1] crtstuff.c
	[ 	  c] __JCR_LIST__
	[ 	 19] deregister_tm_clones
	[ 	 2e] __do_global_dtors_aux
	[ 	 44] completed.7585
	[ 	 53] __do_global_dtors_aux_fini_array_entry
	[ 	 7a] frame_dummy
	[ 	 86] __frame_dummy_init_array_entry
	[ 	 a5] hello.c
	[ 	 ad] __FRAME_END__
	[ 	 bb] __JCR_END__
	[ 	 c7] __init_array_end
	[ 	 d8] _DYNAMIC
	[ 	 e1] __init_array_start
	[ 	 f4] __GNU_EH_FRAME_HDR
	[ 	107] _GLOBAL_OFFSET_TABLE_
	[ 	11d] __libc_csu_fini
	[ 	12d] _ITM_deregisterTMCloneTable
	[ 	149] j
	[ 	14b] _edata
	[ 	152] __libc_start_main@@GLIBC_2.2.5
	[ 	171] __data_start
	[ 	17e] __gmon_start__
	[ 	18d] __dso_handle
	[ 	19a] _IO_stdin_used
	[ 	1a9] __libc_csu_init
	[ 	1b9] __bss_start
	[ 	1c5] main
	[ 	1ca] _Jv_RegisterClasses
	[ 	1de] __TMC_END__	
	[ 	1ea] _ITM_registerTMCloneTable
```


___HASH___ holds the symbol hash table, supporting symbol table access.
___DYNAMIC___ holds information for dynamic linking.
___NOBITS___ is similar to _PROGBITS_ but occupies no space.

Example


_.bss_ section holds uninitialized data, meaning the bytes could have any value. Until the OS loades the section into main memory, there is no need to allocate space for the binary image on disc to reduce the size of a binary file. The details from the _.bss_ :

```
[Nr]	Name 		 Type 		  Address 	    Offset
	Size 		 EntSize 	  Flags  Link  Info  Align

[26]	.bss		 NOBITS		  0000000000601038 00001038
	0000000000000008 0000000000000000   WA     0     0     1

[27]	.comment	 PROGBITS	  0000000000000000 00001038
	0000000000000034 0000000000000001   MS     0     0     1
```

The size of the _.bss_ section is 8 bytes, while the offset is the same for both, meaning that _.bss_ consumes no byte of the executable binary on disk.

The _.comment_ section has no starting address because it will be discarded once loaded into memory.


___REL___ holds realocation entries without exoplicit addends. This will be explained in 8.1.

___RELA___ holds realocation entries with explicit addends. This will be explained in 8.1.

___INT_ARRAY___ is an array of function pointers for program initialization. When a program is run, before getting to the _main()_ function, the code in _.init_ and in this section, _INT_ARRAY_ are executed first.

The reason this happens it to ensure that shared object files that have no _main()_ function are executed, and to estamblish a proper environment for _main()_ to run in. It also allows for more modularity, as the main applocation code is not responsible for establishing an environment for an external file, as the file itself does that.

We will not use any _.init_ or _INIT_ARRAY_ in out OS, for simplicity.


Example

```c
#include <stdio.h>

__attribute__((constructor)) static void init1() {
	printf("%s\n, __FUNCTION__");
}

__attribute__((constructor)) static void init2() {
	printf("%s\n, __FUNCTION__");
}

int main(int argc, char *argv[]) {
	printf("hello world\n");
	return 0;
}
```

This program automatically calls the constructor:
```
$ gcc -m32 hello.c -o hello
$ ./hello
init1
init2
hello world
```

Optionally, you can control the flow of constructors using a priority from 101 onward. The priorities 0 to 100 are reserved by _gcc_. If you want _init2_ to run before _init1_, you can set a higher priority:
```c
#include <stdio.h>

__attribute__((constructor(102))) static void init1() {
	printf("%s\n", __FUNCTION__);
}

__attribute__((constructor(101))) static void init2() {
	printf("%s\n", __FUNCTION__);
}

int main(int argc, char *argv[]) {
	printf("hello world\n");
	return 0;
}
```
```
$ gcc -m32 hello.c -o hello
$ ./hello
init2
init1
hello world
```

We can also add initialization functions another way :

```c
#include <stdio.h>

void init1() {
	printf("%s\n", __FUNCTION__);
}

void init2() {
	printf("%s\n", __FUNCTION__);
}

/* Without typedef, init is a definition of a function pointer. 
   With typedef, it is the declaration of a type. */

typedef void (*init)();

__attribute__((section(".init_array"))) init init_arr[2] = {init1, init2};

int main(int argc, char *argv[]) {
	printf("hello world\n");
	return 0;
}
```

The attribute `section(...)` puts a function in a perticular section rather than the default _.text_. Here, it is put in _init.array_. The section name can be anything.  Non-standard section names are often used for controlling the final binary layout of a compiled program. 


___FINI_ARRAY___ is an array of functional pointers for program termination, called after exiting _main()_. If the application terminates abnormally, such as through a _abort()_ or crash, the _.finit_array_ is ignored. 

Example

```c
__attribute__((destructor)) static void destructor() {
	printf("%s\n, __FUNCTION__");
}

int main(int argc, char *argv[]) {
	printf("hello world\n");
	return 0;
}
```
```
hello world
destructor
```

___PREINIT_ARRAY___ is an array of functional pointers that are invoked before all other initialization functions in _INIT_ARRAY_. The only way to put functions into this section is with the `section()` attribute.
```c
#include <stdio.h>

void preinit1() {
	printf("%s\n", __FUNCTION__);
}

void init1() {
	printf("%s\n", __FUNCTION__);
}

typedef void (*preinit)();
typedef void (*init);

__attribute((section(".preinit_array"))) preinit preinit_arr[2] = {preinit1};

__attribute((section(".preinit_array"))) init init_arr[2] = {init1};

int main(int argc, char *argv[]){
	printf("hello world\n");
	return 0;
}
```
```
preinit1
init1
hello world
```

___GROUP___ defines a section group, which is the same section that appears in different object files, but when merged into the final executable binary, only the one copy is kept and the rest are discarded. This section is only relevant in C++ object files, so we will not look at it further.



___SYMTAB_SHNDX___ is a section containing extended section indexes, that are associated with a symbol table. This section only appears when the _Ndx_ value of an entry in the symbol table exceeds the _LORESERVE_ value. This sectio then maps between a symbol and an actual index vale if a section header. 



When you understand sectio types, you can understand the values in _Link__ and _Info_ fields.

TODO: TABLE


### 5.5 Program header table



A _program header table_ is an array of program headers that defines the memory layout of a program at runtime.

A _program header_ is a description of a program segment.
A _program segment_ is a collection of related sections. When an OS loades a program, it only uses _segments_, not _sections_. 


To view the info inside a program header table, use the command:
```
$ readelf -l <binary file>
```

A program header has types :

_PHDR_ specifies the location and size of the program header itself.
_INTERP_ specifies location and size of null-terminated path name to invoke as an interpreter for linking runtime libraries. 
_LOAD_ specifies loadable segment.
_DYNAMIC_ specifies dynamic linking information.
_NOTE_ specifies the location and size of auxiliary information.
_TLS_ specifies _Thread-Local-Storage_ template, formed from the combination of all section with the flag TLS.
_GU_STACK_ indicates weather a program's stack should be executable or not. 


A segment also has permissions:

_R_ Read
_W_ Write
_E_ Executable


Example
```
Elf file type is EXEC (Executable file)
Entry point 0x400430
There are 9 program headers, starting at offset 64
Program Headers:
	Type 		Offset 		  VirtAddr 	      PhysAddr
			FileSiz     	  MemSiz 	      Flags Align
	PHDR 		0x0000000000000040 0x0000000000400040 0x0000000000400040
			0x00000000000001f8 0x00000000000001f8 R E    8
	INTERP 		0x0000000000000238 0x0000000000400238 0x0000000000400238
			0x000000000000001c 0x000000000000001c R      1
	    [Requesting program interpreter: /lib64/ld-linux-x86-64.so.2]
	LOAD 		0x0000000000000000 0x0000000000400000 0x0000000000400000
			0x000000000000070c 0x000000000000070c R E   200000
	LOAD 		0x0000000000000e10 0x0000000000600e10 0x0000000000600e10
			0x0000000000000228 0x0000000000000230 RW    200000
	DYNAMIC 	0x0000000000000e28 0x0000000000600e28 0x0000000000600e28
			0x00000000000001d0 0x00000000000001d0 RW     8
	NOTE 		0x0000000000000254 0x0000000000400254 0x0000000000400254
			0x0000000000000044 0x0000000000000044 R      4
	GNU_EH_FRAME 	0x00000000000005e4 0x00000000004005e4 0x00000000004005e4
			0x0000000000000034 0x0000000000000034 R      4
	GNU_STACK 	0x0000000000000000 0x0000000000000000 0x0000000000000000
			0x0000000000000000 0x0000000000000000 RW     10
	GNU_RELRO 	0x0000000000000e10 0x0000000000600e10 0x0000000000600e10
			0x00000000000001f0 0x00000000000001f0 R      1
Section to Segment mapping:
  Segment Sections...
    00
    01 .interp
    02 .interp .note.ABI-tag .note.gnu.build-id .gnu.hash .dynsym .dynstr
.gnu.version .gnu.version_r .rela.dyn .rela.plt .init .plt .plt.got .text .fini
.rodata .eh_frame_hdr .eh_frame
    03 .init_array .fini_array .jcr .dynamic .got .got.plt .data .bss
    04 .dynamic
    05 .note.ABI-tag .note.gnu.build-id
    06 .eh_frame_hdr
    07
    08 .init_array .fini_array .jcr .dynamic .got
```
_LOAD_ segments appear twice:
```

	LOAD 		0x0000000000000000 0x0000000000400000 0x0000000000400000
			0x000000000000070c 0x000000000000070c R E   200000

	LOAD 		0x0000000000000e10 0x0000000000600e10 0x0000000000600e10
			0x0000000000000228 0x0000000000000230 RW    200000
```

The reason there are two of them is because they have different permissions. The first has _Read_ and _Execute_ permissions. This is a _text_ segment. The lower has _Read_ and _Write_ permissions. This is a _data_ segment. 


_LOAD_ contains the following sections:
```
    02 .interp .note.ABI-tag .note.gnu.build-id .gnu.hash .dynsym .dynstr
.gnu.version .gnu.version_r .rela.dyn .rela.plt .init .plt .plt.got .text .fini
.rodata .eh_frame_hdr .eh_frame
    03 .init_array .fini_array .jcr .dynamic .got .got.plt .data .bss
```

The first number is the index in the program header table and the remaining text is a list of all the sections within the segment. 



### 5.6 Segments vs Sections


Why does the OS use segments not sections? A segment is the perspective of an OS, while a section is the perspective of a linker. Looking into the structure of a segment, we can see:

* A segment is a collenction of sections. Sections are logically grouped together by their attributes. For example, all sections in _LOAD_ segment are always loaded by the OS, all have the same permission, either a _RE_ for executable or _RW_ for data.

* By grouping sections into segments, it is easier for the OS to batch load sections just once at the start and end of a segment, instead of loading section by section.

* Since a segment is for loading a program and a sections is for linking a program, all the sections in a segment is _within its start and end virtual memory addresses of a segment_.


To understand the last point, consider an example of linking two object files:
_hello.c_
```c
#include <stdio.h>

int main(int argc, char *argv[]) {
	printf("Hello World\n");
	return 0;
}
```
_math.c_
```c
int add(int a, int b) {
	return a + b;
}
```
Compile the two source files as object files:
```
$ gcc -m32 -c math.c
$ gcc -m32 -c hello.c
```

Checking the sections of _math.0_:
```
$ readelf -S math.o
```
```
There are 11 section headers, starting at offset 0x1a8:
Section Headers:
  [Nr] Name 		Type 	 Addr     Off    Size   ES Flg Lk Inf Al
  [ 0] 			NULL 	 00000000 000000 000000 00      0  0  0
  [ 1] .text 		PROGBITS 00000000 000034 00000d 00 AX   0  0  1
  [ 2] .data 		PROGBITS 00000000 000041 000000 00 WA   0  0  1
  [ 3] .bss 		NOBITS   00000000 000041 000000 00 WA   0  0  1
  [ 4] .comment 	PROGBITS 00000000 000041 000035 01 MS   0  0  1
  [ 5] .note.GNU-stack  PROGBITS 00000000 000076 000000 00      0  0  1
  [ 6] .eh_frame        PROGBITS 00000000 000078 000038 00 A    0  0  4
  [ 7] .rel.eh_frame 	REL 	 00000000 00014c 000008 08 I    9  6  4
  [ 8] .shstrtab 	STRTAB   00000000 000154 000053 00      0  0  1
  [ 9] .symtab 		SYMTAB   00000000 0000b0 000090 10      10 8  4
  [10] .strtab 		STRTAB   00000000 000140 00000c 00      0  0  1
Key to Flags:
W (write), A (alloc), X (execute), M (merge), S (strings)
I (info), L (link order), G (group), T (TLS), E (exclude), x (unknown)
O (extra OS processing required) o (OS specific), p (processor specific)
```

All the section virtual memory address of every address is set to 0. At this stage, each object file is simply a _block of binary_ that contains code and data. It exists to serve as a material container for the final exeutable binary.

No segments exist at this stage:
```
$ readelf -l math.o
There are no program headers in this file
```

The same is true for the _hello.o_ file.

Only when object files are combined into a final executable binary, sections are fully realised:
```
$ gcc -m32 math.o hello.o -o hello
$ readelf -S hello
```
```
There are 31 section headers, starting at offset 0x1804:
Section Headers:
  [Nr] Name 		 Type 	  Addr 	   Off    Size   ES Flg Lk Inf Al
  [ 0] 		 	 NULL 	  00000000 000000 000000 00      0  0  0
  [ 1] .interp 		 PROGBITS 08048154 000154 000013 00  A   0  0  1
  [ 2] .note.ABI-tag  	 NOTE 	  08048168 000168 000020 00  A   0  0  4
  [ 3] .note.gnu.build-i NOTE 	  08048188 000188 000024 00  A   0  0  4
  [ 4] .gnu.hash 	 GNU_HASH 080481ac 0001ac 000020 04  A   5  0  4
  [ 5] .dynsym 		 DYNSYM   080481cc 0001cc 000050 10  A   6  1  4
  [ 6] .dynstr     	 STRTAB   0804821c 00021c 00004a 00  A   0  0  1
  [ 7] .gnu.version 	 VERSYM   08048266 000266 00000a 02  A   5  0  2
  [ 8] .gnu.version_r    VERNEED  08048270 000270 000020 00  A   6  1  4
  [ 9] .rel.dyn 	 REL      08048290 000290 000008 08  A   5  0  4
  [10] .rel.plt    	 REL  	  08048298 000298 000010 08  AI  5  24 4
  [11] .init 		 PROGBITS 080482a8 0002a8 000023 00  AX  0  0  4
  [12] .plt 		 PROGBITS 080482d0 0002d0 000030 04  AX  0  0  16
  [13] .plt.got   	 PROGBITS 08048300 000300 000008 00  AX  0  0  8
  [14] .text  	 	 PROGBITS 08048310 000310 0001a2 00  AX  0  0  16
  [15] .fini  	 	 PROGBITS 080484b4 0004b4 000014 00  AX  0  0  4
  [16] .rodata 		 PROGBITS 080484c8 0004c8 000014 00  A   0  0  4
  [17] .eh_frame_hdr 	 PROGBITS 080484dc 0004dc 000034 00  A   0  0  4
  [18] .eh_frame 	 PROGBITS 08048510 000510 0000ec 00  A   0  0  4
  [19] .init_array     INIT_ARRAY 08049f08 000f08 000004 00  WA  0  0  4
  [20] .fini_array     FINI_ARRAY 08049f0c 000f0c 000004 00  WA  0  0  4
  [21] .jcr 		 PROGBITS 08049f10 000f10 000004 00  WA  0  0  4
  [22] .dynamic 	 DYNAMIC  08049f14 000f14 0000e8 08  WA  6  0  4
  [23] .got 		 PROGBITS 08049ffc 000ffc 000004 04  WA  0  0  4
  [24] .got.plt 	 PROGBITS 0804a000 001000 000014 04  WA  0  0  4
  [25] .data 	 	 PROGBITS 0804a014 001014 000008 00  WA  0  0  4
  [26] .bss 		 NOBITS   0804a01c 00101c 000004 00  WA  0  0  1
  [27] .comment 	 PROGBITS 00000000 00101c 000034 01  MS  0  0  1
  [28] .shstrtab 	 STRTAB   00000000 0016f8 00010a 00      0  0  1
  [29] .symtab 	 	 SYMTAB   00000000 001050 000470 10      30 48 4
  [30] .strtab 		 STRTAB   00000000 0014c0 000238 00      0  0  1
Key to Flags:
W (write), A (alloc), X (execute), M (merge), S (strings)
I (info), L (link order), G (group), T (TLS), E (exclude), x (unknown)
the anatomy of a program 149
O (extra OS processing required) o (OS specific), p (processor specific)
```
Every section has an address. In reality, _gcc_ does not combine the objects by itself, it invokes the linker _ld_. This linker uses the default script that it can find in the system to build the executable binary. In the default script, a segment is assigned a starting address 0x8048000 and sections belong to it. Then:
* 1st section address = starting segment address + section offset
  0x8048000 + 0x154 = 0x08048154
* 2nd section address = starting section address + section offset
  0x8048000 + 0x168 = 0x8048168
And so on until the last loadable section.

We can see this by listing all the segments :
```
$ readelf -l hello
```
```
Elf file type is EXEC (Executable file)
Entry point 0x8048310
There are 9 program headers, starting at offset 52
Program Headers:
   Type 	Offset   VirtAddr   PhysAddr   FileSiz MemSiz  Flg  Align
   PHDR 	0x000034 0x08048034 0x08048034 0x00120 0x00120 R E  0x4
   INTERP 	0x000154 0x08048154 0x08048154 0x00013 0x00013 R    0x1
	[Requesting program interpreter: /lib/ld-linux.so.2]
   LOAD 	0x000000 0x08048000 0x08048000 0x005fc 0x005fc R E  0x1000
   LOAD 	0x000f08 0x08049f08 0x08049f08 0x00114 0x00118 RW   0x1000
   DYNAMIC 	0x000f14 0x08049f14 0x08049f14 0x000e8 0x000e8 RW   0x4
   NOTE 	0x000168 0x08048168 0x08048168 0x00044 0x00044 R    0x4
   GNU_EH_FRAME 0x0004dc 0x080484dc 0x080484dc 0x00034 0x00034 R    0x4
   GNU_STACK  	0x000000 0x00000000 0x00000000 0x00000 0x00000 RW   0x10
   GNU_RELRO  	0x000f08 0x08049f08 0x08049f08 0x000f8 0x000f8 R    0x1
Section to Segment mapping:
  Segment Sections...
    00
    01 .interp
    02 .interp .note.ABI-tag .note.gnu.build-id .gnu.hash .dynsym .dynstr
.gnu.version .gnu.version_r .rel.dyn .rel.plt .init .plt .plt.got .text .fini
.rodata .eh_frame_hdr .eh_frame
    03 .init_array .fini_array .jcr .dynamic .got .got.plt .data .bss
    04 .dynamic
    05 .note.ABI-tag .note.gnu.build-id
    06 .eh_frame_hdr
    07
    08 .init_array .fini_array .jcr .dynamic .got
```

For example, _LOAD_ segment starts at 0x8048000 and ends at 0x8048000 + 0x005fc = 0x080485fc

The last section in the first _LOAD_ segment is _.eh_frame_. It starts at 0x0804851 because the start address is 0x08048000, the offset into the file ix 0x510. The end address should be 0x08048000 + 0x510 + 0xec = 0x80485fc because the segment size s 0xec. This is exactly the same s the end address of the first _LOAD_ segment above : 0x08048000 + 0x5ec = 0x80485fc.
