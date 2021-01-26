

## From Hardware to Software : Layers of Abstraction
&nbsp;

This chapter tries to explain the connection between software and hardware, and how software is represented physically.

&nbsp;
### 2.1 The physical implementation of a _bit_

When we write software, we manipulate the electrical circuit at the physical level. 

A very simple example of an electric current is a light bulb. It has 2 states : 1 is on, 0 is off. The problem with this system is that it needs human intervention to switch between states. To overcome this, we invented a device called ___transistor___  that controled the state based on voltage. This invention started the whole computer industry.

At the core, a transistor is just a resistor whose values can vary based on an input current's voltage. With this property, a transistor can switch electrical signal on and off (block or not block an electric flow), and also amplify it. At 0v, no current passes through the transistor because the resistor value is enough to block the electrical flow. At +3.5v, current flows through the transistorbecause the resistor value is lessened.

A ___bit___ has two states: 0 and 1. It is the building block of all electrical systems and software. Bits can change states much like a light bulb, 0 representa 0v (no electric flow) and 1 is +3.5v to +5v (electric flow). Transistors implement a bit corectly, as it can regulate the flow based on voltage. 

&nbsp;
#### 2.1.1 MOSFET transistor

The invention of the classical transistor opened oportunities for new electrical devices. Before it, vaccum tubes (fancier light bulbs) were used to represent the 0 and 1, and required humans to switch states. 

___MOSFET___, or ___M___ etal- ___O___ xide- ___S___ emiconductor ___F___ ield- ___E___ ffect ___T___ ransistor, invented in 1959 is an improved original transistor. It is better then the original because of it's shorter switch time between states, more stable, consumes less power and easier to produce. 


There are two types of _MOSFET_ transistors, analogous to the original two types of transistor: _n-MOSFET_ and _p-MOSFET_. They are calles ___NMOS___ and ___PMOS___ for short. 

&nbsp;


### 2.2 Digital Logic Gates

A _Logic Gate_ is a device that implements a boolian function. All digital devices make use of them. Each type of logic gate takes in a number of inputs and returns an output. 

&nbsp;
#### 2.2.1 The theory behind the logic gates

Logic gates only accept binary inputs and return binary outputs. At the time of creation, a branch of mathematics already dealt with binary values, called _Boolean Algebra_. They are created with a sound mathematical foundation. A set of boolian functions is _functionally complete_ if this set can construct all other Boolean functions. Later, it was proven that you can create all other functions with only _NOR_ or _NAND_ gates alone. Thus, _NOR_ and _NAND_ are _functionally complete_. 

This is why the invention of transistors, than CMOS circuit revilutionized the computer industry. 


&nbsp;
#### 2.2.2 Logic Gate implementation : CMOS cirtuit

Underlying every logic gate is a circuit calles ___CMOS___ - ___C___ complementary ___MOS___ FET. CMOS consists of two complementary transistors, _NMOS_ and _PMOS_. The simplest CMOS circuit is the NOT gate :

TODO: Put photo of NOT gate here.


From NOT, NAND can be created :


TODO: Photo of NAND


From NAND gate we have all other gates. Those simple circuits compute the logical operators in normal programming languages. For example `NOT` is executed as a NOT gate, `&` or `&&` is executed as a AND gate, and so on.

When writing software, we manipulate the logic gates. 

One interesting property of CMOS is that a ___k-input gate uses k PMOS and k NMOS transistors___. All logic gates are created using NMOS and PMOS transistors, and form the base of all electronics. Thansks to this pattern, It is possible to separate the physical circuit implementation and logic implementation. Digital designs are done by designing with logic gates, then later be _compiled_ into physical circuits. Later, we will see how logic gates become a language that describes how circuits operate. 

Finally, an implemented circuit with all parts is stored in a physical package called a ___chip___.  A chip is a substrate that an integrated circuit is etched onto. However, a chip also refers to a completely packaged integrated circuit in consumer market. Depends on the context. 

&nbsp;
#### Example :
74HC00 is a chip with four 2-input NAND gates. The chi[ comes with 8 input pins and 4 output pins, 1 pin for conecting to a voltage source and 1 pin for connection to the ground. The gates inside can be combined. Each combination allows a different logic function be implemented, efectively creating other gates.
Many of those NAND-gate chips combined can build a simple computer. Software at the physical level is just electrical flow.

TODO: Photo of 74HC00
TODO: Photo of logic diagram 74HC00
TODO: Photo of combinations of NAND gates.



&nbsp;
### 2.3 Beyond Logic Gates: Machine Language

Because logic gates only accept 1's and 0's, all hardware only understands 1 and 0. _Machine Language_ is a collection of unique bit patterns that a device can identify and perform a corresponding action. A _Machine Instruction_ is a unique bit pattern that a device can identify. In a computer system, a device that uses this language is called a ___CPU___m ___C___ entral ___P___ rocessing ___U___ nit, which controls all activitiws going inside a computer. For example, in the x86 architecture, the pattern `10100000` is telling the CPU to add two numbers. `000000101` tells the CPU to hald the computer. In the early days of computing, people had to write completely in binary. 

How do the binary commands actually do an action? Underlying the CPU is a circuit that implements those instructions. Similar to how a function is called in a normal programming language, a bit pattern is a name of a little function inside the CPU. Note that the CPU is not the only device with it's own language. A device with it's own machine language is a _programmable device_, since a user can use the language to command the device to perform different actions. For example, a printer has it's own set of commands instructing it how to print a page. 

&nbsp;
#### Example 2.3.1
A user can use a 74HC00 chip without knowing the internal structure, but only using the interface. First, we need to know the layout :

TODO: Put image of layout here. If you see this, it is kind of embarising.

Then, the functionality for each pin :

```
Symbol	  |   Pin	| Description
-------------------------------------
1A to 4A  | 1, 4, 9, 12 | data input
1B to 4B  | 2, 5, 10, 13| data input
1Y to 4Y  | 3, 6, 8, 11 | data output
GND	  | 7		| ground (0V)
Vcc	  | 14		| supply voltage
```

Finally, how to use the pins :

```
Input		|  Output
---------------------------
nA	| nB	| nY
L	| L	| H
L	| X	| H
X	| L	| H
H	| H	| L
```

`n` is a number : 1, 2, 3, 4
`H` - High Voltage
`L` - Low Voltage
`X` - Don't Care


A user does not need to know the implementation, only the usage described by the earlier tables and the truth table. We can say that the truth table above is the machine language for the device. 

The device has 8 input pins. This means that it accepts binary stings of 8 bits. At the same time, it has 4 output pins, which means it produces binary strings of 4 bits from the 8 bit input. 

Even though this device is simpe, the language can accept a big number of binary strings : 2^8 + 2^4 = 272. This is a small number compares to a device like a CPU, with hundreds of pins. 

Example of input and output :
```
	|		  Input	  	        |      Output
Pin	| 1A | 1B | 2A | 2B | 3A | 3B | 4A | 4B | 1Y | 2Y | 3Y | 4Y
Value	| 1  | 1  | 0  | 1  | 1  | 0  | 0  | 0  | 0  | 1  | 0  | 1 
```

On the other hand, if an OR gate is implemented, we can only build a 2 input OR gate from a 74HC00, as it requires 3 NAND gates : 2 input NAND and 1 output NAND. Each input NAND represents only a 1 bit value. 

TODO: Image of OR gate from NAND
TODO: Image of implementation and notation on chip, page 20


To implement a 4-bit OR gate, we need 4 74HC00 chips.

TODO: Image of 4 chips.



&nbsp;
#### 2.3.2 Assembly Language

The Assembly language is a symbolic representation of binary machine code, giving bit patterns better names. For example, insteaf of writing `000000101`, programmers only needed to write `hlt`. Such an abstraction makes usage of a CPU much easier and more memorable. As a result, code was written faster. 

Understanding Assembly is paramount for low level programming, eve today. The more instructions a programmer wants to know, the deeper understanding he needs to have of hardware architecture. 

&nbsp;
#### Example 2.3.2 :

We can build a device with 2 assembly instructons :
```
or   <op1>, <op2>
nand <op1>, <op2>
```

`or` accepts two 4-bit operands. This coresponds to a 4-input OR gate device built from 4 74HC00 chips.
`nand` accepts teo 4-bit operands. This coresponds to a single 74HC-- chip.


Esentially, the gates from example 2.3.1 implement those instructions.


Up to now we have manually fed electric signals into the pins. To automate this process means to simply write the assembly code and the device that implements it is selected corectly. To solve this, we must give each instructuon an index in binary code, called the _operation code_ or _opcode_, and embed it as part of input. This code is ilistrated in the table :

```
Instruction  |  Binary code
    nand     |      00
     or      |      01
```


Each input now contains aditional data at the beginning : an opcode :

```
nand 1100, 1100
```

Coresponding to the binary string `0011001100` The first two bits, `00` encodes a `nand` instruction.

Also, we need to add another decive to select a device, based on a binary code peculiar to an instruction. Such a device is called a ___decoder___, an important component in a CPU that decides which circuit to use. If the decoder was fed the above example `0011001100`, because the code starts with `00` data would be sent to a NAND device. 


When we write code in assembly and save it is a text file, a program called an ___assembler___ translated the text file into binary strings. How does an assembler exist in the first place, how is it written? Initialy, it was written in binary strings. In the next generation, it is written in the initial assembly code, then use the first version to compile it. Those binary strings are stored in another device called a _storage device_, an array of circuits to store 0's and 1's.

A decoder is build out of logic gates. However, a storage device can be anything that can store 0's and 1's. It can be a magnetized device using magnetizm to store info, or an electric circuit device that can change and remember data if it has voltage. Modern devices are so complex that it is impossible and unnecesary to understand every implementation detail. What we need to learn is the implementation. 



TODO: Insert picture of basic computer.



A computer esentially implements this process :

* Fetch an instruction from the storage device.
* Decode the instuction.
* Execute the instruction.

The above device is rudementary, but it can run this cycle. It can be expanded by adding more devices and adding opcode for them, then update the decoder. 


&nbsp;
#### 2.3.3  Programming Languages


Assembly is only one layer of abstraction up from basic machine code, made from 0's and 1's. Over time, people started to see repeting patterns of usage in assembly and decided to increse abstraction. In C, instead of writing code to chech if one variable is greater then another and if so, executing a block of code, you can use an `if` statement. 

TODO: Picture of abstraction.


People abstracter common patterns of assembly code, then wrote a program to translate it acordingly.  The program that translates text forms to machine code is called a ___compiler___. 

Any software logic that a programming language can implement, hardware can too. The reverse is also true, everything possible in hardware can be implemented in software. In reality, even if all programming languages have this relation, not all of them are able to express the same programs. They are plotted on a scale, from _high level_ to _low level_.

The higher a programming language is, the more layers of abstraction between it and hardware and in turn, closer to human thinking. Python, for example, cannot manipulate underlying hardware, despite being able to deliver the same computation as low level languages. This is intentional, the reason being to shield the programmer from low level implementation details not relevant to the current problem domain. Such convenience is not without loss, it requires software to carry an extra code for managing hardware details, making the code run slower. 

The more abstraction a programming language imposes, the harder it is to manipulate hardware. This is the reason why C is usually used for writing an operating system , since it is very close to the underlying hardware. 

A high level programming language lets you focus on the problem domain, while a low level one lets you dictate hardware implementation.


&nbsp;
### 2.4 Abstraction 

There is a need for abstraction in order to make creating software easier, leaving the irrelevent details hidden. 

&nbsp;
#### 2.4.1 Why abstraction works

Abstraction is fundamental :

* Logic gates abstract away the details of CMOS
* Machine language abstracts he details of Logic gates
* Assembly abstracts away the details of Magine languages
* Programming languages abstract away the details of Assembly

This is done like this :

* See a reccuring pattern.
* Build a language that implements the pattern.
* Strip away details from the pattern implementation in old language.
* Give a new and simpler names.

A new layer is just a more convenient way to describe the old one. 
Reaccuring patterns are why abstraction works. 

In order to study patterns, we needed to develop a systematic method : Mathematics. 

> A mathematicia, like a painter or a poet, is a maker of patterns. If his patterns are more permanent than theirs, it is because they are made with ideas. (G. H. Hardy, 2005)

A mathematical formula represents a pattern. 

For this reason, it can describe reaccuring patterns in nature. It can also be applied in the digital world, witch is a subset of the real one. 

Mathematics can be used to help translatoin between layers easier. It is a universal language for all layers. 

&nbsp;
#### 2.4.2 Why abstraction reduces complexity. 

Abstraction leverages productivity by striping away irrelevant details. 
A programming language is expressive if its syntax is designes to express th eproblem domain it is trying to solde. The _what_ it will do rather then _how_.





