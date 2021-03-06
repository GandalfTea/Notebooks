

## Discovering Inheritance Techniques

&nbsp;

TOC
1. [Building Classes with Inheritance](#building_with_inheritance)
	* [Overriding Methods](#overriding_methods)
	* [The `override` keyward](#override)
	* [The `virtual` keyward](#virtual)
	* [Implementation of _virtual_](#virtual_implementation)
	* [The Need for `virtual` Destructors](#virtual_destructors)
	* [Preventing Overriding](#preventing_overriding)
2. [Inheritance for Reuse](#inheritance_reuse)
	* [Respect Your Parents](#respect_parents)
	* [Casting Up and Down](#casting)
3. [Inheritance for Polymorphism](#inheritance_polymorphism)
4. [Multiple Inheritance](#multiple_inheritance)
	* [Naming Collisions and Ambiguous Case Classes](#name_ambiguous)
6. [Interesting and Obscure Inheritance Issues](#obscure_issues)
7. [Special Cases in Overriding Methods](#special_cases)
8. [Copy Constructors and Assignment Operators in Derived Classes](#copy_assignment)
9. [Run-Time Type Facilities](#run_time)
10. [Non-`public` Inheritance](#non_public)
11. [Virtual Base Classes](#virtual_base)

&nbsp;

### Building Classes with Inheritance <a name="building_with_inheritance"></a>


In earlier chapters we talked about the _is-a_ relationship between classes. It is relevant when you want to build a class that builds on another class. One way to accomplish this is to copy the code from one class to another. This is very annoying. 
&nbsp;

#### Extending Classes

When defining a class, you can tell the compiler that it is _inheriting_ from another class, witch is called _the parent class_ or _base class_. Extending an existing class gives the new class (which is now a _derived clas_ or _sub-class_) the ability to describe only the way in witch it is different from the parent class. 

```c++
class Base
{
	public:
		void someMethod();
	protected:
		int mProtectedInt;
	private:
		int mPrivateInt;
};
```

Deriving a class:

```c++
class Derived : public Base
{
	public:
		void someOtherMethod();
};
```

`Derived` is a full class that inherits everything from the `Base` class.

&nbsp;

#### A Client's View on Inheritance


To a client or any other part of your code, an objct of type `Derived` is also an object of type `Base` because of the inheritance. This means that the `public` methods of both are available for use :

```c++
Derived myDerived;
myDerived.someMethod();
myDerived.someOtherMethod();
```

This is only in one direction, a `Base` object will not have any knowledge or access to `Derived` :

```c++
Base myBase;
myBase.someOtherMethod();	// Error : Base does not have a someOtherMethod().
```

A pointer or reference towards a `Base` object can also point to a `Derived` object. This is explained later. It is important to know that this works :

```c++
Base* base = new Derived();
```

However, you can't call any `Derived` methods :

```c++
base->someOtherMethod();	// Error.
```
&nbsp;

#### A Derived Class's View of Inheritance

It is very similar to a normal class. You can use `private` and `protected` data members and methods like it were it's own. In this case, the `Deriverd` class augments the `Base` Class by adding a new method named `someOtherMethod()`. You can define this method ny using data members from `Base`, :

```c++
void Derived::someOtherMethods() {
	cout << "I can access base class data members" << endl;
	cout << "Its value is " << mProtectedInt << endl;
}
```


In fact, the difference between `private` and `protected` in a class is that `protected` members are available to derrived classes while `pivate` are not :

```c++
void Derived::someOtherMethos() {
	cout << mPrivateInt << endl;	// Error
}
```
&nbsp;

#### Preventing Inheritance

C++ allows you to mark a class as `final`. This means no class can inherit from it. 
```c++
class Base final
{
	// . . .
};
```
This is no longer possible :
```c++
class Derived : public Base
{
	// . . .
};
```
&nbsp;

### Overriding Methods <a name="overriding_methods"></a>

Till now, `Derived` only added upon what `Base` already had. You are also able to modify the behaviour of methods that are inherited. 

The catch is that only methods marked with the keyward `virtual` in the base class can pe properlt overridden :

```c++
class Base
{
	public:
		virtual void someMethod();
	// . . .
};
```

While the `virtual` keyward does have some subtleties, a good rule of thumb is to go ahead and make all the methods `virtual`. The only drawback is a very tiny performance hit. The same holds true for the `Derived` class, all methods should be marked `virtual` :

```c++
class Derived : public Base
{
	public:
		virtual void someOtherMethod();
}:
```

#### Note : Constructors should not be made virtual, but destructors are ok. Compiler generated destructors are not `virtual`.

&nbsp;

#### Syntax for Overriding a Method

You redeclare it exactly as in the base class and add the `override` keyward. Then you go ahead and provide the implementation.

For example, let's override the `someMethod()` method. The original definition is in the `Base.cpp` file :

```c++
voic Base::someMethod() {
	cout << "This is the original" << endl;
}
```

Note that you do not need to repeat the `virtual` keyward.

Firstly you must declare it in the class definition :

```c++
class Derived : public Base
{
	public:
		virtual void someMethod() override;
		virtual void someOtherMethod();
};
```


The implementation is specified in the `Derived.cpp` file :

```c++
void Derived::someMethod() {
	cout << "This is the overriden method." << endl;
}
```

Once a method or destructor is marked as `virtual`, it is virtual for all derived classes even if the keyward is not present. 

&nbsp;

### A Client's View of Overridden Methods


All the code works just like before, but now the same function called on different objects does different things :

```c++
Base myBase;
Derived myDerived;

myBase.someMethod();
myDerrived.someMethod();
```
Output:
```
This is the original.
This is the overriden method.
```

In the case of pointers and references, if you have a `Base` refference that refers to an object that is really `Derived`, calling `someMethod()` will return the one from the `Derived` class:

```c++
Derived myDerived;
Base& ref = myDerived;

ref.someMethod();	// Calls the Derived version.
```

Still, you cannot access methods and members that are not also present in the `Base` class :

```c++
Derived myDerived;
Base& ref = myDerived;

myDerived.someOtherMethod();	// Works
ref.someOtherMethod();		// Error
```

You can also cast or assign a `Derived` object to a `Base` because a `Derived` is a `Base`. However, the object loses any knowledge of the `Derived` class at that point :

```c++
Derived myDerived;

Base assignmentObject = myDerived;	// Assign a Derived to a Base
assignmentObject.someMethod();		// Calls the Base's version
```
&nbsp;

### The _override_ Keyward <a name="override"></a>

It is easy to accidently create a new method rather then overriding. 

The following example overrides without the override keyward :

```c++
class Base
{
	public:
		virtual void someMethod(double d);
};
```
```c++
class Derived : public Base
{
	public:
		virtual void someMethod(double d);
};
```

This is a correct overriding of the function.
Now, suppose you accidentally use an `int` parameter instead of a `double` :

```c++
class Derived : public Base
{
	public:
		void virtual someMethod(int i);
};
```
This code does not override `someMethod()` but instead creates a new virtual method. If you try to call it throw a reference, the method of the `Base` class is called :

```c++
Derived myDerived;

Base& ref = myDerived;
ref.someMehod(1.1);	// Base version;
```

This type of problem can occur when you start updating the base class but forget to update all inherited classe.

You can prevent this using the `override` keyward. :

```c++
class Derived : public Base
{
	public:
		virtual void someMethod(int i) override;
};
```

This generates a compiler error, to attract your attention that the function is not the same as in the base class.

#### Note : Always use the `override` keyward as a way to secure against this sort of problems. 

&nbsp;

### The _virtual_ Keyward <a name="virtual"></a>

If a method is not `virtual`, you can still override it, but it will be wrong in subtle ways. 


#### Hiding instead of Overriding

Overriding without `virtual` :

```c++
class Base
{
	public:
		void go() {cout << "go() in Base" << endl; }
};
```
```c++
class Derived : public Base
{
	public:
		void go() { cout << "go() in Derived" << endl; }
};
```

Attempting to call `go()` on `Derived` appears to work initially:

```c++
Derived myDerived;
myDerived.go();
```

Output :
```
go() on Derived
```

However, because the method is not `virtual` it is not actually overridden. The `Derived` class created a new method, also called `go()`, that is competely unrelated to the `Base`'s `go()`. To prove this, call the method in the contex of a `Base` pointer :

```c++
Derived myDerived;
Base& ref = myDerived;
ref.go();
```
Output:
```
go() called in Base
```

This is because the `virtual` keyward was omitted. 

#### Note : Attempting to override a non-`virtual` function will hide the base class definition and will create a new method.

&nbsp;

### How `virtual` is Implemented <a name="virtual_implementation"></a>

When a class is compiled in C++, a binary object is created that contains all methods of the class. 

In the case of non-`virtual` methods, the code to transfer contol is hard-coded directly where the method is called based on the compile-time type. This is called ___static binding___, also known as _early binding_.

If the method is `virtual`, the corect implementation is called through the use of a special area of memoty called the ___vtable___ or _virtual table_. Each class that has one or more `virtual` methods has a vtable, and each object of such a class contains a pointer to that table. This _vtable_ contains pointers to the implementations of the `virtual` methods. When a method call is made, the pointer is followed into the vtable and to the appropiate version of the method is called based on the type of the object at run time. This is called ___dynamic binding___ , also known as _late binding_.

As an example :

```c++
class Base
{
	public:
		virtual void func1() {}
		virtual void func2() {}
		void nonVirtualFunc() {}
};
```
```c++
class Derived : public Base
{
	public:
		virtual void func2() override {}
		void nonVirtualFunc() {}
};
```

Instances :
```c++
Base myBase;
Derived myDerived;
```

The `myBase` object contains a pointer to it's _vtable_. The vtable has 2 entries: `func1()` and `func2()`. Those entries point to the implementations of `Base::func1()` and `Base::func2()`.


`myDerived` also contains a pointer to it's vtable, witch also has 2 entries: `func1()` and `func2()`. `func1()` points to `Base::func1()` implementation because it is not overridden. `func2()` entry points to `Derived::func2()`.


```
myBase	
_________ 	         vtable   

vtable pointer ------->   func1 --------> Base::func1() Implementation
	   	          func2 -----/ -> Base::func2() Implementation
				    /
				   /
myDerived			  /
_________		vtable	 /
				/
vtable pointer -------> func1()/
			func2() --------> Derived::func2() Implementation
```

#### Note : None of the vtables contain the `nonVirtualFunc()` because it is not `virtual`.

&nbsp;

### The Justification for `virtual`


Because it is advised to make all functions `virtual`, why should the compiler not do this automatically? Many people believe it should, the Java language does this. 

The reason it was created is to do with the overhead of the vtable. To call a vtable, the program needs to perform an extra operation by dereferencing the pointer to the apropiate code to execute. This is a miniscule performance hit, but the designers thought it was better if the programmer decides this. 

In some rare cases, the preformance hit might be too costly, and you might need to avoid the hit.

&nbsp;

### The Need for `virtual` Destructors <a name="virtual_destructors"></a>


Even if you decide not to adopt the guideline to make all methods `virtual`, you still need to apply it to the destructor. This is because if the destructor is not `virtual`, it can easily result in a situation where memory is not freed. Only for a class marked as final is it ok to have a non-`virtual` destructor.


The following code shows how easy it is to trick the compiler into not calling a destructor if it is non-`virtual` :


```c++
class Base
{
	public:
		Base() {}
		~Base() {}
};
```
```c++
class Derived : public Base
{
	public:
		Derived() {
			mString = new char[30];
			cout << "mString allocated" << endl;
		}

		~Derived() {
			delete[] mString;
			cout << "mString dealocated" << endl;
		}

	private:
		char* mString;
};
```
```c++
int main() {
	Base* ptr = new Derived();	//mString is allocated here.
	delete ptr; //~Base is called, but not ~Derived because it is not virtual.
	return 0;
}
```

Output:
```
mString allocated
```


#### Note : If you do not want to use a destructor, but only make it virtual, you can default it :

```c++
class Base
{
	public:
		virtual ~Base() = default;
}
```
### Note : Constructors cannot and need not be virtual because you always specify the exact class being constructed.

&nbsp;

### Preventing Overriding <a name="preventing_overriding"></a>


C++ allows you to mark a method as `final`, like a class, witch means it cannot be overriden in a derived class. Trying will result in a compiler error.

```c++
class Base
{
	public:
		virtual ~Base() = default;
		virtual void someMethod() final;
};
```
&nbsp;

## Inheritance for reuse <a name="inheritance_reuse"></a>

### The WeatherPrediction Class


Imagine you were given the task of writing a weather prediction program. It is not in your field of expertise, but you find a library online. It is distributed as a compiled library to protect the intellectual property, but you can see the class definition.

```c++
class WeatherPrediction
{
	public:
		virtual ~WeatherPrediction();
		virtual void setCurrentTempFahrenheit(int temp);
		virtual void setPositionOfJupiter(int distanceFromMars);
		virtual int getTomorrowTempFahrenheit() const;
		virtual double getChanceOfRain() const;
		virtual void showResult() const;
		virtual std::string getTemperature() const;
	private:
		int mCurrentTempFahrenheit;
		int mDistanceFromMars;
};
```

Note the use of `virtual`, because the class presumes that the methods might be overridden in a derived class.


As always, the library is helpful but it is not perfect for our needs.

&nbsp;

### Adding functionality in a Deried Class

You need to make a few changes to the `WeatherPrediction` class. To begin, define a new class, `MyWeatherPrediction` that inherits from `WeatherPrediction`:

```c++
#include "WeatherPrediction.h"

class MyWeatherPrediction : public WeatherPredictoin
{
};
```

As a first edit, you want to add support for the Celsius scale. 
This might be a little wiers, because you do not know anything about the internal workings of the class. What if all the internal calculations are made using Fahrenheit?

One way  to do it is to use the derived class to act as a go-between, interfacing between the user, who can use either scale, and the base class, witch only uses Fahrenheit.


The first step is to add new methods that allow clients to set the current temperature to Celsius instead of Fahrenheit and also get tomorrow's prediction. You also need private helper functions that convert between Celsius and Fahrenheit in both directions. These methods can be `static` because they are the same for all instances of the class.

```c++
#include "WeatherPrediction.h"

class MyWeatherPrediction : public WeatherPrediction
{
	public:
		virtual void setCurrentTempCelsius(int temp);
		virtual int getTomorrowTempCelsius() const;
	private:
		static int convertCelsiusToFahrenheit(int celsius);
		static int convertFahrenheitToCelsius(int fahrenheit);
};
```

Because you add functionality to another class, it is recomanded you keep all the naming conventions.

The implementation for the transformation function is streightforward. The other two are more interesting. To set the current temperature in celsius, you need to convert the temperature first and then present it to the parent class in unit it understands :

```c++
void MyWeatherPrediction::setCurrentTempCelsius(int temp) {
	int fahrenheitTemp = convertCelsiusToFahrenheit(temp);
	setCurrentTempFahreneit(fahrenheitTemp);
}
```

The same is true for the `getTomorrowTempCelsius` :

```c++
int MyweatherPrediction::getTomorrowTempCelsius() const {
	int fahrenheitTemp = getTomorrowTempFahrenheit();
	return convertFahrenheitToCelsius(fahrenheitTemp);
}
```
&nbsp;

### Replacing functionality in a Derived Class


We need to also replace the `showResult()` method.

```c++
class MyWeatherPrediction : public WeatherPrediction
{
	public:
		virtual void setCurrentTempCelsius(int temp);
		virtual int getTomorrowTempCelsius() const;
		virtual void showResult() const override;
	private:
		static int convertCelsiusToFahrenheit(int celsius);
		static int convertFahrenheitToCelsius(int fahrenheit);
};
```

Here is a possible new implementation :

```c++
void MyWeatherPrediction::showResult() const {
	cout << "Tomorrow's temperature will be" <<
		getTomorrowTempCelsius() << " degrees Celsius (" <<
		getTomorrowTempFahrenheit() << "degrees Fahrenheit" << endl;
	
	cout << "Chance of rain is " << (getChanceOfRain() * 100) << " percent" << endl;
	
	if (getChanceOfRain() > 0.5) {
		cout << "Bring an umbrella!" << endl;
	}
}
```
&nbsp;

### Respect Your Parents <a name="respect_parents"></a>

When writing a derived class, you need to be aware of the interaction between the parent class and the child classes. Issues such as order of creatin, constructor chaining and casting are all potential sources of bugs.
&nbsp;

### Parent Constructors

The creation order of object is as follows :

   * If there is a base class, the constructor of the base class is executed. (unless there is a call to the base class constructor in the ctor-initializer)
   * Non-`static` data members are constructed in the order that they apear.
   * The self constructor is called.

Those rules can spread, for example, if the class has a grandparent, the grandparent is constructed before the parent. As an example:

```c++
class Something
{
	public:
		Something() { cout << "2"; }
;}
```
```c++
class Base
{
	public:
		Base() { cout << "1"; }
};
```
```c++
class Derived : public Base
{
	public:
		Derived() { cout << "3"; }
	private:
		Something mDataMember;
};
```
```c++
int main() {
	Derived myDerived;
	return 0;
}
```

The output is :
```
123
```

When the `myDerived` object is created, the constructor for `Base` is called first, outputting the string `1`. Next, the `mDataMember` is initialized, calling the `Something` constructor, witch outputs `2`. Finally the `Derived` constructor is called, which outputs `3`.

If you want to choose which constructor is called from the `Base` class, you must chain the constructor just as when initializing data members :

```c++
class Base
{
	public:
		Base(int i);
};
```
```c++
class Derived : public Base
{
	public:
		Derived();
};
```
```c++
Derived::Derived() : Base(7) {
	// . . .
}
```

Derived could also pass a variable if the constructor asked for it:
```
Derived::Derived(int i) : Base(i) {}
```

Passing constructor arguments from one class to another is normal, passing data members however, will not work. The code will compile, but data members will be initialized only _after_ the constructor is finished. 

&nbsp;

### Parent Destructors

The order of destruction is the reverse of the construction :

   * The main object is destroyed
   * Any data members in the class are destroyed, in the reverse order of their construction.
   * The parent class is destroyed. 

#### Note : Remember to make all the destructors virtual!
&nbsp;

### Refering to Parent Names

When overriding a method, the code for it's implementation is replaces. However, you might still want to make use of the original one.

As an example, let's override the `getTemperature` method :

```c++
class MyWeatherPrediction : public WeatherPrediction
{
	public:
		virtual std::string getTemperature() const override;
		// . . .
};
```

Suppose you want to add a Celsius circle at the end, you might do this :

```c++
string MyWeatherPrediction::getTemperature() const {
	return getTemperature() + "\u00B0F";	// BUG
}
```

This does not work because, under the rules of name resolution for C++, it first resolves against the local scope, then the class scope. In consequence, it ends up calling `MyWeatherPrediction::getTemperature()`. This results in an infinite recursion.

To make this work, you need to use the scope resolution operator :

```c++
string MyWeatherPrediction::getTemperature() const {
	return WeatherPrediction::getTemperature() + "\u00B0F";
}
```
&nbsp;

#### The Book Clasification Example :

Imagine a clas hierarchy of book types. Because in the lower levels, the class specifies the type of book, a method that gets the description of the book needs to take all levels into consideration :

```c++
class Book
{
	public:
		virtual ~Book() = default;
		virtual string getDescription() const { return "Book"; }
		virtual int getHeight() const { return 120; }
};
```
```c++
class Paperback : public Book
{
	public:
		virtual string getDescription() const override {
			return "Paperback" + Book::getDescription();
		}
};
```
```c++
class Romance : public Paperback
{
	public:
		virtual string getDescription() const override {
			return "Romance" + Paperback::getDescription();
		}

		virtual int getHeight() const override {
			return Paperback::getHeight() / 2;
		}
};
```
```c++
class Technical : public Book
{
	public:
		virtual string getDescription() const override {
			return "Technical" + Book::getDescription();
		}
};
```
```c++
int main() {
	
	Romance novel;
	Book book;
	
	cout << novel.getDescription() << endl;	// Output "Romance Paperback Book"
	cout << book.getDescription() << endl;	// Outputs "Book"
	cout << novel.getHeight() << endl;	// Outputs "60"
	cout << book.getHeight() << endl;	// Outputs "120"
	
	return 0
}
```

&nbsp;

### Casting Up and Down <a name="casting"></a>

As you have seen, an object can be cast or assigned to its parent class. If it is performed on a plain old object, the result is slicing :
```c++
Base myBase = myDerived;
```

However, slicing does not occur if the object is assigned to a pointer or reference of its base class:

```c++
Base& myBase = myDerived;	// No slicing
```


This is called ___upcasting___.  It is a good ideea to make your methods and functions take references to classes instead of using direct objects. By using reference, it can be passed without slicing.

Casting from a base class to one if it's derived classes, also called ___downcasting___, is often frowned upon by professional C++ programmers because there is no guarantee that the object really belongs to that derived class, and downcasting is a sign of bad design. 


For example :
```c++
void presumptuous( Base* base) {
	Derived* myDerived = static_cast<Derived*>(base);
}
```

If the programmer calls `presumptuous()` they might pass in a `base*`. There are no compile-time checks that can be done. The function blindly assumes that `base` is actually a pointer to a `Derived`.

Downcasting is sometimes necesarry. If you have to do it, you should use a `dynamic_cast()` which refuses a cast that doesn't make sense. This info resides on the vtable, therefore `dynamic_cast()` works only for objects with a vtable. A rewrite of the previous example :

```c++
void lessPresumptuous(Base* base) {
	Derived* myDerived = dynamic_cast<Derived*>(base);
	if (myDerived != nullptr) {
		// . . .
	}
}
```

#### Note : If you have to use downcasting, you should modify your design. 


&nbsp;

## Inheritance For Polymorphism <a name="inheritance_polymorphism"></a>

### Designing the Polymorphic Spreadsheet Cell



In wanting to transform the Spreadsheet class to a polymorphic design, you might think to make a `StringSpreadsheetCell` class and then derive other types from it. You would quickly find that you need to override most of the methods. Instead, you should take the commonality of all those variants and create a Base SpreadsheetCell clas from witch to derive all subsequent types.

#### The SpreadsheetCell Base Class :

The similarity between the subsequent derived classes is that they all hold a single piece of data, albeit different type.
Al cells should be able to set and get their value back as a string.

```c++
class Spreadsheet Cell
{
	public:
		virtual ~SpreadsheetCell() = default;
		virtual void set(std::string_view inString);
		virtual std::string getString() const;
};
```
&nbsp;


#### Pure Virtual Methods and Abstract Base Classes


_Pure Virtual Methods_ are methods that are explicitly undefined in the base class. The compiler enforces that if a class contains one of those, it will not be able to create an objefct from it. It is only for inheritance.

```c++
class SpreadsheetCell
{
	public:
		virtual ~SpreadsheetCell() = default;
		virtual void set(sed::string_view inString) = 0;
		virtual std::string getString() const = 0;
};
```
&nbsp;

#### The Individual Derived Classes

If a derived class does not implement all the virtual methods from the parent, it is abstract as well.
&nbsp;

#### String

```c++
class StringSpreadsheetCell : public SpreadsheetCell
{
	public:
		virtual void set(std::string_view inString) override;
		virtual std::string getString() const override;
		
	private:
		std::optional<std::string> mValue;
};
```

The `std::optional` type is discussed in later chapters. With it it is possible to distinguish weather a value for a cell has been set or not.

Implementation :

```c++
void StringSpreadsheetCell::set(string_view inString) {
	mValue = inString;
}
```
```c++
string StringSpreadsheetCell::getString() const {
	return mValue.value_or("");
}
```
The `getString` has to keep in mind that `mValue` is an `std::optional` and might not have a value. By using `mValue.value_or("")`, the value is returned if it exists. Otherwise an empty string is returned.
&nbsp;

#### Double


```c++
class DoubleSpreadsheetCell : public SpreadsheetCell
{
	public:
		virtual void set(double inDouble);
		virtual void set(std::string_view inString) override;
		virtual std::string getString() const override;

	private:
		static std::string doubleToString(double inValue);
		static double stringToDouble(std::string_view inValue);
		std::optional<double> mValue;
};
```

Implementation:

```c++
void DoubleSpreadsheetCell::set(double inDouble) {
	mValue = inDouble;
}
```
```c++
void DoubleSpreadsheetCell::set(string_view inString) {
	mValue = stringToDouble(inString);
}
```
```c++
string DoubleSpreadsheetCell::getString() const {
	return (mValue.hasvalue() ? doubleToString(mValue.value()) : "");
}
```
&nbsp;

### Leveraging Polymorphism



```c++
vector<unique_ptr<SpreadsheetCell>> cellArray
```

```c++
cellArray.push_back(make_unique<StringSpreadsheetCell>());
cellArray.push_back(make_unique<StringSpreadsheetCell>());
cellArray.push_back(make_unique<DoubleSpreadsheetCell>());
```


```c++
cellArray[0]->set("hello");
cellArray[1]->set("10");
cellArray[2]->set("18");
```
```c++
cout << "Vector values are [" << cellArray[0]->getString() << "," <<
			      	 cellArray[1]->getString() << "," <<
				 cellArray[2]->getString() << "," <<
			         endl;
```

&nbsp;

#### Future Considerations

The design is a lot better, but still not up to par with a real spreadsheet program.

One example of missing functionality is the ability to convert from one cell type o another. This could be done using a _converting constructor_, also known as a _typed constructor_.

```c++
class StringSpreadsheetCell : public SpreadsheetCell
{
	public:
	StringSpreadsheetCell() = default;
	StringSpreadsheetCell(const DoubleSpreadsheetCell& inDoubltCell);
	// . . .
};
```
Implementation:

```c++
StringSpreadsheetCell::StringSpreadsheetCell (
	const DoubleSpreadsheetCell& inDoubleCell) {
	mValue = inDoubleCell.getString();
}
```

Secondly, there are several solutions for implementing overloaded operators for cells. One aproach is to implement one for each combination of cells. Another aproach is to decide a common representation. The proceding implementations already decided on string being the common representation. 

```c++
StringSpreadsheetCell operator+(const StringSpreadsheetCell& lhs,
				const StringSpreadsheetCell& rhs)
{
	StringSpreadsheetCell newCell;
	newCell.set(lhs.getString() + rhs.getString());
	return newCell;
}
```
As long as the compiler can turn any cell into a `StringSpreadsheetCell`, this will work. 

```c++
DoubleSpreadsheetCell myDbl;
myDbl.set(8.4);
StringSpreadsheetCell result = myDbl + myDbl;
```
This works. But it returns `8.4000008.400000`.

&nbsp;

## Multiple Inheritance <a name="multiple_inheritance"></a>

The syntax is simple :
```c++
class Baz : public Foo, public Bar
```

The `Baz` objects have the public and protected methods of both `Foo` and `Bar`. A `Baz` object can be upcast to either of them. Creating a `Bar` object calls the parent constructors in the order of the class definition, and vice versa with destructors.
&nbsp;

### Naming Collisions and Ambiguous Base Classes <a name="name_ambiguous"></a>

#### Name Ambiguity
What if both parents had a method with the same name? Because they are not related, they do not override.  The compiler will complain if you try to call it, but it will compile if there is no call.

To solve this, you can either upcast it using `dynamic_cast()` or use a `disembiguation syntax`

```c++
dynamic_cast<Dog&>(myConfusedAnimal).eat();
myConfusedAnimal.Dog::eat();
```
&nbsp;

#### Ambiguous Base Classes

Another way to provoke ambiguity is to inherit the same class twice. 
```c++
class Dog {};
class Bird : public Dog {};
class DogBird : public Bird, public Dog {}; //Error!
```

Another example which is permitted would be to have a common parent `Animal` class. Then inheriting from both `Dog` and `Bord` would be permitted, but naming ambiguity can still occur. This is refered to as a _diamond-shaped_ class hierarchy structore. The best way to do it is to have the top class be an abstract base class with all methods virtual. Thus there would be no naming issues.

&nbsp;

## Interesting and Obscure Inheritance Issues <a name="obscure_issues"></a>

### Changing te Overriden Method's Characteristics

#### Changing the return type

A good rule of thumb is to override a method with the exact same declaration. However, in C++ you can change the return type of a overriden function as long as the return type is a reference or pointer to a clss, and the new return type is a reference or pointer to a descendent class. 

A good way to figure out weather you can change the return type of an overriden method is to consider weather existing code would still work. 

You can not, however, change the return type to `void`


&nbsp;

#### Changing the Method Parameters

If you use the same name of a virtual function but different parameters, it is not overriten but a new method is created. If you do this, the original method will be hidden. 

If you want to keep the original, you can do this :

```c++
class Base:
{
	public:
		virtual void someMethod();
};
```
```c++
class Derived : public Base
{
	public:
		using Base::someMethod;	// Inherits 
		virtual void someMethod(int i);  //Creates a new one
};
```
&nbsp;

### Inherited Constructors

In the previous example, you saw how using `using` can force inherit from the base class. You can do the same with constructors. 

```c++
class Base
{
	public:
		virtual ~Base() = default;
		Base() = default;
		Base(std::string_view str);
};
```
```c++
class Derived : public Base
{
	public:
		Derived(int i);
};
```

```c++
Base base("Hello");	// OK, calls string_view Bas ctor
Derived derived1(1);	// OK, cals int Derived ctor
Derived derived2("Hello");	// Error, Derived does not inherit string_view ctor.
```

You can explicitly inherit the ctor:

```c++
cass Derived : public Base
{
	public:
		using Base::Base;
		Derived(int i);
};
```

This inherits all but the default constructor. You cannot inherit only one.

```c++
Derived derived1(1);	  //OK
Derived derived2("Hello") //OK
```

If there is already a constructor with the same argument for the `Derived` class, it takes precedence over the inherited. You also cannot inherit constructors with the same arguments from 2 different classes.

&nbsp;

### Special Cases in Overriding Methods <a name="special_cases"></a>

#### The Base Class Method is `static`

In C++, you cannot override a `static` method. A method cannot be both 	`static` and `virtual`. 

If you inherit a static method with the same name as a local static method, you have 2 separate methods.

```c++
class BaseStatic
{
	public:
		static void beStatic();
};
```
```c++
class DerivedStatic : public BaseStatic
{
	public:
		static void beStatic();
};
```
Those are two different methods.

#### Note : static methods are scoped by the name of the class in which they are defined, they are not methods that apply to a specific object. When you call a static method, the version determined by normal name resolution is called. When the method is called by an object, the object is not actually involved in the call.


&nbsp;

#### The Base Class Method is Overloaded


If you override a method inherited from a bace class, the compiler implicitly hides all other instances of the name on the base class. 

```c++
class Base
{
	public:
		virtual ~Base() = default;
		virtual void overload() {};
		virtual void overload(int i) {};
};
```
```c++
class Derived : public Base
{
	public:
		virtual void overload() overide;
};
```
If you attempt to call the version that takes an int as argument, your code will not compile. It is possible to access the method from a `Derived` object by casting it into a `Base` pointer or reference. You can also use `using`. 


#### Note : To avoid obscure bugs, you should override all versions of an overloaded method or use the using keyward. 

&nbsp;

### The Base Class Method is `private` or `protected`

There is nothing special with overloadin a `private` or `protected` method. Just because a derived class can't call the `private` methods doesn't mean it can't override them. 

For example :

```c++
class MilesEstimator
{
	public:
		virtual ~MilesEstimator() = default;
		virtual int getMilesLeft() const;
		virtual void setGallonsLeft(int gallons);
		virtual int getGallonsLeft() const;

	private:
		int mGallonsLeft;
		virtual int getMilesPerGallon() const;
};
```

Implementation :

```c++
int MilesEstimator::getMilesLeft() const {
	return getMilesPerGallon() * getGallonsLeft();
}
```
```c++
void MilesEstimator::setGallonsLeft(int gallons) {
	mGallonsLeft = gallons;
}
```
```c++
int MilesEstimator::getGallonsLeft() const {
	return mGallonsLeft;
}
```
```c++
int MilesEstimator::getMilesPerGallon() const {
	return 20;
}
```


Derived class :

```c++
class EfficientCarMilesEstimator : public MilesEstimator
{
	private:
		virtual int getMilesPerGallon() const overriee;
};
```
Implementation :

```c++
int EfficientCarMilesEstimator::getMilesPerGallon() const {
	return 35;
}
```

&nbsp;

### The Base Class Method has Default Arguments 


You can override the default argument.

If the base class has a defalt as 2 and the derived as 7 if derived is cast in a base pointer or reference, the value of 2 will be used. 

&nbsp;

### The Base Class Method Has a Different Acess Level


It is not common to change the access level, but there might be a few legitemate reasons to attempt. 

There are two approaches, one is to change the access specifier for the entire base class ( this is described later in the chapter) and the other is simpl to redefine the access in the derived class :

```c++
class Base
{
	public:
		virtual void someMethod();
};
```
```c++
class Derived : public Base
{
	protected:
		virtual void someMethod() override;
};
```

You can still access it through a pointer :
```c++
Base myBase;
Derived& ref = myBase;
ref.someMethod();
```

To lessen restrictions, you can create a public function that calls the private or protected function. 
&nbsp;

## Copy Constructors and Assignment Operators in Derived Classes <a name="copy_assignment"></a>

If your derived data does not have any special data that require nondefault copy constructors, you don't need to have one, regardless if the base class has one. If you do not explicitly define the copy constructor or `operator=`, a default will be provided for the data members specified in the derived class., and the base class ones will be used for data members specified in the base class. 

If you do need one, you need to chain it to the parent copy constructor :

```c++
class Base
{
	public:
		virtual ~Base() = default;
		Base() = default;
		Base(const Base& src);
};
```
```c++
Base::Base(const Base& src) {
}
```
```c++
class Derived : public Base
{
	public:
		Derived() = default;
		Derived( const Derived& src);
};
```
``` c++
Derived::Derived(const Derived& src) : base(src) {
}
```

Similarly, if the derived class overrides the `operator=`, you need to call the parent's version too :

```c++
Derived& Derived::operator=(const Derived& rhs) {
	if (&rhs == this) {
		return *this;
	}

	Base::operator=(rhs); // call parent's operator=
	// . . .
}
```
&nbsp;

### Run-Time Type Facilities	<a name="run_time"></a>


Relative to other OOP-oriented languages, C++ is very compile-time oriented. There are features that provide a run-time view of an object. They are grouped under the name ___run-time type information___ or _RTTI_. This offers some useful features. One such feature is `dynamic_cast()`. A second usage is the `typeid` operator, witch lets you query an object at run time to find out it's type :

```c++
#include <typeinfo>

class Animal { public: virtual ~Animal() = default; };
class Dog : public Animal {};
class Bird : public Animal {};

void speak(const Animal& animal) {
	if (typeid(animal) == typeid(Dog)) {
		cout << "Wof!" << endl;
	} else if (typeid(animal) == typeid(Bird)) {
		cout << "Chirp!" << endl;
	}
}
```

Whenever you have to do this, use instead a virtual method called `speak()` in the `Animal` class. 

#### Note : `typeid` works, similar to `dynamic_cast()`, only if the class has a _vtable_. 

One of the uses of `typeid` is logging and debugging :

```c++
class Loggable
{
	public:
		virtual ~Loggable() = default;
		virtual std::string getLogMessage() const = 0;
};
```
```c++
class Foo : public Loggable
{
	public:
		std::string getLogMessage() const override;
};
```
```c++
std::string Foo::getLogMessage() const {
	return "Hello logger.";
}
```
```c++
void logObject(const Loggable& loggableObject) {
	cout << "typeid(loggableObject).name()" << ": ";
	cout << loggableObject.getLogMessage() << endl;
}
```

Output:
```
class Foo: Hello logger.
```

In this case, the name was `class Foo`, however, it depends on the compiler. Using a different compiler, the output is :

```
3Foo: Hello logger.
```

&nbsp;

### Non-public Inheritance <a name="non_public"></a>

If you don't specify anny access specifier for the parent, it is `private` for a _class_ and `pubic` for a _struct_. 

Declaring the parent class to be `protected` means that everything inherited will be considered `protected`. This is not very common and is not recomanded. It usually shows a flaw in design. 
&nbsp;

### Virtual Base Classes <a name="virtual_base"></a>

Earlier we discussed diamond-shaped hierarchical structure for classes. The top class was the ambiguous base class. It was recomanded that the base class does not have any functionality because of ambiguous problems like naming. 

C++ has another mechanism to fix this, called _virtual base classes_ :


```c++
class Animal
{
	public:
		virtual void eat() = 0;
		virtual void sleep() { cout << "zzz" << endl; }
};
```
```c++
class Dog : public virtual Animal
{
	public:
		virtual void bark() { cout << "Woof" << endl; }
		virtual void eat() override { cout << "Dog : Done" << endl; }
};
```
```c++
class Bird : public virtual Animal
{
	public:
		virtual void chirp() { cout << "Chirp" << endl; }
		virtual void eat() override { cout << "Bird : Done."; }    
};
```
```c++
class DogBird : public Dog, public Bird
{
	public:
		virtual void eat() override { Dog::eat(); }
};
```

```c++
int main() {
	DogBird myConfusedAnimal;
	myConfusedAnimal.sleep(); // Not ambiguous because of virtual base class.
	return 0;
}
```
