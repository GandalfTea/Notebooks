__TOC__

* 3.1 [What is a computer?](#3.1)
* 3.2 [Computer Architecture](#3.2)
	* 3.2.1 [Instruction Set Architecture](#3.2.1)
	* 3.2.2 [Computer Organization](#3.2.2)
	* 3.2.3 [Hardware](#3.2.3)
* 3.3 [x86 Architecture](#3.3)
* 3.4 [Intel Q35 Chipset](#3.4)
* 3.5 [x86 Execution Environment](#3.5)

&nbsp;

## Computer Architecture

In order to do low level programming, we must first understand the architecture of a computer. 

&nbsp;

### 3.1 What is a computer?  <a name="3.1"></a>


A computer is a hardware device that consists of at least a CPU, a memory device and input/ouput interfaces. There are two big groups 

  * ___Single-purpose computer___ is a computer build from the hardware level to operate on a specific task. 

  * ___General-purpose computer___ is a computer that can accept software designed to emulate various single-purpose computers.

&nbsp;

Examples :

__Server__ : A server is a single-purpose computer with big resources to allow many personal computers to connect to it. 


__Desktop Computer__ : It is a general-purpose computer designed with moderate resources for regular use. 


__Mobile Computer__ : It is a general putpose computer with limited resources, designed to be small and to be carried around

__Game Consoles__ : They are general purpose computer optimised for gaming. They use custom CPU and GPUs but are similar to desktop. 

&nbsp;

__Embedded Computer__  
It is a single-board or single-chip computer with limited resources, designed to be integrated into bigger hardware devices. 

One type of embedded compters is a _microcontroller_, used for controlling other hardware devices. They are general-purpose but are typically sused to perform a single task. 

Another type is a _system-on-chip_. It is more complex and has more resources then a microcontroller. It is a general-purpose that is comparable to a desktop. They are typically used in smartphones, like the Apple A% SoC used in Ipad2 and iPhone 4S, or Qualcomm Snapdragon used in Android. 


All of those are introduces in an environment called ___PCB___, ___P___ rinted ___C___ ircuit ___B___ oards. This is a physical board with connections between electronic components. It is used to combine those components into larger devices, thus why _embedded_. They are to be found everywhere, from toys to power tools, office machines, etc. 
  
<img src="https://i.imgur.com/q48hE62.png" alt="img" style="zoom: 10%;" />
  


The line between a microcontroler and a system-on-chip is blurry. 


&nbsp;

__Field Programmable Gate Arrays__

Usually called FPGA, it is an array of reconfigurable logic gates. In a similar manner to the multi 74HC00 example, this contains thousands of chips called _logic blocks_. Those can be programed to implement high-level features, like algorithms. 
  
<img src="https://i.imgur.com/vCZx4qP.png" alt="img" style="zoom: 10%;" />
  

Digital hardware components are designed by using a language to describe how gates are wired togeteher. This language is called ___Hardware Description Language___. Later it is compiled into a description of connected electronics called a ___netlist___. 


The difference between FPGA and other embedded computers is that with an FPGA you work directly at the logic gates level, while most embedded computers function at the Assembly level. 

They are usually implemented in cases that ask for high-speed processing, like real-time medical image processing, cruise control systems, etc. 

&nbsp;

__Application-Specific Integrated Circuit__

Usually refered as _ASIC_, it is a chip designed for a particular purpose. It does not contain refigurable logic blocs, it has already resigned logic blocks that fulfull their purpose. 

They can be considered as the final stage of a FPGA prototype. They are very constly to manifacture and if errors are made, you must throw it away. 


&nbsp;

### 3.2 Computer Architecture  <a name="3.2"></a>


 
Computer Architecture = Instruction Set Architecture + Cmputer Organization.

At the highest level is the Instruction Set Architecture    
At the middle level is the Computer Organization    
At the lowest level is the Hardware.    


&nbsp;

#### 3.2.1 Instruction Set Architecture  <a name="3.2.1"></a>

It is a basic set of commands and instructions that a microprocessor can understand and carry out. 

It is usually refed to as a ___ISA___, and it is the design of the environment that implements an instruction set. Esentially, a run-time environment similar to those interpreters of high-level languages. They include all the instructions, registers, interrupts, memory models, addressing models, I/O, etc. of a CPU. The more features, the more circuits are required to implement it. 

&nbsp;

#### 3.2.2 Computer Organization  <a name="3.2.2"></a>

This is the functional view of viewing a computer. Hardare compinents are only boxes with input and output tha connect to each other. Two computers may have the same ISA but different organiation. For example, both Intel and AMD implement the x86 ISA, but the hardware components are not the same. 

Organization may depend on the manifacutrer's design, but they are all originated from the Von Neumann architecture :

    
<img src="https://i.imgur.com/SaydgMO.png" alt="img" style="zoom: 10%;" />
   

_CPU_ fetches instructions continuously from main memory and execute.
_Memory_ stores program data and code.
_Bus_ are electrical wires for sending raw bits between components. 
_I/O Devices_  take input and return output, for example mouse inputs info, monitor takes information from CPU to display it, LED turns on/off according to patterns computed by CPU.

The Von Neumann operates by storing instructions in main memory and CPU fetches those instructions into it's internal storage for execution, one after another. Data is transfered through bus between CPU, IO and memory, and where to store in the device is transferred through the address bus by CPU.  This architecture implements the fetch-decode-execute cycle. 

Today, a computer has more buses, each is specialized in a type of traffic. At the core, they are all stil Von Neumann architecture. 

&nbsp;

The __CPU__ is the essential part of any computer. To understand a CPU is esential for writing an OS :

* To control all the other parts, the programmer must go through the CPU, as it is the only programmable by code. 

* Many OS concepts are implemented directly in hardware in the CPU : task switching, paging. A kernal programmer needs to know how to use the hardware features, to avoid duplication.

* CPU built-in OS features boost performance, and developers are free to implement such features.

* To use the CPU, the developer needs to understand the documentation provided by the manifacturer. 

* After understanding one architecture well, all other are easier to learn. 


A CPU is an implementation of an ISA. You can control it with Assembly language. The reason you can control every other part through it is that it is comunicating with other devices through these two interfaces :

&nbsp;

_Registers_ are hardware components for high speed data access and comunication to other hardware devices. They allow software to control hardware directly by writing to registers of a device, or recieve information from hardware by reading from registers of  device.

Not all are used to transmisit data, the registers in the CPU are used as storage for temporary data. Other devices always have a set of registers for communicating with the CPU. 


_Port_ is a special register in a hardware device specialized for communication with other devices. When data is written to a port, It causes a hardware device o perform some operating using the values on the port. The difference between a port and a register is that ports do not store data, but delegate data to some other circuit. 

Those two interfaces are the only ones for controlling hardware with softeare. Writing drivers is esentially learning the functionality of each register and how to use them properly to control the device. 

&nbsp;

___Memory___ is a storage device that stores information. It consists of many cells that contain a bite of data and it's address number, so a CPU can access exact locations. Memory in a Von Neumann machine does not distinguish between witch bites are data and witch are instructons. It is up to the software to decide. To a CPU there is no distinction between the two, there is only data, and it can execute all that is fed, with undesired results. 


The ___RAM___ is controlled by a device called a _memory controller_. Curently, most processors have this device embedded, so the CPU has a dedicated bus connecting to the RAM. 
In older computers, this device was located in a chip known as _MCH_, _Memory Controller Hub_. In this case, there is no direct connection between CPU and RAM, but to a MCH chip. The newer option provides better performance. 

   
<img src="https://i.imgur.com/JKPAa9n.png" alt="img" style="zoom: 10%;" />
   


The physical implementation of RAM is a grid of cells that each contain a transistor and a _capacitor_, witch stores charge for short periods of time. Thee transistor controls access to thee capacitor. The charge on the capacitor slowly dissipates, requiring the inclusion of a refresh circuit to periodically read values from the cells and write them back after amplification from an external power source. 

_Bus_ transmits data between components. Phycally they are electrical wires. The total number of wires is called _bus width_ and is dependent on how much the CPU can support. If a CPU only accepts 16 bits at a time, then the bus has 16 wires connecting from components to the CPU. 

&nbsp;
### 3.2.3 Hardware  <a name="3.2.3"></a>


Hardware is formed of different ways of implementing the same instruction set architecture. For example, i7 provides more power and consumes more energy, while the laptop version consumes less and gives less power. We usually do not need to understand the implementation if documents are available. Instructions and computer organizaation is more relevant to an operating system.


&nbsp;
### 3.3 x86 Architecture  <a name="3.3"></a>


A _chipset_ is a chip with multiple functions. Historically, a chipset is actually a set of individual chips all responsible for a different function. As hardware progressed, the set of ships became single with more space, energy, and more cost efficient. In a general computer, components are connected through a PCB called a _Motherboard_. Each CPU needs a compatible motherboard that can host it. A motherboard is defined by its chipsset model that determine the environment that a CPU can control. The environment consists of :

* A slot or more for CPU.
* A chipset of two chips witch are the Northbridge and Southbridge chips:
  	
	* _Northbridge_ chip is responsible for the high-performance communication between CPU, main memory and the GPU.
	* _Southbridge_ chip is responsible for the communication with IO devices and other devices that are not performance sensitive. 

* Slots for memory sticks.
* Slot for GPU.
* Generic slots for other devices : network card, sound card, etc.
* Ports for IO devices. 

To write an OS, a programmer needs to understand how to program these devices. After all, the OS removes the need for programs to manage hardware. Learning to program the CPU is the most important. For that reason, this book will focus on how to program a x86 CPU. The reason is that all computers have a CPU, while not all might have the other pieces. 

Learning how to program an x86 CPU is a daunting task. 

   
<img src="https://i.imgur.com/Tu78QGz.png" alt="img" style="zoom: 10%;" />
   

&nbsp;

### 3.4 Intel Q35 Chipset  <a name="3.4"></a>

Released in 2007, it is used as an example of a high-level computer organization. Later, we will use QEMU to emulate a Q35 system. With this chipset, the CPU is also relatively up to date  so that we can use the latest? manuals from Intel. 


&nbsp;

### 3.5 x86 Execution Environment  <a name="3.5"></a>

An execution environment is an environment that provides the facility to make code executable. It needs to address the following questions:

__Supported operations?__  
data transfers, arithmetic, control, floatin-point, etc.    

__Where are the operands stored?__   
registers, memory, stack, accumulator    

__How many explicit operands are there for each instruction?___    
0,1,2, or 3.     

__How is the operand location specified?__     
register, immediate, indirect     

__What type and size of operands are supported?__    
byte, int, float, double, sting, vector, etc.   


For the remainder of this chapter, please read to chapter 3 in Intel Manual Volume 1, _Basic Execution Environment_.



