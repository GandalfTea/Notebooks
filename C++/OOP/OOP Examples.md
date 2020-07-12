`#	https://www3.ntu.edu.sg/home/ehchua/programming/cpp/cp3_OOP.html#zz-2.4`



[toc]



#### 1.0 The Circle Class.



![ClassDiagramCircle.png](https://www3.ntu.edu.sg/home/ehchua/programming/cpp/images/ClassDiagramCircle.png)



Instead of putting all the codes in a single file. We shall "separate the interface and implementation" by placing the codes in 3 files.

1. `Circle.h`:  defines the public interface of the `Circle` class.
2. `Circle.cpp`:  provides the implementation of the `Circle` class.
3. `TestCircle.cpp`: A test driver program for the `Circle` class.



##### 1.1 Circle.h - Heather

```c++
#include <string>   // using string
using namespace std;
 
// Circle class declaration
class Circle {
private:   // Accessible by members of this class only
   // private data members (variables)
   double radius;
   string color;
 
public:    // Accessible by ALL
   // Declare prototype of member functions
   // Constructor with default values
   Circle(double radius = 1.0, string color = "red");
 
   // Public getters & setters for private data members
   double getRadius() const;
   void setRadius(double radius);
   string getColor() const;
   void setColor(string color);
 
   // Public member Function
   double getArea() const;
};
```



Program Notes:

- The header file contains  *declaration* statements, that tell the compiler about the names and types, and function prototypes without the implementation details.

- C++98/03 does NOT allow you to assign an initial value to a data member (except const static members). Date members are to be initialized via the constructor. For example,  

  ```
  double radius = 1.0;
     // error: ISO C++ forbids in-class initialization of non-const static member 'radius'
  ```

    C++11 allows in-class initialization of data members.

- You can provide default value to function's arguments in the header. For example,  

  ```
  Circle(double radius = 1.0, string color = "red");
  ```

- Header contains function prototype, the parameter names are ignored  by the compiler, but good to serve as documentation. For example, you  can leave out the parameter names in the prototype as follows:  

  ```
  Circle(double = 1.0, string = "red");   // without identifiers
     // Identifiers not needed in prototype but good to serve as documentation
  ```

Header files shall contains constants, function prototypes, class/struct declarations.





##### 1.2 Circle.cpp - Implementation

```c++
/* The Circle class Implementation (Circle.cpp) */
#include "Circle.h" // user-defined header in the same directory
 
// Constructor
// default values shall only be specified in the declaration,
// cannot be repeated in definition
Circle::Circle(double r, string c) {
   radius = r;
   color = c;
}
 
// Public getter for private data member radius
double Circle::getRadius() const {
   return radius;
}
 
// Public setter for private data member radius
void Circle::setRadius(double r) {
   radius = r;
}
 
// Public getter for private data member color
string Circle::getColor() const {
   return color;
}
 
// Public setter for private data member color
void Circle::setColor(string c) {
   color = c;
}
 
// A public member function
double Circle::getArea() const {
   return radius*radius*3.14159265;
}
```



Program Notes:

- The implementation file provides the *definition* of the functions, which are omitted from the *declaration* in the header file.

- \#include "Circle.h"
   The compiler searches the headers in double quotes (such as `"Circle.h"`) in the *current directory* first, then the system's include directories. For header in angle bracket (such as `<iostream>`), the compiler does NOT searches the current directory, but only the  system's include directories. Hence, use double quotes for user-defined  headers.

- Circle::Circle(double r, string c) {
   You need to include the `*className*::` (called *class scope resolution operator*) in front of all the members names, so as to inform the compiler this member belong to a particular class.
   (Class Scope: Names defined inside a class have so-called *class scope*. They are visible within the class only. Hence, you can use the same  name in two different classes. To use these names outside the class, the class scope resolution operator `*className*::` is needed.)

- You CANNOT place the default arguments in the implementation (they shall be placed in the header). For example,

  ```c++
  Circle::Circle(double r = 1.0, string c = "red") {   // error!
  ```



To compile: `g++ -c Circle.cpp` in cmd.





##### 1.3 TestCircle.cpp - Test Driver

```c++
/* A test driver for the Circle class (TestCircle.cpp) */
#include <iostream>
#include "Circle.h"   // using Circle class
using namespace std;
 
int main() {
   // Construct an instance of Circle c1
   Circle c1(1.2, "red");
   cout << "Radius=" << c1.getRadius() << " Area=" << c1.getArea()
        << " Color=" << c1.getColor() << endl;
 
   c1.setRadius(2.1);   // Change radius and color of c1
   c1.setColor("blue");
   cout << "Radius=" << c1.getRadius() << " Area=" << c1.getArea()
        << " Color=" << c1.getColor() << endl;
 
   // Construct another instance using the default constructor
   Circle c2;
   cout << "Radius=" << c2.getRadius() << " Area=" << c2.getArea()
        << " Color=" << c2.getColor()  << endl;
   return 0;
}
```



To compile: `g++ -o TestCircle.exe TestCircle.cpp Circle.o` in cmd.





#### 2.0 The Time Class



![class diagram](https://www3.ntu.edu.sg/home/ehchua/programming/cpp/images/ClassDiagramTime.png)



Let's write a class called `Time`, which models a specific instance of time with hour, minute and second values, as shown in the class diagram.

The class `Time` contains the following members:

- Three `private` data members: `hour` (0-23), `minute` (0-59) and `second` (0-59), with default values of 0.
- A `public` constructor `Time()`, which initializes the  data members `hour`, `minute` and `second` with the  values provided by the caller.
- `public` getters and setters for private data members: `getHour()`, `getMinute()`, `getSecond()`, `setHour()`, `setMinute()`, and `setSecond()`.
- A `public` member function `setTime()` to set the values of `hour`, `minute` and `second` given by the caller.
- A `public` member function `print()` to print this `Time` instance in the format "`hh:mm:ss`", zero-filled, e.g., `01:30:04`.
- A `public` member function `nextSecond()`, which increase this instance by one second. `nextSecond()` of `23:59:59` shall be `00:00:00`.

Let's write the code for the `Time` class, with the header and implementation separated in two files: `Time.h` and `Time.cpp`.



##### Time.h

```c++
/* Header for the Time class (Time.h) */
#ifndef TIME_H   // Include this "block" only if TIME_H is NOT defined
#define TIME_H   // Upon the first inclusion, define TIME_H so that
                 //  this header will not get included more than once
class Time {
private:  // private section
   // private data members
   int hour;     // 0 - 23
   int minute;   // 0 - 59
   int second;   // 0 - 59
 
public:   // public section
   // public member function prototypes
   Time(int h = 0, int m = 0, int s = 0); // Constructor with default values
   int getHour() const;   // public getter for private data member hour
   void setHour(int h);   // public setter for private data member hour
   int getMinute() const; // public getter for private data member minute
   void setMinute(int m); // public setter for private data member minute
   int getSecond() const; // public getter for private data member second
   void setSecond(int s); // public setter for private data member second
   void setTime(int h, int m, int s);   // set hour, minute and second
   void print() const; // Print a description of this instance in "hh:mm:ss"
   void nextSecond();  // Increase this instance by one second
};  // need to terminate the class declaration with a semicolon
 
#endif  // end of "#ifndef" block
```



###### Dissecting Time.h

```c++
#ifndef TIME_H
#define TIME_H
 ......
#endif
```


To prevent an header file from included *more than once* into a source file (which could result in compilation error if an entity is declared twice, e.g., ()`int i`), we wrap the header codes within a pair of *preprocessor directives* `#ifndef` (if not define) and `#endif`. The codes within the if-block will only be included if the identifier `TIME_H` has not been defined. This is true for the first inclusion, which also defines the identifier `TIME_H` (the first directive in body of the if-block). No subsequent inclusion is possible, since `TIME_H` has been defined during the first inclusion. By convention, use the identifier `XXX_H` (or `XXX_H_INCLUDED`) for header `Xxx.h`.

```c++
class Time {
 private:
 ......
 public:
 ......
 };
```


The header `Time.h` contains the *class declaration* for the class `Time`. It is divided into two sections: `private` and `public`. The `private` members (data or functions) are accessible by members of this class only, while `public` members are visible by all (such as the `main()` function which is outside the class). The class declaration must be terminated by a semicolon.

```c++
private:
  int hour;
  int minute;
  int second;
 public:
  ......
```


We declare 3 private data members called `hour`, `minute` and `second`. In C++98/C++03, you are NOT allow to initialize a data member in the class declaration (except `const` `static` `int` data members). For example, setting `hour = 0` causes a compilation error.  Instead, the data members are to be  initialized in the constructor (to be shown later). The newer C++11  allows initialization of data members.

Only *member function prototypes* are listed in the class declaration. A function prototype consists of the return-type, function name and parameter types.

```c++
Time(int h = 0, int m = 0, int s = 0);
```


declares the so-called *constructor*. A constructor is a special  function that has the same name as the class. A constructor has no  return type, or implicitly return `void`. No `return` statement is allowed inside the constructor's body. A constructor can  only be used during the instance declaration to initialize the data  members of the instance. It cannot be invoked thereafter.

In the function prototypes of the header, we can set the default  values of the function's parameters for any function member using "`= *default-value*`".  In this case, this constructor can be invoked with 0 to 3 arguments, the omitted *trailing* arguments will be set to their default values, e.g.,

```c++
Time t1(1, 2, 3); // no default used
Time t2(1, 2);    // s = 0 (default)
Time t3(1);       // m = 0, s = 0 (defaults)
Time t4;          // h = 0, m = 0, s = 0 (all defaults) - no empty parentheses ()
```

The identifiers `h`, `m` and `s` are not needed in the function prototype - you only need to specify the  parameters' types. But they serve as  proper documentation, and are  strongly recommended.

```c++
int getHour() const;
void setHour(int h);
int getHour() const;
void setHour(int h);
int getHour() const;
void setHour(int h);
```

declare the so-called *getter* and *setter* for the private data member `hour`, `minute` and `second`. Since the data members are `private` and are not accessible outside the class, `public` getters and setters are often provided to read and modify the `private` data members.  By convention, a getter receives nothing (`void`) from the caller and returns a value of the type of the data member; a  setter receives a value of the type of the data member and returns `void`. Setters may validate the input before setting the value of the data member.
We declare the getter function *constant*, by placing the keyword const after the function parameter list. A `const` member function cannot modify any data member of this object. Getter does not need to modify any data member.

```c++
void setTime(int h, int m, int s);
```


declares a public member function to set the `hour`, `minute` and `second` of this instance in one call.

```c++
void print() const;
```


declares a public member function to print this instance in the format `HH:MM:SS`, zero-filled, e.g., `01:56:09`. The function `print()` returns `void`.

```c++
void nextSecond();
```


declares a public member function to increase this instance by one second. For example, `23:59:59` becomes `00:00:00`. The function `nextSecond()` returns `void`.





##### Time.cpp

```c++
/* Implementation for the Time Class (Time.cpp) */
#include <iostream>
#include <iomanip>
#include "Time.h"    // include header of Time class
using namespace std;
 
// Constructor with default values. No input validation
Time::Time(int h, int m, int s) {
   hour = h;
   minute = m;
   second = s;
}
 
// public getter for private data member hour
int Time::getHour() const {
   return hour;
}
 
// public setter for private data member hour. No input validation
void Time::setHour(int h) {
   hour = h;
}
 
// public getter for private data member minute
int Time::getMinute() const {
   return minute;
}
 
// public setter for private data member minute. No input validation
void Time::setMinute(int m) {
   minute = m;
}
 
// public getter for private data member second
int Time::getSecond() const {
   return second;
}
 
// public setter for private data member second. No input validation
void Time::setSecond(int s) {
   second = s;
}
 
// Set hour, minute and second. No input validation
void Time::setTime(int h, int m, int s) {
   hour = h;
   minute = m;
   second = s;
}
 
// Print this Time instance in the format of "hh:mm:ss", zero filled
void Time::print() const {
   cout << setfill('0');    // zero-filled, need <iomanip>, sticky
   cout << setw(2) << hour  // set width to 2 spaces, need <iomanip>, non-sticky
        << ":" << setw(2) << minute
        << ":" << setw(2) << second << endl;
}
 
// Increase this instance by one second
void Time::nextSecond() {
   ++second;
   if (second >= 60) {
      second = 0;
      ++minute;
   }
   if (minute >= 60) {
      minute = 0;
      ++hour;
   }
   if (hour >= 24) {
      hour = 0;
   }
}
```



###### Dissecting Time.cpp

The implementation file `Time.cpp` contains member's definitions (whereas the header file contains the declarations), in particular, member functions.

All member's identifiers in the implementation are preceded by the *classname* and the *scope resolution operator* (`::`), e.g., `Time::Time` and `Time::getHour`, so that the compiler can tell that these identifiers belong to a particular class, in this case, `Time`.

```c++
Time::Time(int h, int m, int s) {
  hour = h;
  minute = m;
  second = s;
 }
```

In the constructor, we initialize the `private` data members `hour`, `minute` and `second` based on the inputs provided by the caller. C++ does NOT initialize  fundamental-type (e.g., `int`, `double`) data members. It also does NOT issue an error message if you use an  data member before it is initialized. Hence, It is strongly recommended  to initialize all the data members in the constructor, so that the  constructed instance is complete, instead of relying on the user to set  the values of the data members after construction.

The default values of the parameters are specified in the class  declaration (in the header), NOT in the function definition. Placing a  default value in function definition (e.g., `h = 0`) causes a compilation error.

Take note that we have not included input validation (e.g., hour  shall be between 0 and 23) in the constructor (and setters). We shall do that in the later example.

```c++
int Time::getHour() const {
  return hour;
 }
```


 the public getter for private data member `hour` simply returns the value of the data member `hour`.

```c++
void Time::setHour(int h) {
  hour = h;
 }
```


 the public setter for private data member `hour` sets the data member `hour` to the given value `h`. Again, there is no input validation for h (shall be between 0 to 23).

The rest of the function definitions are self-explanatory.

###### "this" Pointer

Instead of naming the function parameters `h`, `m` and `s`, we would like to name the parameters `hour`, `minute` and `second`, which are semantically more meaningful. However, these names crashes  with the names of private data members. C++ provides a keyword `this` (which is a pointer to this instance - to be discussed later) to  differentiate between the data members and function parameters. `this->hour`, `this->minute` and `this->second` refer to the data members; while `hour`, `minute`, and `second` refer to the function parameters. We can rewrite the constructor and setter as follows:

```c++
Time::Time(int hour, int minute, int second) {  // Constructor
   this->hour = hour;
   this->minute = minute;
   this->second = second;
}
 
Time::setHour(int hour) {   // Setter for hour
   this->hour = hour;
}
 
Time::getHour() const {  // Getter for hour
   return this->hour;    // this-> is the default, and hence optional
}
```

###### Member Initializer List

C++ provide an *alternative syntax* to initialize data members in the constructor called  *member initializer list*. For example,

```c++
Time::Time(int h, int m, int s) : hour(h), minute(m), second(s) { 
   // The body runs after the member initializer list
   // empty in this case
}
```

The  member initializer list is placed after the function parameter list, separated by a colon, in the form of `*dataMemberName*(*parameters*)`. For fundamental-type data members (e.g., `int`, `double`), `hour(h)` is the same as `hour = h`. For object data members (to be discussed later), the *copy constructor* will be invoked. The function body will be executed *after* the  member initializer list, which is empty in this case.

The data members in the initializer list are initialized in the order of their declarations in the class declaration, not the order in the  initializer list.





##### Test Driver - TestTime.cpp

```c++
/* Test Driver for the Time class (TestTime.cpp) */
#include <iostream>
#include "Time.h"    // include header of Time class
using namespace std;
 
int main() {
   Time t1(23, 59, 59);   // Test constructor
 
   // Test all public member functions
   t1.print();       // 23:59:59
   t1.setHour(12);
   t1.setMinute(30);
   t1.setSecond(15);
   t1.print();       // 12:30:15
   cout << "Hour is "   << t1.getHour()   << endl;
   cout << "Minute is " << t1.getMinute() << endl;
   cout << "Second is " << t1.getSecond() << endl;
 
   Time t2;     // Test constructor with default values for hour, minute and second
   t2.print();  // 00:00:00
   t2.setTime(1, 2, 3);
   t2.print();  // 01:02:03
 
   Time t3(12); // Use default values for minute and second
   t3.print();  // 12:00:00
 
   // Test nextSecond()
   Time t4(23, 59, 58);
   t4.print();
   t4.nextSecond();
   t4.print();
   t4.nextSecond();
   t4.print();
 
   // No input validation
   Time t5(25, 61, 99); // values out of range
   t5.print();  // 25:61:99
}
```



###### Dissecting TestTime.cpp

The test driver tests the constructor (with and without the default  values) and all the public member functions. Clearly, no input  validation is carried out, as reflected in instance `t5`.



###### Exercise

Add member functions `previousSecond()`, `nextMinute()`, `previousMinute()`, `nextHour()`, `previousHour()` to the `Time` class.



###### Compiling the Program

You can compile all the source file together to get the executable file as follows:

```
// Using GCC on Windows
// Compile all source files, -o specifies the output
> g++ -o TestTime.exe Time.cpp TestTime.cpp  
// Execute the program
> TestTime
```

Alternatively, you can compile `Time.cpp` into an object file `Time.o`, and then the test driver with the object file. In this way, you only  distribute the object file and header file, not the source file.

```
// Compile Time.cpp into object file Time.o, with -c option
> g++ -c Time.cpp
// Compile test driver with object file
> g++ -o TestTime.exe TestTime.cpp Time.o
// Execute the test driver
> TestTime
```