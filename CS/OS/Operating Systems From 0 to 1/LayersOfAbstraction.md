

## From Hardware to Software : Layers of Abstraction


This chapter tries to explain the connection between software and hardware, and how software is represented physically.


### 2.1 The physical implementation of a bit

When we write software, we manipulate the electrical circuit at the physical level. 

A very simple example of an electric current is a light bulb. It has 2 states : 1 is on, 0 is off. The problem with this system is that it needs human intervention to switch between states. To overcome this, we invented a device called ___transistor___  that controled the state based on voltage.

This invention started the whole computer industry.

At the core, a transistor is just a resistor whose values can vary based on an input current's voltage. With this property, a transistor can switch electrical signal on and off (block or not block an electric flow), and also amplify it.

At 0v, no current passes through the transistor because the resistor value is enough to block the electrical flow. At +3.5v, current flows through the transistorbecause the resistor value is lessened.

A ___bit___ has two states: 0 and 1. It is the building block of all electrical systems and software. Bits can change states much like a light bulb, 0 representa 0v (no electric flow) and 1 is +3.5v to +5v (electric flow). Transistors implement a bit corectly, as it can regulate the flow based on voltage. 


#### 2.1.1 MOSFET transistor

The invention of the classical transistor opened oportunities for new electrical devices. Before it, vaccum tubes (fancier light bulbs) were used to represent the 0 and 1, and required humans to switch states. 

___MOSFET___, or ___M___ etal- ___O___ xide- ___S___ emiconductor ___F___ ield- ___E___ ffect ___T___ ransistor, invented in 1959 is an improved original transistor. It is better then the original because of it's shorter switch time between states, more stable, consumes less power and easier to produce. 


There are two types of _MOSFET_ transistors, analogous to the original two types of transistor: _n-MOSFET_ and _p-MOSFET_. They are calles ___NMOS___ and ___PMOS___ for short. 




### 2.2 Digital Logic Gates

A _Logic Gate_ is a device that implements a boolian function. All digital devices make use of them. Each type of logic gate takes in a number of inputs and returns an output. 

#### 2.2.1 The theory behind the logic gates

Logic gates only accept binary inputs and return binary outputs. At the time of creation, a branch of mathematics already dealt with binary values, called _Boolean Algebra_. They are created with a sound mathematical foundation. A set of boolian functions is _functionally complete_ if this set can construct all other Boolean functions. Later, it was proven that you can create all other functions with only _NOR_ or _NAND_ gates alone. Thus, _NOR_ and _NAND_ are _functionally complete_. 

This is why the invention of transistors, than CMOS circuit revilutionized the computer industry. 



#### 2.2.2 Logic Gate implementation : CMOS cirtuit

Underlying every logic gate is a circuit calles ___CMOS___ - ___C___ complementary ___MOS___ FET. CMOS consists of two complementary transistors, _NMOS_ and _PMOS_. The simplest CMOS circuit is the NOT gate :

TODO: Put photo of NOT gate here.


From NOT, NAND can be created :


TODO: Photo of NAND


From NAND gate we have all other gates. Those simple circuits compute the logical operators in normal programming languages. For example `NOT` is executed as a NOT gate, `&` or `&&` is executed as a AND gate, and so on.

When writing software, we manipulate the logic gates. 

One interesting property of CMOS is that a ___k-input gate uses k PMOS and k NMOS transistors___. All logic gates are created using NMOS and PMOS transistors, and form the base of all electronics. Thansks to this pattern, It is possible to separate the physical circuit implementation and logic implementation. Digital designs are done by designing with logic gates, then later be _compiled_ into physical circuits. Later, we will see how logic gates become a language that describes how circuits operate. 

Finally, an implemented circuit with all parts is stored in a physical package called a ___chip___.  A chip is a substrate that an integrated circuit is etched onto. However, a chip also refers to a completely packaged integrated circuit in consumer market. Depends on the context. 


#### Example :
74HC00 is a chip with four 2-input NAND gates. The chi[ comes with 8 input pins and 4 output pins, 1 pin for conecting to a voltage source and 1 pin for connection to the ground. The gates inside can be combined. Each combination allows a different logic function be implemented, efectively creating other gates.
Many of those NAND-gate chips combined can build a simple computer. Software at the physical level is just electrical flow.

TODO: Photo of 74HC00
TODO: Photo of logic diagram 74HC00
TODO: Photo of combinations of NAND gates.




### 2.3 Beyond Logic Gates: Machine Language

Because logic gates only accept 1's and 0's, all hardware only understands 1 and 0. _Machine Language_ is a collection of unique bit patterns that a device can identify and perform a corresponding action. A _Machine Instruction_ is a unique bit pattern that a device can identify. In a computer system, a device that uses this language is called a ___CPU___m ___C___ entral ___P___ rocessing ___U___ nit, which controls all activitiws going inside a computer. For example, in the x86 architecture, the pattern `10100000` is telling the CPU to add two numbers. `000000101` tells the CPU to hald the computer. In the early days of computing, people had to write completely in binary. 

How do the binary commands actually do an action? Underlying the CPU is a circuit that implements those instructions. Similar to how a function is called in a normal programming language, a bit pattern is a name of a little function inside the CPU. Note that the CPU is not the only device with it's own language. A device with it's own machine language is a _programmable device_, since a user can use the language to command the device to perform different actions. For example, a printer has it's own set of commands instructing it how to print a page. 


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




#### 2.3.2 Assembly Language



