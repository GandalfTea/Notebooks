

`# https://www3.ntu.edu.sg/home/ehchua/programming/cpp/cp4_PointerReference.html`

[toc]

#### 1. Pointer Variables

A computer memory location, that holds some information (int, bool, char, etc.), also has an address attached to it. It is *hexadecimal* (numbers with a base of 16), 8 bit number:



  ![MemoryAddressContent.png](https://www3.ntu.edu.sg/home/ehchua/programming/cpp/images/MemoryAddressContent.png)

A *pointer variable*, henceforth *pointer*, is the same as other variables but only stores memory addresses. 





##### 1.2 Declaring pointers

A pointer is associated with a type, therefore:

```c++
type *ptr;  // Declare a pointer variable called ptr as a pointer of type
type* ptr; 
type * ptr; // All syntax works.

int * ptr;  //Pointer named ptr pointing to an int.
			//The address holds an int value.
```

When declaring multiple pointers, you need to place the * for every name.

```c++
int *p1, *p2, i;   //p1 and p2 are pointes and i is an int.
int* p1, p2, i;	   //p1 is a pointer, p2 and i are int.
int * p1, * p2, i; //p1 and p2 are pointes and i is an int.
```



Naming Convention of Pointers:  Include a `p` or `ptr` as *prefix* or *suffix*:  `iPtr`, `numberPtr`, `pNumber`, `pStudent`.





##### 1.3 Address-Of Operator - *&*

The address-of operator can only be used on the *RHS* (right hand side).

```c++
int number = 88;	//int Number with a value of 88.
int * pNumber;		//Pointer pNumber with no value, pointing to an int.
pNumber = &number;	//pNumber = the address of number.

pNumber = 0c22ccec.	//Address of number
number = 88.
```



![PointerDeclaration.png](https://www3.ntu.edu.sg/home/ehchua/programming/cpp/images/PointerDeclaration.png)

​				



##### 1.4 Dereferencing operator  -  *

The *indirection operator* (or *dereferencing operator*) `*` operates on a pointer, and returns the value stored in the address kept in the pointer variable. 

```c++
int number = 88;	//int Number with a value of 88.
int * pNumber;		//Pointer pNumber with no value, pointing to an int.
pNumber = &number;	//pNumber = the address of number.

cout << pNumber << endl;  //Print the content address.
cout << *pNumber << endl; //Print the value stored in the address.

*pNumber = 99;			  //Assign the value to the address pointed to.
						  //NOT to the pointer.

cout << *pNumber << endl; //Prints out 99.
cout << number << endl;   //Has also changed to 99.
```

`pNumber` is a pointer, it stores the address of an int.

`*pNumber` dereferences pNumber and it represents the value pointed to.



Referencing a value indirectly via a  pointer is called *indirection* or *dereferencing*.

The indirection operator `*` can be used in both the RHS   `temp = *pNumber` and the LHS `*pNumber = 99` of an assignment statement.





##### 1.5 Pointers need a type

The type of a pointer (int, double, etc.) needs to be specified in the declaration. A pointer can only hold an address of the declared type; it cannot hold an address of a different type.

```c++
int i = 88;
double d = 55.66;
int * iPtr = &i;	//pointer to i
double * dPtr = &d;	//pointer to d

iPtr = &d;	//ERROR, different type.
dPtr = &i;	//ERROR, different type.
iPtr = i;	//ERROR, pointer holds address, not value.

int j = 99;
iPtr = &j; 	//You can change the address stored in a pointer.
```

```c++
//EXAMPLE

#include <iostream>
using namespace std;
 
int main() {
   int number = 88;    // Declare an int variable and assign an initial value
   int * pNumber;      // Declare a pointer variable pointing to an int (or int pointer)
   pNumber = &number;  // assign the address of the variable number to pointer pNumber
 
   cout << pNumber << endl;  // Print content of pNumber (0x22ccf0)
   cout << &number << endl;  // Print address of number (0x22ccf0)
   cout << *pNumber << endl; // Print value pointed to by pNumber (88)
   cout << number << endl;   // Print value of number (88)
 
   *pNumber = 99;            // Re-assign value pointed to by pNumber
   cout << pNumber << endl;  // Print content of pNumber (0x22ccf0)
   cout << &number << endl;  // Print address of number (0x22ccf0)
   cout << *pNumber << endl; // Print value pointed to by pNumber (99)
   cout << number << endl;   // Print value of number (99)
                             // The value of number changes via pointer
 
   cout << &pNumber << endl; // Print the address of pointer variable pNumber (0x22ccec)
}
```





##### 1.6 Uninitialized pointers

This is a logical error:

```c++
int * iPtr;
*iPtr = 55;
cout << *iPtr <<endl;
```

The pointer `iPtr` was declared without initialization, it is pointing to "somewhere" which is of course an invalid memory location. The `*iPtr = 55` corrupts the value of "somewhere". You need to initialize a pointer by  assigning it a valid address. Most of the compilers does not signal an  error or a warning for uninitialized pointer.





##### 1.7 Null pointers

You can initialize a pointer to 0 or NULL, to point to nothing. Dereferencing a null pointer causes a `STATUS_ACCESS_VIOLATON` exception.

```c++
int * iPtr = 0; // Declare an int pointer, and initialize the pointer to point to nothing
nt * p = NULL; // Also declare a NULL pointer points to nothing

cout << *iPtr << endl;  // ERROR! STATUS_ACCESS_VIOLATION exception
```

Initialize a pointer to null during declaration is a good software engineering practice.

C++11 introduces a new keyword called `nullptr` to represent null pointer.





#### 2.Reference Variables

A reference is an *alias*, or an *alternate name* to an existing variable. The main use of references is acting as function formal parameters to support pass-by-reference.  If a reference variable is passed into a function, the function works  on the original copy (instead of a clone copy in pass-by-value). Changes inside the function are reflected outside the function.

A reference is similar to a pointer. In many cases, a reference can be  used as an alternative to pointer, in particular, for the function  parameter.





##### 2.1 References (or Aliases) - &

The meaning of symbol `&` is different in an expression and in a declaration. When it is used in an expression, `&` denotes the address-of operator, which returns the address of a variable, e.g., if `number` is an `int` variable, `&number` returns the address of the variable `number` (this has been described in the above section). However, when `&` is used in a *declaration* (including *function formal parameters*), it is part of the type identifier and is used to declare a *reference variable* (or *reference* or *alias* or *alternate name*). It is used to provide *another name*, or *another reference*, or *alias* to an existing variable.



Syntax:

```c++
type &newName = existingName;
type& newName = existingName;
type & newName = existing Name;
```

Example:

```c++
#include <iostream>
using namespace std;
 
int main() {
   int number = 88;          // Declare an int variable called number
   int & refNumber = number; // Declare a reference (alias) to the variable number
                             // Both refNumber and number refer to the same value
 
   cout << number << endl;    // Print value of variable number (88)
   cout << refNumber << endl; // Print value of reference (88)
 
   refNumber = 99;            // Re-assign a new value to refNumber
   cout << refNumber << endl;
   cout << number << endl;    // Value of number also changes (99)
 
   number = 55;               // Re-assign a new value to number
   cout << number << endl;
   cout << refNumber << endl; // Value of refNumber also changes (55)
}
```





##### 2.2 How references work

A reference works as a pointer. A reference is declared as an alias of a variable. It stores the address of the variable, as illustrated:



![ReferenceIsAPointer.png](https://www3.ntu.edu.sg/home/ehchua/programming/cpp/images/ReferenceIsAPointer.png)



##### 2.3 Reference vs Pointer

Pointers and references are equivalent, except:

   1. A reference is a *name constant for an address*. You need to initialize the reference during declaration.

      ```c++
      int & iRef;   // Error: 'iRef' declared as reference but not initialized
      ```

      Once a reference is established to a variable, you cannot change the reference to reference another variable.

      

2.  No need to reference and dereference.

   

Example:

```c++
#include <iostream>
using namespace std;
 
int main() {
   int number1 = 88, number2 = 22;
 
   // Create a pointer pointing to number1
   int * pNumber1 = &number1;  // Explicit referencing
   *pNumber1 = 99;             // Explicit dereferencing
   cout << *pNumber1 << endl;  // 99
   cout << &number1 << endl;   // 0x22ff18
   cout << pNumber1 << endl;   // 0x22ff18 (content of the pointer variable - same as above)
   cout << &pNumber1 << endl;  // 0x22ff10 (address of the pointer variable)
   pNumber1 = &number2;        // Pointer can be reassigned to store another address
 
   // Create a reference (alias) to number1
   int & refNumber1 = number1;  // Implicit referencing (NOT &number1)
   refNumber1 = 11;             // Implicit dereferencing (NOT *refNumber1)
   cout << refNumber1 << endl;  // 11
   cout << &number1 << endl;    // 0x22ff18
   cout << &refNumber1 << endl; // 0x22ff18
   //refNumber1 = &number2;     // Error! Reference cannot be re-assigned
                                // error: invalid conversion from 'int*' to 'int'
   refNumber1 = number2;        // refNumber1 is still an alias to number1.
                                // Assign value of number2 (22) to refNumber1 (and number1).
   number2++;   
   cout << refNumber1 << endl;  // 22
   cout << number1 << endl;     // 22
   cout << number2 << endl;     // 23
}
```







##### 2.4 Pass-by-reference in Functions with Reference vs Pointer Arguments



###### Pass by Value

In C/C++, by default, arguments are passed into functions *by value* (except arrays which is treated as pointers). That is, a clone copy of the argument is made and passed into the  function. Changes to the clone copy inside the function has no effect to the original argument in the caller. In other words, the called  function has no access to the variables in the caller. For example:

```c++
#include <iostream>
using namespace std;
 
int square(int);
 
int main() {
   int number = 8;
   cout <<  "In main(): " << &number << endl;  // 0x22ff1c
   cout << number << endl;         // 8
   cout << square(number) << endl; // 64
   cout << number << endl;         // 8 - no change
}
 
int square(int n) {  // non-const
   cout <<  "In square(): " << &n << endl;  // 0x22ff00
   n *= n;           // clone modified inside the function
   return n;
}
```

The output clearly shows that there are two different addresses.





###### Pass-by-Reference with Pointer Arguments

In many situations, we may wish to modify the original copy directly or avoid cloning big files. We use pointers for that.

For exemple:

```c++
#include <iostream>
using namespace std;
 
void square(int *);
 
int main() {
   int number = 8;
   cout <<  "In main(): " << &number << endl;  // 0x22ff1c
   cout << number << endl;   // 8
   square(&number);          // Explicit referencing to pass an address
   cout << number << endl;   // 64
}
 
void square(int * pNumber) {  // Function takes an int pointer (non-const)
   cout <<  "In square(): " << pNumber << endl;  // 0x22ff1c
   *pNumber *= *pNumber;      // Explicit de-referencing to get the value pointed-to
}
```

The called function operates on the same address, and can thus modify the variable in the caller.





###### Pass-by-Reference with Reference Arguments

Instead of passing pointers into function, you could also pass  references into function, to avoid the clumsy syntax of referencing and  dereferencing. For example:

```c++
#include <iostream>
using namespace std;
 
void square(int &);
 
int main() {
   int number = 8;
   cout <<  "In main(): " << &number << endl;  // 0x22ff1c
   cout << number << endl;  // 8
   square(number);          // Implicit referencing (without '&')
   cout << number << endl;  // 64
}
 
void square(int & rNumber) {  // Function takes an int reference (non-const)
   cout <<  "In square(): " << &rNumber << endl;  // 0x22ff1c
   rNumber *= rNumber;        // Implicit de-referencing (without '*')
}
```





###### *const* Function Reference/Pointer Parameters

A `const` function formal parameter cannot be modified inside the function. Use `const` whenever possible as it protects you from inadvertently modifying the  parameter and protects you against many programming errors.

A `const` function parameter can receive both `const` and non-`const` argument. On the other hand, a non-`const` function reference/pointer parameter can only receive non-`const` argument. For example:

```c++
#include <iostream>
using namespace std;
 
int squareConst(const int);
int squareNonConst(int);
int squareConstRef(const int &);
int squareNonConstRef(int &);
 
int main() {
   int number = 8;
   const int constNumber = 9;
   cout << squareConst(number) << endl;
   cout << squareConst(constNumber) << endl;
   cout << squareNonConst(number) << endl;
   cout << squareNonConst(constNumber) << endl; //Doesn't work
 
   cout << squareConstRef(number) << endl;
   cout << squareConstRef(constNumber) << endl;
   cout << squareNonConstRef(number) << endl;
    
   cout << squareNonConstRef(constNumber) << endl;
       // error: invalid initialization of reference of
       //  type 'int&' from expression of type 'const int'
}
 
int squareConst(const int number) {
   // number *= number;  // error: assignment of read-only parameter
   return number * number;
}
 
int squareNonConst(int number) {  // non-const parameter
   number *= number;
   return number;
}
 
int squareConstRef(const int & number) {  // const reference
   return number * number;
}
 
int squareNonConstRef(int & number) {  // non-const reference
   return number * number;
}
```





##### 2.5 Passing the function's Return Value
