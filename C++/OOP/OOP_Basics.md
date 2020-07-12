`#	https://www3.ntu.edu.sg/home/ehchua/programming/cpp/cp3_OOP.html`

[toc]



#### 1. Why OOP? 



It is trying to make programming modular, like cars or PC's. Hardware, such as computers and cars, are assembled from parts, which are reusable components. Unlike  hardware, it is very difficult to "assemble" an application from *software  components*. For each new application, we have to re-invent the wheels and write the program from scratch.



##### 1.1 Traditional Procedural-Oriented Languages.

Traditional procedural-oriented languages (such as C and Pascal) suffer  some notable drawbacks in creating reusable software components:

1. The programs are made up of functions. Functions are often not *reusable*. It is very difficult to copy a function from one program and reuse in  another program because the the function is likely to reference the  headers, global variables and other functions. In other words, functions are not well-encapsulated as a self-contained *reusable unit*.
2. The procedural languages are not suitable of *high-level abstraction* for solving real life problems.  For example, C programs uses  constructs such as if-else, for-loop, array, function, pointer, which  are low-level and hard to abstract real problems such as a Customer  Relationship Management (CRM) system or a computer soccer game.   (Imagine using assembly codes, which is a very low level code, to write a computer soccer game.  C is better but no much better.)

In brief, the traditional procedural-languages *separate* the data structures and algorithms of the software entities.



![OOP_CFunction.png](https://www3.ntu.edu.sg/home/ehchua/programming/java/images/OOP_CFunction.png)





##### 1.2 Object Oriented Programming Languages.

Object-oriented programming (OOP) languages are designed to overcome these problems.

1. The basic unit of OOP is a *class*, which encapsulates both the *static attributes* and *dynamic behaviors* within a "box", and specifies the public interface for using these  boxes. Since the class is well-encapsulated (compared with the  function), it is easier to reuse these classes. In other words, OOP  combines the data structures and algorithms of a software entity inside  the same box.

2. OOP languages permit *higher level of abstraction* for solving  real-life problems. The traditional procedural language (such as C and  Pascal) forces you to think in terms of the structure of the computer  (e.g. memory bits and bytes, array, decision, loop) rather than thinking in terms of the problem you are trying to solve.  The OOP languages  (such as Java, C++, C#) let you think in the problem space, and use  software objects to represent and abstract entities of the problem space to solve the problem.



![OOP_Objects.png](https://www3.ntu.edu.sg/home/ehchua/programming/java/images/OOP_Objects.png)



As an example, suppose you wish to write a computer soccer games  (which I consider as a complex application).  It is quite difficult to  model the game in procedural-oriented languages.  But using OOP  languages, you can easily model the program accordingly to the "real  things" appear in the soccer games.

- Player: attributes include name, number, location in the field, and etc;  operations include run, jump, kick-the-ball, etc.
- Ball:
- Reference:
- Field:
- Audience:
- Weather:

Most importantly, some of these classes (such as `Ball` and `Audience`) can be reused in another application, e.g., computer basketball game, with little or no modification.



![OOP_SoccerGame.png](https://www3.ntu.edu.sg/home/ehchua/programming/java/images/OOP_SoccerGame.png)





##### 1.3 Benefits of OOP.

The procedural-oriented languages focus on procedures, with function as  the basic unit. You need to first figure out all the functions and then  think about how to represent data.

The object-oriented languages focus on components that the user  perceives, with objects as the basic unit. You figure out all the  objects by putting all the data and operations that describe the user's  interaction with the data.

Object-Oriented technology has many benefits:

- *Ease in software design* as you could think in the problem space rather than the machine's bits and bytes.  You are dealing with  high-level concepts and abstractions.  Ease in design leads to more  productive software development.
- *Ease in software maintenance*:  object-oriented software are easier to understand, therefore easier to test, debug, and maintain.
- *Reusable software*: you don't need to keep re-inventing the  wheels and re-write the same functions for different situations.  The  fastest and safest way of developing a new application is to reuse  existing codes - fully tested and proven codes.





#### 2. OOP Basics



##### 2.1 Classes & Instances.

**Class**: A *class is a definition of  objects of the same kind*.  In other words, a *class* is a blueprint, template, or prototype that defines and describes the *static attributes* and *dynamic behaviors* common to all objects of the same kind.

**Instance**: An *instance* is *a realization of a particular item of a class*.  In other words, an instance is an *instantiation* of a class.  All the instances of a class have similar properties, as  described in the class definition.  For example, you can define a class  called "`Student`" and create three instances of the class "`Student`" for "`Peter`", "`Paul`" and "`Pauline`".

The term "*object*" usually refers to *instance*. But it  is often used quite loosely, which may refer to a class or an instance.



##### 2.2 A Class is a 3-compartment Box encapsulating Data and Functions.

A class can be visualized as a three-compartment box, as illustrated:



![OOP_ThreeCompartment.png](https://www3.ntu.edu.sg/home/ehchua/programming/cpp/images/OOP_ThreeCompartment.png)

1. ***Classname*** (or identifier):  identifies the class.

2. ***Data Members*** or ***Variables*** (or *attributes*, *states*, *fields*):  contains the *static attributes* of the class.

3. ***Member Functions*** (or *methods*, *behaviors*, *operations*):  contains the *dynamic operations* of the class.

   

In other words, a class encapsulates the static attributes (data) and  dynamic behaviors (operations that operate on the data) in a box.

**Class Members**: The *data members* and *member functions* are collectively called *class members*.

The followings figure shows a few examples of classes:



![OOP_ClassExamples.png](https://www3.ntu.edu.sg/home/ehchua/programming/cpp/images/OOP_ClassExamples.png)



The following figure shows two instances of the class `Student`, identified as "`paul`" and "`peter`".



![OOP_InstanceExamples.png](https://www3.ntu.edu.sg/home/ehchua/programming/cpp/images/OOP_InstanceExamples.png)



**Unified Modeling Language (UML) Class and Instance Diagrams**: The above class diagrams are drawn according to the UML notations. A  class is represented as a 3-compartment box, containing name, data members (variables), and member functions, respectively. Classname is  shown in bold and centralized. An instance (object) is also represented  as a 3-compartment box, with instance name shown as `instanceName:Classname` and underlined.



**Brief Summary**

1. A *class* is a programmer-defined, abstract, self-contained, reusable software entity that mimics a real-world thing.
2. A class is a 3-compartment box containing the name, data members (variables) and the member functions.
3. A class encapsulates the data structures (in data members) and  algorithms (member functions). The values of the data members constitute its *state*. The member functions constitute its *behaviors*.
4. An *instance* is an instantiation (or realization) of a particular item of a class.





##### 2.3 Class Definition

In C++, we use the keyword `class` to define a class.  There are two sections in the class declaration: `private` and `public`, which will be explained later. For example:

```c++
class Circle {         // classname
private:
   double radius;      // Data members (variables)
   string color;
public:   
   double getRadius(); // Member functions
   double getArea();
}
```

```c++
class SoccerPlayer {   // classname
private:
   int number;         // Data members (variables)
   string name;
   int x, y;
public:   
   void run();         // Member functions
   void kickBall();
}
```



**Class Naming Convention**: A classname  shall be a noun or a noun phrase made up of several words. All the words shall be initial-capitalized (camel-case).  Use a *singular* noun for classname.  Choose a meaningful and self-descriptive classname.  For examples, `SoccerPlayer`, `HttpProxyServer`, `FileInputStream`, `PrintStream` and `SocketFactory`.





##### 2.4 Creating Instances of a Class.

To create *an instance of a class*, you have to:

1. Declare an instance identifier (name) of a particular class.
2. Invoke a constructor to construct the instance (i.e., allocate storage for the instance and initialize the variables).



For examples, suppose that we have a class called `Circle`, we can create instances of `Circle` as follows:

```c++
// Construct 3 instances of the class Circle: c1, c2, and c3
Circle c1(1.2, "red");  // radius, color
Circle c2(3.4);         // radius, default color
Circle c3;              // default radius and color
```

Alternatively, you can invoke the constructor explicitly using the following syntax:

```C++
Circle c1 = Circle(1.2, "red");  // radius, color
Circle c2 = Circle(3.4);         // radius, default color
Circle c3 = Circle();            // default radius and color
```



##### 2.5 The Dot (.) Operator.

To reference a *member of a object* (data member or member function), you must:

1. First identify the instance you are interested in, and then
2. Use the *dot operator* (`.`) to reference  the member, in the form of `*instanceName.memberName*`.

For example, suppose that we have a class called `Circle`, with two data members (`radius` and `color`) and two functions (`getRadius()` and `getArea()`). We have created three instances of the class `Circle`, namely, `c1`, `c2` and `c3`. To invoke the function `getArea()`, you must first identity the instance of interest, says `c2`, then use the *dot operator*, in the form of `c2.getArea()`, to invoke the `getArea()` function of instance `c2`.

For example:

```c++
// Declare and construct instances c1 and c2 of the class Circle
Circle c1(1.2, "blue");
Circle c2(3.4, "green");

// Invoke member function via dot operator
cout << c1.getArea() << endl;
cout << c2.getArea() << endl;

// Reference data members via dot operator
c1.radius = 5.5;
c2.radius = 6.6;
```



Calling `getArea()` without identifying the instance is meaningless, as the radius is unknown (there could be many instances of `Circle` - each maintaining its own radius).



##### 2.6 Data Members (Variables).

A *data member* (*variable*) has a *name* (or *identifier*) and a *type*; and holds a *value* of that particular type (as descried in the earlier chapter). A data  member can also be an instance of a certain class (to be discussed  later).

**Data Member Naming Convention**: A data  member name shall be a noun or a noun phrase made up of several words.   The first word is in lowercase and the rest of the words are  initial-capitalized (camel-case), e.g., `fontSize`, `roomNumber`, `xMax`, `yMin` and `xTopLeft`. Take note that variable name begins with an lowercase, while classname begins with an uppercase.





##### 2.7 Member Functions

A member function:

1. receives parameters from the caller,
2. performs the operations defined in the function body, and
3. returns a piece of result (or void) to the caller.

**Member Function Naming Convention:** A  function name shall be a verb, or a verb phrase made up of several  words. The first word is in lowercase and the rest of the words are  initial-capitalized (camel-case).  For example, `getRadius()`, `getParameterValues()`.





##### 2.8 OOP Example

A class called `Circle` is to be defined as illustrated in the class diagram. It contains two data members: `radius` (of type `double`) and `color` (of type `String`); and three member functions: `getRadius()`, `getColor()`, and `getArea()`. Three instances of `Circle`s called `c1`, `c2`, and `c3` shall then be constructed with their respective data members, as shown in the instance diagrams.

![OOP_Circle.png](https://www3.ntu.edu.sg/home/ehchua/programming/java/images/OOP_Circle.png)

```c++
#include <iostream>    // using IO functions
#include <string>      // using string
using namespace std;
 
class Circle {
private:
   double radius;      // Data member (Variable)
   string color;       // Data member (Variable)
 
public:
   // Constructor with default values for data members
   Circle(double r = 1.0, string c = "red") {
      radius = r;
      color = c;
   }
 
   double getRadius() {  // Member function (Getter)
      return radius;
   }
 
   string getColor() {   // Member function (Getter)
      return color;
   }
 
   double getArea() {    // Member function
      return radius*radius*3.1416;
   }
};   // need to end the class declaration with a semi-colon
 
// Test driver function
int main() {
   // Construct a Circle instance
   Circle c1(1.2, "blue");
   cout << "Radius=" << c1.getRadius() << " Area=" << c1.getArea()
        << " Color=" << c1.getColor() << endl;
 
   // Construct another Circle instance
   Circle c2(3.4); // default color
   cout << "Radius=" << c2.getRadius() << " Area=" << c2.getArea()
        << " Color=" << c2.getColor() << endl;
 
   // Construct a Circle instance using default no-arg constructor
   Circle c3;      // default radius and color
   cout << "Radius=" << c3.getRadius() << " Area=" << c3.getArea()
        << " Color=" << c3.getColor() << endl;
   return 0;
}
```





##### 2.9 Constructors.

A *constructor* is a special function that has the *function name* same as the *classname*. In the above `Circle` class, we define a constructor as follows:

```c++
// Constructor has the same name as the class
Circle(double r = 1.0, string c = "red") {
   radius = r;
   color = c;
}
```

A constructor is used to construct and *initialize all the data members*. To create a new instance of a class, you need to declare the name of the instance and invoke the constructor. For example:

```c++
Circle c1(1.2, "blue");
Circle c2(3.4);      // default color
Circle c3;           // default radius and color
                     // Take note that there is no empty bracket ()
```

A constructor function is different from an ordinary function in the following aspects:

- The name of the constructor is the same as the classname.
- Constructor has no return type (or implicitly returns `void`).  Hence, no `return` statement is allowed inside the constructor's body.
- Constructor can only be invoked *once* to initialize the instance constructed. You cannot call the constructor afterwards in your program.
- Constructors are not inherited (to be explained later).





##### 2.10 Default Arguments for Functions.

In C++, you can specify the default value for the trailing arguments of a function (including constructor) in the function header. For example:

```c++
#include <iostream>
using namespace std;
 
// Function prototype
int sum(int n1, int n2, int n3 = 0, int n4 = 0, int n5 = 0);
 
int main() {
   cout << sum(1, 1, 1, 1, 1) << endl; // 5
   cout << sum(1, 1, 1, 1) << endl;    // 4
   cout << sum(1, 1, 1) << endl;       // 3
   cout << sum(1, 1) << endl;          // 2
// cout << sum(1) << endl;  // error: too few arguments
}
 
// Function definition
// The default values shall be specified in function prototype,
//   not the function implementation
int sum(int n1, int n2, int n3, int n4, int n5) {
   return n1 + n2 + n3 + n4 + n5;
}
```





##### 2.11 *public* vs. *private* Access Control Modifiers

An *access control modifier* can be used to control the  visibility of a data member or a member function within a class.  We  begin with the following two access control modifiers:

1. **public**: The member (data or function) is accessible and available to *all* in the system.
2. **private**: The member (data or function) is accessible and available *within this class only*.

For example, in the above `Circle` definition, the data member `radius` is declared `private`. As the result, `radius` is accessible inside the `Circle` class, but NOT outside the` `class. In other words, you cannot use "`c1.radius`" to refer to `c1`'s `radius` in `main()`. On the other hand, the function `getRadius()` is declared `public` in the `Circle` class. Hence, it can be invoked in the `main()`.

**UML Notation**: In UML notation, `public` members are denoted with a "`+`", while `private` members with a "`-`" in the class diagram





##### 2.12 Information Hiding and Encapsulation.

A class encapsulates the static attributes and the dynamic behaviors  into a "3-compartment box".  Once a class is defined, you can seal up  the "box" and put the "box" on the shelve for others to use and reuse.   Anyone can pick up the "box" and use it in their application. 

This follows the principle of *information hiding*.  That is,  objects communicate with each others using well-defined interfaces  (public functions).  Objects are not allowed to know the implementation  details of others.  The implementation details are hidden or  encapsulated within the class.  Information hiding facilitates reuse of  the class.

**Rule of Thumb**: Do not make any data member `public`, unless you have a good reason.





##### 2.13 Getters and Setters

To allow other  to *read* the value of a `private` data member says `xxx`, you shall provide a ***get function*** (or *getter* or *accessor function*) called `getXxx()`.  A getter need not expose the data in raw format.  It can process the  data and limit the view of the data others will see.  Getters shall not  modify the data member.

To allow other classes to *modify* the value of a `private` data member says `xxx`, you shall provide a ***set function*** (or *setter* or *mutator function*) called `setXxx()`.  A setter could provide data validation (such as range checking), and transform the raw data into the internal representation.



For example, in our `Circle` class, the data members `radius` and `color` are declared `private`. That is to say, they are only available within the `Circle` class and not visible outside the `Circle` class - including `main()`.  You cannot access the `private` data members `radius` and `color` from the `main()` directly - via says `c1.radius` or `c1.color`.  The `Circle` class provides two public accessor functions, namely, `getRadius()` and `getColor()`.  These functions are declared `public`.  The `main()` can invoke these public accessor functions to retrieve the `radius` and `color` of a `Circle` object, via says `c1.getRadius()` and `c1.getColor()`.

There is no way you can change the `radius` or `color` of a `Circle` object, after it is constructed in `main()`.  You cannot issue statements such as `c1.radius = 5.0` to change the `radius` of instance `c1`, as `radius` is declared as `private` in the `Circle` class and is not visible to other  including` main()`.If the designer of the `Circle` class permits the change of the `radius` and `color` after a `Circle` object is constructed, he has to provide the appropriate setter, e.g.:

```c++
// Setter for color
void setColor(string c) {
   color = c;
}
   
// Setter for radius
void setRadius(double r) {
   radius = r;
}
```

With proper implementation of *information hiding*, the designer of a class has full control of what the user of the class can and cannot do.





##### 2.14 Keyword *this*

You can use keyword "`this`" to refer to *this* instance inside a class definition.

One of the main usage of keyword `this` is to resolve ambiguity between the names of data member and function parameter. For example:

```c++
class Circle {
private:
   double radius;                 // Member variable called "radius"
   ......
public:
   void setRadius(double radius) { // Function's argument also called "radius"
      this->radius = radius;
         // "this.radius" refers to this instance's member variable
         // "radius" resolved to the function's argument.
   }
   ......
}
```

In the above codes, there are two identifiers called `radius` - a data member and the function parameter. This  causes naming  conflict. To resolve the naming conflict, you could name the function  parameter `r` instead of `radius`. However, `radius` is more approximate and meaningful in this context. You can use keyword `this` to resolve this naming conflict. "`this->radius`" refers to the data member; while "`radius`" resolves to the function parameter.

Alternatively, you could use a prefix (such as `m_`) or suffix (such as `_`) to name the data members to avoid name crashes. For example:

```c++
class Circle {
private:
   double m_radius;  // or radius_
   ......
public:
   void setRadius(double radius) {
      m_radius = radius;  // or radius_ = radius
   }
   ......
}
```

C++ Compiler internally names their data members beginning with a leading underscore (`e.g., _xxx`) and local variables with 2 leading underscores (e.g., `__xxx`). Hence, avoid name beginning with underscore in your program.





##### 2.15 *const* Member Functions.

A `const` member function, identified by a `const` keyword at the end of the member function's header, cannot modifies any data member of this object. For example:

```c++
double getRadius() const {  // const member function
   radius = 0;  
      // error: assignment of data-member 'Circle::radius' in read-only structure
   return radius;
}
```





##### 2.16 Conventions for Getters/ Setters and Constructors.

The constructor, getter and setter functions for a `private` data member called `xxx` of type `T` in a class `Aaa` have the following conventions:

```c++
class Aaa {
private:
   // A private variable named xxx of type T
   T xxx;
public:
   // Constructor
   Aaa(T x) { xxx = x; }
   // OR
   Aaa(T xxx) { this->xxx = xxx; }
   // OR using member initializer list (to be explained later)
   Aaa(T xxx) : xxx(xxx) { }
 
   // A getter for variable xxx of type T receives no argument and return a value of type T
   T getXxx() const { return xxx; }
 
   // A setter for variable xxx of type T receives a parameter of type T and return void
   void setXxx(T x) { xxx = x; }
   // OR
   void setXxx(T xxx) { this->xxx = xxx; }
}
```

For a `bool` variable `xxx`, the getter shall be named `isXxx()`, instead of `getXxx()`, as follows:

```c++
private:
   // Private boolean variable
   bool xxx;
public: 
   // Getter
   bool isXxx() const { return xxx; }
 
   // Setter
   void setXxx(bool x) { xxx = x; }
   // OR
   void setXxx(bool xxx) { this->xxx = xxx; }
```





##### 2.17 Default Constructor.

A default constructor is a constructor with no parameters, or having  default values for all the parameters. For example, the above `Circle`'s constructor can be served as default constructor with all the parameters default.

```c++
Circle c1;   // Declare c1 as an instance of Circle, and invoke the default constructor
Circle c1(); // Error!
             // (This declares c1 as a function that takes no parameter and returns a Circle instance)
```

In C++, if you did not provide ANY constructor, the compiler  automatically provides a default constructor that does nothing. That is:

```c++
ClassName::ClassName() { }  // Take no argument and do nothing
```

Compiler will not provide a default constructor if you define any  constructor(s). If all the constructors you defined require arguments,  invoking no-argument default constructor results in error. This is to  allow class designer to make it impossible to create an *uninitialized* instance, by NOT providing an explicit default constructor.





##### 2.18 Constructor's Member Initializer List.

Instead of initializing the private data members inside the body of the constructor, as follows:

```c++
Circle(double r = 1.0, string c = "red") {
   radius = r;
   color = c;
}
```

We can use an alternate syntax called  *member initializer list* as follows:

```c++
Circle(double r = 1.0, string c = "red") : radius(r), color(c) { }
```

Member initializer list is placed after the constructor's header, separated by a colon (`:`). Each initializer is in the form of `*data_member_name*(*parameter_name*)`. For fundamental type, it is equivalent to `*data_member_name* = *parameter_name*`. For object, the constructor will be invoked to construct the object.  The constructor's body (empty in this case) will be run after the  completion of  member initializer list.

It is recommended to use member initializer list to initialize all the  data members, as it is often more efficient than doing assignment inside the constructor's body.





##### 2.19 *Destructor.

A *destructor*, similar to constructor, is a special function that has the same name as the classname, with a prefix `~`, e.g., `~Circle()`. Destructor is called implicitly when an object is destroyed.

If you do not define a destructor, the compiler provides a default, which does nothing.

```c++
class MyClass {
public:
   // The default destructor that does nothing
   ~MyClass() { }
......
}
```



Advanced Notes:

If your class contains data member which is dynamically allocated (via `new` or `new[]` operator), you need to free the storage via `delete` or `delete[]`.





##### 2.20 *Copy Constructor.

A *copy constructor* constructs a new object by copying an  existing object of the same type. In other words, a copy constructor  takes an argument, which is an object of the same class.

If you do not define a copy constructor, the compiler provides a default which  copies all the data members of the given object. For example,

```c++
Circle c4(7.8, "blue");
cout << "Radius=" << c4.getRadius() << " Area=" << c4.getArea()
     << " Color=" << c4.getColor() << endl;
                // Radius=7.8 Area=191.135 Color=blue
 
// Construct a new object by copying an existing object
// via the so-called default copy constructor
Circle c5(c4);
cout << "Radius=" << c5.getRadius() << " Area=" << c5.getArea()
     << " Color=" << c5.getColor() << endl;
                // Radius=7.8 Area=191.135 Color=blue
```

The copy constructor is particularly important. When an object is passed into a function *by value*, the copy constructor will be used to make a clone copy of the argument.



Advanced notes:

Pass-by-value for object means calling the copy constructor. To avoid  the overhead of creating a clone copy, it is usually better to  pass-by-reference-to-`const`, which will not have side effect on modifying the caller's object.

The copy constructor has the following signature:  

```c++
class MyClass {
private:
   T1 member1;
   T2 member2;
public:
   // The default copy constructor which constructs an object via memberwise copy
   MyClass(const MyClass & rhs) {
      member1 = rhs.member1;
      member2 = rhs.member2;
   }
......
}
```

The default copy constructor performs *shadow copy*. It does not copy the dynamically allocated data members created via `new` or `new[]` operator.





##### 2.21 *Copy Assignment Operator.

The compiler also provides a default assignment operator (`=`), which can be used to assign one object to another object of the same class via memberwise copy. For example, using the `Circle` class defined earlier:

```c++
Circle c6(5.6, "orange"), c7;
cout << "Radius=" << c6.getRadius() << " Area=" << c6.getArea()
     << " Color=" << c6.getColor() << endl;
                // Radius=5.6 Area=98.5206 Color=orange
cout << "Radius=" << c7.getRadius() << " Area=" << c7.getArea()
     << " Color=" << c7.getColor() << endl;
                // Radius=1 Area=3.1416 Color=red (default constructor)
 
c7 = c6; // memberwise copy assignment
cout << "Radius=" << c7.getRadius() << " Area=" << c7.getArea()
     << " Color=" << c7.getColor() << endl;
                // Radius=5.6 Area=98.5206 Color=orange
```



Advanced notes:

- You could overload the assignment operator to override the default.

- The copy constructor, instead of copy assignment operator, is used in declaration:   

- ```c++
  Circle c8 = c6;  // Invoke the copy constructor, NOT copy assignment operator
                   // Same as Circle c8(c6)
  ```

- The default copy assignment operator performs *shadow copy*. It does not copy the dynamically allocated data members created via `new` or `new[]` operator.

- The copy assignment operator has the following signature:  

  ```c++
  class MyClass {
  private:
     T1 member1;
     T2 member2;
  public:
     // The default copy assignment operator which assigns an object via memberwise copy
     MyClass & operator=(const MyClass & rhs) {
        member1 = rhs.member1;
        member2 = rhs.member2;
        return *this;
     }
  ......
  }
  ```

  

- The copy assignment operator differs from the copy constructor in that it must release the dynamically allocated contents of the target  and prevent self assignment. The assignment operator shall return a  reference of this object to allow chaining operation (such as `x = y = z`).
- The default constructor, default destructor, default copy constructor, default copy assignment operators are known as *special member functions*, in which the compiler will automatically generate a copy if they are used in the program and not explicitly defined.





#### 3. Separating Header and Implementation.

For better software engineering, it is recommended that the class  declaration and implementation be kept in 2 separate files: declaration  is a header file "`.h`"; while implementation in a "`.cpp`". This is known as separating the public interface (header declaration)  and the implementation. Interface is defined by the designer,  implementation can be supplied by others. While the interface is fixed,  different vendors can provide different implementations. Furthermore,  only the header files are exposed to the users, the implementation can  be provided in an object file "`.o`" (or in a library). The source code needs not given to the users.

