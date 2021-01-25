`#	https://en.wikipedia.org/wiki/Field-programmable_gate_array`



[toc]



### **Field-programmable gate array**



#### What are they?

A **field-programmable gate array** (***FPGA***) is an [integrated circuit](https://en.wikipedia.org/wiki/Integrated_circuit) designed to be configured by a customer or a designer after manufacturing – hence the term "[field-programmable](https://en.wikipedia.org/wiki/Field-programmability)". The FPGA configuration is generally specified using a [hardware description language](https://en.wikipedia.org/wiki/Hardware_description_language) (HDL),

FPGAs contain an array of [programmable](https://en.wikipedia.org/wiki/Programmable_logic_device) [logic blocks](https://en.wikipedia.org/wiki/Logic_block), and a hierarchy of "reconfigurable interconnects" that allow the blocks to be "wired together", like many logic gates that can be inter-wired  in different configurations. [Logic blocks](https://en.wikipedia.org/wiki/Logic_block) can be configured to perform complex [combinational functions](https://en.wikipedia.org/wiki/Combinational_logic), or merely simple [logic gates](https://en.wikipedia.org/wiki/Logic_gate) like [AND](https://en.wikipedia.org/wiki/AND_gate) and [XOR](https://en.wikipedia.org/wiki/XOR_gate). In most FPGAs, logic blocks also include [memory elements](https://en.wikipedia.org/wiki/Memory_cell_(computing)), which may be simple [flip-flops](https://en.wikipedia.org/wiki/Flip-flop_(electronics)) or more complete blocks of memory allowing flexible [reconfigurable computing](https://en.wikipedia.org/wiki/Reconfigurable_computing) as performed in [computer software](https://en.wikipedia.org/wiki/Software). FPGAs have a remarkable role in the embedded system development due to capability to:

- ​    start system software (SW) development simultaneously with hardware (HW).
- ​    enable system performance simulations at very early phase of the development.
- ​    allow various system partitioning (SW and HW) trials and iterations before final freezing of the system architecture



#### How are they used?

//







### **Architecture**



**FPGA: >  Logic Block (*CLBs*) >  Logic Cells >  Logic Gates >  Transistors;**

​	    	**>  Input/Output Blocks (*IOBs*) >**

 	       **>  Switch Matrix (red lines) >**



<img src="https://www.allaboutcircuits.com/uploads/articles/Figure1_LUT.jpg" alt="img" style="zoom: 80%;" />



#### ***Logic Gates*** Architecture :

Boolean functions may be practically implemented by using electronic gates. The following points are important to understand.

- Electronic gates require a power supply. 

- Gate **INPUTS** are driven by voltages having two nominal values, e.g. 0V and 5V representing logic 0 and logic 1 respectively.       

- The **OUTPUT** of a gate provides two nominal values of voltage only, e.g. 0V and 5V representing logic 0 and logic 1 respectively. In general, there is only one output to a logic gate except in some special cases.

  

  Logic gates from transistors: https://www.youtube.com/watch?v=sTu3LwpF6XI

  

  

  [![What is a Logic Gate - A Beginner's Guide](data:image/jpeg;base64,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Kpy6TRkKWqbXUoZl6tHej0kbHy8jA4/yamTncRoa1awtmRZxO3o6yyr0mnGVtjQDD3l9dec7W7hRtiAPiTqVTL+jfhNtrWvQxd9dRFto3oaA7N5AAAD01PNfow4NvvjdXye21h94LagZ45z1Ur+y8OHtea3ZUpO0rP519g7Io/XPjgfIOP4LniSV5WVe4tyXsXY6/Ra9/VRdkASz8L4VjYqeHjUpUnwqUKPv15zcgedFKVqERQqqAFCjQAHkAB5CekRAREQEREBERAREQEREBERAREQEREBI3i/HMXEapcmwIbrBXVsNoufIEgaX7ToSSlS51wEycjCotG0sfKVteY3jWaIPoQdEH4iBPNxfHFltTWBWpqS27q2FStuvpYse2vyb+vbXeaWHzVh22LWrWA2Eio203VpaQN6qssQLYdAkdJOx3E57UuVeeKU2I7304nD6rQF73im29mKD18SvR18W1LLx3jeLxBKMfBsFt3teI5WsHdC1WrZY1w/otBGXR0STqBLrznhmzwguV4nSG6Tg5gbpJ11EeF2XfbflLFKyo/5db/y5f/vaWaAiIgIiICIiBp8U4lTjILL26ULonVokBnPSvVoe6NkDZ7Dc87eNYyPaj2BTTWtlxbYREbeupz7oOlJ1vetH1E1Oc8mpMKxba/F8UeClX9bZZ7qp8vjv0AJ9JRUwLqeH5ODaDbmU34uTeygs2VQLamLgdy3u1NX0/wDV69YF7wOaMS6xalaxXffhePTdULdDZ8JrEUWdu/uk9u8815vwTb4RewHxjR1vRetPjBinR4xTw+rqGh73cyH4txrG4hZi04FotsXMotsNXfwa6yWs8X+rLAFAD3JY9ux1AvTeKnttcNgLxm431InS4Ayj02G3Z2iv0sygDsD37d5o6pECJQiIgIiIGJF28wYqLkM9mvZv+cAqwK7XqUga24I8iu9nYGz2kpOa83J4uecyqvqx8E0jOA3+X6XFgAA+scf+U16ltfGBdsbmDFsyjiLZ/tAqW01srK3htrR94Dv3Gx5jfefB5lwxQMjxPybWPWnuP1vYrMrJXWB1u20bsAd62O0qnEMVrc3Ky8X3rqBhX0dP9InhP4lfzDoSPt6T6SO5bzErx+H51oYYou4l1synVRttsNdln5g0GXq9Or5wL7icyYtq2lWZTSnXal1VtdiponqNdih+k6Ojrvoz54TzNiZLiuprA7J4iLfTdSXTttq/FVfEA2Nld62PjKzxLMrzci/JxG8SirheVXZanet3cgoiN5OV6GJ15dQ+M+OXarq8vAObYLFbBK4bVp4a1v0IbUsHU3WzIF0d/wAxu0kDoUREoREQEREBERAREQEREBERAREQEREBMamYgY1GpmIGNTMRAREQEREBERATGpmIGNRqZiAiIgIiICIiAmNTMQMajUzEDGo1MxAREQEREBERAREQEREBERAREQEREBERAREQEREBERAREQEREBERAREQEREBERAREQEREBERAREQEREBERAREQEREBERAREQP//Z)604 × 229](https://www.google.com/url?sa=i&url=https%3A%2F%2Fwww.edrawmax.com%2Flogic-gate%2F&psig=AOvVaw2wj0Lz6yrR5Cwiz8KP_rPR&ust=1595317624176000&source=images&cd=vfe&ved=0CAIQjRxqFwoTCPj_qrOr2-oCFQAAAAAdAAAAABAE)



##### *AND* 

It gives a high output (1) only if all of it's inputs are high. 

| A    | B    | Output |
| ---- | ---- | ------ |
| 0    | 0    | 0      |
| 0    | 1    | 0      |
| 1    | 1    | 1      |

From transistors :  

![img](https://www.cs.bu.edu/~best/courses/modules/Transistors2Gates/images/nand-circuit-cropped.gif)![img](https://www.cs.bu.edu/~best/courses/modules/Transistors2Gates/images/not-circuit-cropped.gif)

In order to build an **AND** gate, all we have to do is to connect the output of the **NAND** gate to the input of a **NOT** gate. 



##### *NAND*

It is a NOT-AND gate, witch is an AND gate followed by NOT gate. The output is high only if any inputs are low. The symbol is an AND gate with a small circle on the output.

| A    | B    | Output |
| ---- | ---- | ------ |
| 0    | 0    | 1      |
| 1    | 0    | 1      |
| 0    | 1    | 1      |
| 1    | 1    | 0      |

<img src="https://www.cs.bu.edu/~best/courses/modules/Transistors2Gates/images/nand-circuit.gif" alt="img" style="zoom:50%;" />

This circuit has two inputs **X** and **Y** and one output **Z**. Thus for **X=0, Y=0** we get **Z=1**. In the case when both **X** and **Y** are 1, the bottom two transistors will be **ON** while the top two will be **OFF**, thus no electricity will flow from the battery to the output **Z**. Thus for **X=1, Y=1** we get **Z=0**. In the case when **X=0** and **Y=1**, we notice that one of the two top transistors will be **ON** and the other will be **OFF**. Similarly, for the bottom transistors, one will be **ON** and the other will be **OFF**. Thus, electricity will still be able to flow to **Z** (through the **ON** transistor on top), thus making **Z=1**. The case for **X=1, Y=0** is similar. It yields **Z=1**.  		



##### *OR*

It gives a high output if one or more inputs is high.

| A    | B    | Output |
| ---- | ---- | ------ |
| 0    | 0    | 0      |
| 0    | 1    | 1      |
| 1    | 0    | 1      |
| 1    | 1    | 1      |

![2-input transistor or gate](https://www.electronics-tutorials.ws/wp-content/uploads/2018/05/logic-log44.gif)



##### *NOR*

It is a NOT-OR gate, witch is an OR gate followed by a NOT. The output is low if one or more inputs is high.

| A    | B    | Output |
| ---- | ---- | ------ |
| 0    | 0    | 1      |
| 0    | 1    | 0      |
| 1    | 1    | 0      |

<img src="https://www.cs.bu.edu/~best/courses/modules/Transistors2Gates/images/nor-circuit.gif" alt="img" style="zoom:50%;" />





##### *NOT*

It reverts the input. It is also known as an *inverter* 

| Input | Output |
| ----- | ------ |
| 0     | 1      |
| 1     | 0      |

<img src="https://www.cs.bu.edu/~best/courses/modules/Transistors2Gates/images/not-circuit.gif" alt="img" style="zoom:50%;" />

If x = 0, the bottom transistor will be off. The top will be on and electricity will reach Z.

If x = 1, the bottom will be on and the top will be off, no electricity will reach Z.



##### *XOR / EXOR* (Exclusive OR gate)

It gives a high output if either but not both of the inputs are high. If both inputs are high, it gives a low.

| A    | B    | Output |
| ---- | ---- | ------ |
| 1    | 0    | 1      |
| 0    | 1    | 1      |
| 1    | 1    | 0      |

##### *XNOR / EXNOR*

It gives a low output if either but not both of the inputs are high. If both inputs are high, it gives a high.

| A    | B    | Output |
| ---- | ---- | ------ |
| 1    | 0    | 0      |
| 0    | 1    | 0      |
| 1    | 1    | 1      |





#### ***Logic Cell*** Architecture :

A typical cell consists of a 4-input [LUT](https://en.wikipedia.org/wiki/Lookup_table), a [Full adder](https://en.wikipedia.org/wiki/Full_adder) (FA) and a D-type [flip-flop](https://en.wikipedia.org/wiki/Flip-flop_(electronics)):



##### Lookup table (***LUT***) :

`# https://www.allaboutcircuits.com/technical-articles/purpose-and-internal-functionality-of-fpga-look-up-tables/`

An array indexing operation that stores values that do not need to be computed at runtime. Used for optimization. The table may be manually populated when the program is written, or the  program may populate the table with values as it calculates them. When  the values are needed later, the program can look them up, saving CPU resources.



Components:



###### Static random-access memory (***SRAM***) :

Stores one bit of info with a latch (Flip-Flops). It requires power to keep the bit in place (volatile memory).



###### Multiplexer (***MUX***) : 

`# https://www.electronics-tutorials.ws/combination/comb_2.html`

The multiplexer takes in multiple input lines and switches one of them into a single output line by the application of a *control signal*. They act like super fast [rotary switches](https://www.google.com/search?q=rotary+switches&client=firefox-b-d&source=lnms&tbm=isch&sa=X&ved=2ahUKEwiaucPN9d3qAhXxsosKHfRwAeUQ_AUoAXoECAwQAw&biw=1837&bih=950&dpr=0.9) connecting one input line at a time (called channel) to the output. They can be either digital (made from high-speed logic gates used to switch digital or binary data) or analogue (using transistors to switch the voltage of current inputs).

The *inputs* are controlled by the *control inputs*, that dictate witch line to output.



![2-to-1 multiplexer circuit](https://www.electronics-tutorials.ws/wp-content/uploads/2018/05/combination-multiplexer2.gif)

​																											2-channel MUX



![4-input multiplexer circuit](https://www.electronics-tutorials.ws/wp-content/uploads/2018/05/combination-multiplexer3.gif)



![multiplexer input selection](https://www.electronics-tutorials.ws/wp-content/uploads/2018/05/combination-comb1a.gif)

​																												4-channel MUX



![multiplexer using logic gates](https://www.electronics-tutorials.ws/wp-content/uploads/2018/05/combination-comb27.gif)

​																										4-channel MUX using Logic Gates.



![multiplexer symbol](https://www.electronics-tutorials.ws/wp-content/uploads/2018/05/combination-comb26.gif)

​																													The MUX symbol



###### How it works :



######  ![img](https://www.allaboutcircuits.com/uploads/articles/Figure2_LUT.jpg)

A specific example of a 2-input LUT comprising of 4 SRAM bits and a 4:1  mux is as shown in Figure 2a. Next, Figure 2b shows its equivalent  architecture but represents a 4:1 mux as a tree of 2:1 muxes. The number of inputs available for a LUT determine its size. In general, a LUT with *n* inputs is seen to comprise of 2*n* single-bit memory cells followed by a 2*n*:1 multiplexer or its equivalent (say, two 2*n*-1:1 muxes followed by one 2:1 mux).





##### Full Adder (***FA***) :

A digital circuit that performs addition on binary numbers.



A **full adder** adds **binary numbers** and accounts for values carried in as well as out.  A one-bit full-adder adds three one-bit numbers, often written as *A*, *B*, and *Ci* , ***A*** and ***B*** are the operands, and ***Ci*** (Carry input) is a bit carried in from the previous addition. The circuit produces a two-bit output, one bit is the ***S*** (result from addition of A and B) and ***Co*** (Carry output, witch becomes *Ci* in the next FA). Because of the *C* bit, they can be chained.

There are also **Half Adders** witch do not have the *C* bit, therefore they can't be chained.

[<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/5/57/Fulladder.gif/220px-Fulladder.gif" alt="img" style="zoom:150%;" />](https://en.wikipedia.org/wiki/File:Fulladder.gif)

Good video for Full Adder: https://www.youtube.com/watch?v=m0C3-JWWvcc





##### Flip-flops (***FF***) or *latch* : 

`# https://www.electronicshub.org/flip-flops/`

A circuit with 2 stable states (0,1) used to store one bit of information.

Flip – flops have two stable states and  hence they are *bistable multivibrators*



###### Multivibrators  :

`# https://www.edgefxkits.com/blog/multivibrators-types-and-thier-working/`

Produce an output electrical wave in the shape of a square. 



​	**Astable** multivibrator: There are no stable stated: the state keeps changing randomly from one to the other. It does not require an input.



[<img src="https://www.edgefxkits.com/blog/wp-content/uploads/Astable_multivibrator.png" alt="Astable_multivibrator" style="zoom: 25%;" />](https://www.edgefxkits.com/blog/wp-content/uploads/Astable_multivibrator.png)



​	**Monostable** multivibrators: One single stable state,

[<img src="https://www.edgefxkits.com/blog/wp-content/uploads/Monostable-Multivibrator.jpg" alt="Monostable Multivibrator" style="zoom: 80%;" />](https://www.edgefxkits.com/blog/wp-content/uploads/Monostable-Multivibrator.jpg)



​	**Bistable** multivibrator:





The two stable states of a bistable multivibrator are High  (logic 1) and Low (logic 0).

Flip flop is formed using **logic gates**, which are in turn made of transistors. Flip flop are basic building blocks in the memory of electronic devices.



#### ***Logic Block*** Architecture :



[![img](https://upload.wikimedia.org/wikipedia/commons/thumb/1/1c/FPGA_cell_example.png/350px-FPGA_cell_example.png)](https://en.wikipedia.org/wiki/File:FPGA_cell_example.png)
