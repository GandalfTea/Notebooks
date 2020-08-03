[toc]

#### **Memory Allocation in C++**

C++ supports three basic types of memory allocation:

- ***Static* memory allocation** holds initialized global and static variables.
- ***Automatic* memory allocation** for function parameters and local variables.
- ***Dynamic* memory allocation**.

Both Static and Automatic know the size of variables at run time and memory allocation and deallocation happens automatically. 





#### **Memory Allocation in RAM**

The typical C++ program uses is divided into 5 areas:

-  ***The Code* segment** -- Read only segment holding the compiled program.
- ***The BSS* segment** (uninitialized data segment) -- holds zero-initialized global and static variables are stored.
- ***The Data* segment** (initialized data segment) -- **Static Memory Allocation**.
- ***The Heap*** -- **Dynamic Memory Allocation**.
- ***The Call Stack*** -- **Automatic Memory Allocation**.

**TODO**: Are they all in RAM? How do they connect when running program? How to optimize for them?

**TODO**: Virtual Memory? `@	http://www.programmerinterview.com/operating-systems/how-virtual-memory-works/` 

**TODO**: How is memory stored in RAM?







##### **The Heap** 

###### Data Structure

`@	https://web.stanford.edu/class/archive/cs/cs161/cs161.1168/lecture4.pdf `

`@	https://www.cs.cmu.edu/~adamchik/15-121/lectures/Binary%20Heaps/heaps.html](https://www.cs.cmu.edu/~adamchik/15-121/lectures/Binary Heaps/heaps.html)`



The heap is a self organizing data structure that can implement a priority queue. The ***max heap*** organizes itself by ordering the numbers stored in it by high to low. It is structured like a tree made from nodes, each node has two or fewer children and the key of each node (the number inside) is greater than the keys of it's child nodes. There are also ***min heaps*** that reverse the structure, from low to high.

The root of a max heap is the largest element stored in it and reverse for the min heap.

All the nodes in the heap must be filled except the ones at the bottom. The bottom level gets filled from left to right.

![img](https://www.cs.cmu.edu/~adamchik/15-121/lectures/Binary%20Heaps/pix/heap.bmp)







###### C++ Implementation

`@	https://www.learncpp.com/cpp-tutorial/79-the-stack-and-the-heap/`



The RAM Heap is a memory that is managed by the programmer. This means that you have to allocate and delete the variables yourself. If this is not done correctly, it can result in *memory leak*. The heap keeps track of memory used for Dynamic Memory Allocation. It is big, therefore large arrays, structures or classes can be allocated here. Although, it is slower than the stack.

This is how you allocate memory on the heap:

```c++
int main(){
    //allocate an initialized int on the heap.
    int *ptr1 {new int(5)};
    
	//This allocated memory for 10 integers on the heap.
	int *ptr2 = new int[10];
}
```

And delete:

```c++
delete ptr1; //return the memory allocated for ptr1 to the system.
ptr = 0 // set ptr to a nullpointer
```

Note that deleting a pointer that is not pointing to dynamically allocated memory may cause bad things to happen.

A pointer that is pointing to deallocated memory is called a *dangling pointer* :

```c++
int *ptr{new int};
*ptr = 7; // put a value in that memory location
 
delete ptr; // return the memory to the operating system.  ptr is now a dangling pointer.

std::cout << *ptr; // Dereferencing a dangling pointer will cause undefined behavior
delete ptr; // trying to deallocate the memory again will also lead to undefined behavior.
```

**TODO**: How does the heap data structure influence the c++ variables? How to use heap in programs?

**TODO**: Heap priority queue?







###### Memory Leaks

`@	https://www.learncpp.com/cpp-tutorial/69-dynamic-memory-allocation-with-new-and-delete/`

Dynamically allocated memory stays allocated until it is explicitly deallocated or until the program ends.

```c++
void doSomething()
{
    int *ptr{ new int{} };
}
```

In this case, the dynamically allocated integer is never deleted. Because `*ptr` is only inside the scope of the function `doSomething()`, it will become out of scope when the function ends. Because it was the only reference to this dynamically allocated integer, the program no longer holds an address for it and therefore cannot delete it. This is called a *memory leak*.

The operating system cannot use that memory because it is considered still in use by the program. Only after the program terminates is the operating system able to reclaim that memory. This can lead to buildups of dump memory, lag and crashes.

This can also happen when to the pointer holding the address is assigned another value or through double allocation.

```c++
int value = 5;
int *ptr{ new int{} }; // allocate memory
delete ptr; //THIS IS THE FIX
ptr = &value; // old address lost, memory leak results
```







##### **The Call Stack**

###### Data Structure

`@	https://www.geeksforgeeks.org/stack-data-structure-introduction-program/`

The Stack or Call Stack is a linear data structure with predetermined capacity that is LIFO (Last in First out). Every time an element is added, it goes to the top and the only element that can be removed is the top one.  The stack is said to be in *Overflow* when it is full and *Underflow* when it is empty.



![Stack Data Structure | Studytonight](https://www.studytonight.com/data-structures/images/stack-data-structure.png)



###### C++ Implementation

`@	https://www.learncpp.com/cpp-tutorial/79-the-stack-and-the-heap/`

The call stack is not managed by the programmer and is instead allocated and deallocated by the OS. The Stack keeps track of all active functions (called but not terminated) from the start of the program and handles allocation of all function parameters and local variables.

The functions associated with the stack are:

```c++
empty() – 	//Returns whether the stack is empty – 	Time Complexity : O(1)
size() –	 //Returns the size of the stack – 	Time Complexity : O(1)
top() –	 //Returns a reference to the top most element of the stack – 	Time Complexity : O(1)
push(g) –	 //Adds the element ‘g’ at the top of the stack – 	Time Complexity : O(1)
pop() –	 //Deletes the top most element of the stack – 	Time Complexity : O(1)
```





###### Inner workings of the Stack

The call stack segment holds the memory used for the call stack. When the application starts, the main() function is pushed on the call stack by the OS. When a function call is encountered, the function is pushed unto the stack and when it ends it is popped. The stack itself is a fixed-size chunk of memory addresses and the *items* we’re pushing and popping on the stack are called **stack frames**. A stack frame keeps track of all of the data associated with one function call. Each frame has a register (a small piece of memory in the CPU) known as the **stack pointer** (*SP*).  The stack pointer keeps track of where the top of the call stack currently is.  We can just leave it to be overwritten by the next item pushed to that  piece of memory.  Because the stack pointer will be below that memory location, we know that memory location is not on the stack.

**TODO**:  How is reallocation done?







###### Stack Register in CPU (**Stack Pointer**)

**TODO**





###### **Frame Pointer**

**TODO**





###### **Stack Frame**

`@	http://www.cs.uwm.edu/classes/cs315/Bacon/Lecture/HTML/ch10s07.html`

`@	http://staff.cs.upt.ro/~chirila/teaching/upt/c51-pt/aamcij/7113/Fly0042.html`

The Stack Frame, also known as the *Activation Record* is a collection of all data associated with a function or a *subprogram call*. 

It includes:

- The return address.
- Argument variables.
- Local variables that are not declares static.
- Saved copies of any registers modified by the function that need to be restored

Since the Stack frames are fixed in size, we update the stack pointer only once as to not have to push and pop all the variables separately. 

Example:

```c++
int function(int a, double b)	    
{
	int x, y;
	...
	return  x;
}
```

is stores as:

```
		Address Content
$sp --> F080    Saved return address	//$sp is the start of a stack pointer.
		F084    a
		F088    b
		F090    x
		F094    y
		F098    Saved register value
		F09C    Saved register value
```



[<img src="http://staff.cs.upt.ro/~chirila/teaching/upt/c51-pt/aamcij/7113/images/fig6-2.jpg" alt="Click To expand" style="zoom:150%;" />](http://staff.cs.upt.ro/~chirila/teaching/upt/c51-pt/aamcij/7113/images/fig6-2_0.jpg)



**TODO**: What is MIPS and subroutine calls?

`@ https://en.wikipedia.org/wiki/MIPS_architecture`





###### The Call Stack in Action

The sequences of steps that takes place when a function is called:

1. A function call is encountered.
2. A stack frame is constructed and pushed onto the stack. It consists of:
   1. The address of the instructions after the call (called the *return address*) to help the CPU remember where to return.
   2. All function arguments.
   3. Memory for any local variables.
   4. **TODO:** Saved copies of any registers modified by the function that need to be restored when the function returns?

3. The CPU jumps to the function starting point.
4. It executes the instructions in the function.
5. When it terminates, registers are restored from the call stack.
6. The stack frame is popped (deletes all variables and arguments)
7. The CPU resumes execution from the return address.

