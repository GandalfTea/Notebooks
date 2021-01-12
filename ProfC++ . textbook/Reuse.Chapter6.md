

##	Designing for REUSE

&nbsp;

#### "Write once, use often"
#### "Avoid code duplication at all cost"

&nbsp;

There are two big parts in code reuse :
* Code Library reuse.
* Writing code for reuse.



&nbsp;

Why to do this :
* Code is rarely used in only one program.
* Saves time and money.
* Other people must use your code.
* Code duplication leads to maintenance nightmare.
* Over time, programers build an extensive library of evolving tools. They do not throw away code.

&nbsp;


### How to design reusable code :

&nbsp;

Most important principle is Abstraction.

Unfortunately, C++ is fundamentally unfrienly to the principle of good abstraction when writing classes. The syntax requires you to combine your public interfaces and non-public (private or protected) data members and methods together in one class definition, thereby exposing some of the internal implementation details of the class to it's clients.

&nbsp;

### How to structure code for optimal reuse :

Your program shoulf be reusable in full, this means that all levels should be reusable separately.

&nbsp;

####  Avoid combining unrelated or logically separated concepts :
	
You should strive for high cohesion. This is also known as Single Responsability Principle (SRP). Don't combine unrelated concepts. 
&nbsp;

&nbsp;

####  Divide your program into logical subsystems :

Subsystems should be discrete components that can be reused separately. Strive for low coupling. 

&nbsp;

####  Use class hierarchies to separate logical concepts :

You chould also avoid combining unrelated concepts at a class level. 

&nbsp;

####  Use AGGREGATION (Has-A relationship) :

Use inheritence when possible and thank use aggregation.

&nbsp;

####  Eliminate user interface dependencies :

If you write a data manipulation library, do not write the user interface in cin or cout or any other user interface.
The Model-View-Controller (MVC) is a good paradigm to separate storing data from visualising data.

&nbsp;

### Templates :

&nbsp;
#### Use templates for generic data structures and alogrithms :

C++ has a concept named templates that allows you to create structures that are generic with respect to a type or class. The porpose of a template is that it is type-free. You specify the type when using it. Templates allow you to write both data structures and algorithms.

The simplest example is the std::vector.

When possible, you should use them and when writing an algorithm or structure, you should make a template.

&nbsp;

### Why templates are best at this :

There are other ways to do what templates do, one example is to store the info as void pointers. This however, is not type-safe in that it does not do any type-checking. 

Another aproach is to write the data structure for a specific class. Through polymorphism, any derrived class can be stored in that structure. This aproach is also not type-safe, as when you remove an object  from the container, you must remember what it really is and down-cast the appropiate type. 

#### NOTE : Down casting means to move something down the hierarchy.

Templates on the other hand, are type-safe. Each instance of a template stores only one type. The program will not compile if not true. 


&nbsp;

### Problems with templates :  

* Wierd sintax
* Only one type per structure
> there is a way around this :
>> write the structure to store std::variant (for specified types) or std::any (for all types) objects.

&nbsp;


### Templates versus inheritence :

Use templates if identical functionality for multiple types.

Use inheritence if you want to provide diferent behaviour for related types.



&nbsp;


## Provide Appropiate Checks and Safeguards :


There are two opposite styles for writing safe code :
&nbsp;


#### 1. Design By Contract :

Witch means that the documentation for a function or a class represents a contract with a detailed description of what the responsability of the client code is and the responsability of your class or function is.

There are three important aspects :
* Preconditions :
	list the conditions that the client code must satisfy before calling a function or method.

* Postconditions :
	list the consitions that must be satisfied by the function or method when it has finished executing.

* Invariants :
	list the conditions that must be satisfied during the whole execution of the function or method.

&nbsp;


#### 2. Design as safe as possible :

The second style is that you design your function and classes as safely as possible. Preform error checking, 

&nbsp;

### The Open/Closed Principle

You should strive to design your classes in such a way that you can later one derrive another class and extend usability. They should be derrivable without modification.

Example :

Suppose you want to create a drawing programe, The first version should support only squares. Your design must contain two classes : Square and Renderer. The former contains the definition of your square and the latter is responsible for drawing :
```
  class Square
  {
  	//Details
  }
```
```
  class Renderer
  {
  	public:  
	   void render(const vector<Square>& squares);
  }
```
```
  void Renderer::render(const vector<Square>& squares)
  {
	for (auto& square : squares) {
		//Render the square object
	}
  }
```
Now, if you want to add support for circles, you create a Circle class :
```
  class Circle
  {
  	//Details
  }
```


You decide to change the render class as follows :
```
  void Renderer::render(const vector<Square>& squares,
			const vector<Circle>& circles)
	for (auto& square : squares) {
		//Render
	}
	for (auto& circle : circles) {
		//Render
	}
```


Doing so, the render method is not closed for modification.
In this case, the design should be inheritance :
```
 class Square : public Shape{};
```
```
class Shape
  {
	public:
	   virtual void render() = 0;
  };
```
```
 class square : public Shape
  {
	public:
	   virtual void render() override { /* render square */}
		//Other members
  };
```
```
 class Circle : public Shape
  {
	public:
	   virtual void render() override { /* render circle */}
		//Other members
  };
```
```
 class Renderer 
  {
	public:
	   void render(const vector<shared_ptr<Shape>>& objects);
  };

  void Renderer::render(const vector<shared_ptr<Shape>>& objects)
  {
	for(auto& object : objects)
	{
		object->render();
	}
  }
```


This code is open  for extension and closed to modification.



&nbsp;


## Design Usable Interfaces :

&nbsp;

#### Follow Familiar Ways :

C++ offers you a feature called operator overloading . This allows you to write classessuch that the standard operators work on them just as they work on built in types like int :
```
  Fraction f1(3, 4);
  Fraction f2(1, 2);
  Fraction sum = f1 + f2;
  Fraction diff = f1 - f2;
  cout << f1 << " " << f2 << endl;
```
Contrast that with without :
```
  Fraction f1(3, 4);
  Fraction f2(1, 2);
  Fraction sum = f1.add(f2);
  Fraction diff = f1.subtract(f2);
  f1.print(cout);
  cout << " ";
  f2.print(cout);
  cout << endl;
```
This offers an easier interface.


&nbsp;

####  Dont't omit required functionality :

Include all behaviours that the clients could need. Include as much functionality in the implementation as possible. Don't require client code to specify information that you already know.

&nbsp;

####  Present uncluttered interfaces :

If you include a lot of functionality, the interfaces might become very cluttered. Don't provide unnecesarry functionality, keep it simple and cleen

&nbsp;

####  Provide documentation in the comments :

There are two ways to do this : comments in the interfaces themselves and external documentation. You should strive to provide both. The comments should describe the behaviour not the implementation. This should include inputs, outputs, error conditions and handeling, intended use and performance guarantees.

&nbsp;

## Design General-Purpose Interfaces :

The interface should be adaptable to different tasks.
&nbsp;

####  Provide multiple ways to perform the same functionality :

Keep in mind that this can lead to cluttered interfaces. This should be an exception when needed.

&nbsp;

####  Provide customizability :

For example, you could allow clients to set their own error handeling routines.


#### Reconciling Generality and Ease of Use :

1. Supply multiple interfaces :
	This is called the interfacee separation principle (ISP)
2. Make functionality wasy to use.


&nbsp;


### The SOLID Principles :
```
S - Single Responsibility Principle (SRP).
O - Open/Closed Principle (OCP).
L - Liskov Substitution Principle (LSP).
I - Interface Segregation Principle (ISP).
D - Dependency Inversion Principle (DIP).
```
