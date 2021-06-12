

## Mastering Classes and Objects
&nbsp;

### Table of Content:
   * [Class Friends](#friends)
   * [Dynamic Memory Allocation in Objects](#dynamic)
   * [Move Semantics](#move-semantics)
   * [More about Methods](#more-methods)
   * [Method Overloading](#method-overloading)
   * [Inline Methods](#inline)
   * [Default Arguments](#default-arguments)
   * [Different kinds of Data Members](#data-member-kinds)
   * [Nested Classes](#nested-classes)
   * [Operator Overloading](#operator-overloading)
   * [Building Stable Interfaces](#stable-interface)



&nbsp;


### Friends <a name="friends"></a>

C++ allows classes to declare that other classes are friends and can access protected and private data members and methods. You can specifi that the Bar class is a friend of Foo like :

```
	class Foo {
		friend class Bar;
		// . . .
	};
```

You can also specify exact methods :

```
	class Foo {
		friend void Bar::processFoo(const Foo& foo);
		// . . .
	};
```

Also works with standalone functions :

```
	class Foo {
		friend void dumpFoo(const Foo& foo);
		// . . .
	};
```


The friend declaration also searves as a prototype.


&nbsp;

### Dynamic Memory Allocation in Objects <a name="dynamic"></a>

Last chapter described the SpreadsheetCell class. This one is modeled around the Spreadsheet class.

A Spreadsheet is a 2D array of SpreadsheetCell's, with methods to set and retrieve cells at specific locations. 


```

#include <cstddef>
#include <SpreadsheetCell.h>

class Spreadsheet {
	public:
		Spreadsheet(size_t width, size_t height);
		void serCellAt(size_t x, size_t y, const SpreadsheetCell& cell);
		SpreadsheetCell& getCellAt(size_t x, size_t y);
	
	private:
		bool inRange(size_t value, size_t upper) const;
		size_t mWidth = 0;
		size_t mHeigh = 0;
		SpreadsheetCell** mCells = nullptr;
};

```

#### Note: The Spreadsheet class uses normal pointers to show the consequences and teach how you should handle dynamic memory in classes.

#### Note : The Spreadsheet class does not contain a 2D array. Instead it contains a SpreadsheetCell * * . This is because the Spreadsheet object might have different dimentions, so the constructor of the class must dynamically allocate a 2D array, done like do :

```
Spreadsheet::Spreadsheet(size_t width, size_t height)
	: mWidth(width), mHeight(height)
{
	mCells = new SpreadsheetCell*[mWidth];
	for (size_t i = 0; i < mWidth; i++) {
		mCells[i] = new SpreadsheetCell[mHeight];
	}
}
```

`mCells` is a pointer on the Stack towards a 2D array on the Heap.

The implementation of the set and retrieval methods are streightforward :

```
void Spreadsheet::setCellAt(size_t x, size_t y, const SpreadsheetCell& cell) {
	if (!inRange(x, mWidth) || !inRange(y, mHeight)){
		throw std::out_of_range("");
	}
	mCells[x][y] = cell;
}
```
```
SpreadsheetCell& Spreadsheet::getCellAt(size_t x, size_t y) {
	if (!inRange(x, mWidth) || !inRange(y, mHeight)) {
		throw std::out_of_range("");
	}
	return mCells[x][y];
}
```

#### Note : Those two methods use a helper method called inRange().

If you look at those two methods, you can see some code duplication.
To deal with this, let's use a `verifyCoordonate()` method:

```
> voidverifyCoordinate(size_t x, size_t y) const;

void Spreadsheet::verifyCoordinate(size_t x, size_t y) const{
	if (x >= mWidth || y >= mHeight) {
		throw std::out_of_range("");
	}
}
```

And now rewrite the set and get functions :

```
void Spreadsheet::setCellAt(size_t x, size_t y, const SpreadsheetCell& cell) {
	verifyCoordinate(x,y);
	mCells[x][y] = cell;
}
```
```
SpreadsheetCell& Spreadsheet::getCellAt(size_t x, size_t y) {
	verifyCoordinate(x,y);
	return mCells[x][y];
}
```


&nbsp;

### Freeing Memory with Destructors

The Spreadsheet distructor:

```
class Spreadsheet {
	public: 
		Spreadsheet(size_t width, size_t height);
		~Spreadsheet();
		// . . .
};
```

The destructor does not take any arguments and there can only be one of them. Destructors are implicitly marked as `noexept`, since they should not throw any exceptions.

#### Note : A function can be marked with the noexcept keyward to specify that it won't throw exceptions :
```
void myNonThrowingFunction() noexcept { /*. . .*/ };
```

Implementation of the destructor :

```
Spreadsheet::~Spreadsheet() {
	for (size_t i - 0; i < mWidth; i++) {
		delete [] mCells[i];
	}
	delete [] mCells;
	mCells = nullptr;
}
```
&nbsp;

### Handling Copying and Assignment

Remember that if you don't write a copy constructor and an assignment operator youtself, C++ writes them for you. The copy constructor does not behave well with primitives, only copying bitwise. That presents problems if you dynamically allocate memory in the object. 

The following code copies the spreadsheet `s1` to initialize `s` when `s1` is passed to the `printSpreadsheet()` :

```
#include "Spreadsheet.h"

void printSpreadsheet(Spreadsheet s) {
	// . . .
}

int main() {
	Spreadsheet s1(4,3);
	printSpreadsheet(s1);
	return 0;
}
```

The spreadsheet contains one pointer var : `mCells`. A shallow copy gives the destination object a copy of the `mCells` pointer but not the underlying data. Thus, in this situatuation, both `s1` ans `s` point to the same data.

The problem is even worse with assignment :

```
Spreadsheet s1(2,2), s2(4,3);
s1 = s2;
```

Not only are they pointing to the same memory but you also have a memory leak for the SpreadsheetCells that were previously assigned to the first spreadsheet.


Relying on the default copy constructor is not good. You must do a deep copy.

#### Note : Whenever you have dynamically allocated memory you should write your own copy constructor and assignment operator to provide a deep copy of the memory.


The Spreadsheet copy constructor :

```
class Spreadsheet {
	public:
		Spreadsheet(const SPreadsheet& src);
		// . . .
}
```
```
Spreadsheet::Spreadsheet(const Spreadsheet& src)
	: Spreadsheet(src.mWidth, src.mHeight)
{
	for(size_t i; i< mWidth; i++){
		for(size_t j; j< mHeight; j++) {
			mCells[i][j] = src.mCells[i][j];
		}
	}
}
```


The Spreadsheet Assignment Operator :

``` 
class Spreadsheet {
	public:
		Spreadsheet& operator=(const Spreadsheet& rhs);
		// . . .
}
```

A naive implementation could be as follows :

```
Spreadsheet& Spreadsheet::operator=(const SPreadsheet & rhs) {
	
	// Check for self-assignment
	if (this == &rhs) {
		return *this;
	}

	// Free the old memory
	for (size_t i = 0; i < mWidth; i++) {
		delete[] mCells[i];
	}

	delete[] mCells;
	mCells = nullptr;

	// Allocate new memory
	mWidth = rhs.mWidth;
	mHeight = rhs.mHeight;

	mCells = new SpreadsheetCell*[mWidth];
	for (size_t i = 0; i < mWidth; i++) {
		mCells[i] = new SpreadsheetCell[mHeight];
	}
	
	// Copy the data
	for (size_t i = 0; i < mWifth; i++) {
		for (size_t j = 0; i < mHeight; j++) {
			mCells[i][j] = rhs.Cells[i][j];
		}
	}

	return *this;
}
```

A lot could go wrong with this code because a lot is going on. It is possible that the `this` object gets into an invalid state. For example, suppose that the memory is successfully freed, that the `mWidth` and `mHeight` are properly set, but an exception is thrown in the loop that is allocating the memory. When that happens, execution of the remainder of the method is slipped and the method is exited. Now the Spreadsheet instance is corrupt; it's `mWidth` and `mHeight` data members state a certain size, but `mCells` data member does not have the right amount of memory. In conclusion, this code is not very safe.

What we need is an all for nothing mechanism, either everything succeeds or the this object remains untouched. To implement such an exceptionsafe assignment operator, the _copy-and-swap idiom_ is recomanded. For this, a non-member `swap()` function is implemented as a friend of the spreadsheet class. You could also write a `swap()` member of the class, however it is recomanded practice to keep it a non-member so that is can also be used by various Srandard Library algorithms. 

Here is a definition of them :

```
class Spreadsheet 
{
	public :
		Spreadsheet& operator=(const Spreadsheet& rhs);
		friend void swap(Spreadsheet& first, Spreadsheet% second) noexcept;
		// . . .
};
```

The implementation of the `swap()` function swaps each data member using the `std::swap()` utility function provied by the std headher `<utility>` :

```
void swap(Spreadsheet& first, Spreadsheet& second) noexcept {
	
	using std::swap;
	
	swap(first.mWidth, second.mWidth);
	swap(first.mHeight, second.mHeight);
	swap(first.mCells, second.mCells);
}
```
With this exception safe function, we can implement the assignment operator :

```
Spreadsheet& Spreadsheet::operator=(const Spreadsheet& rhs) {
	
	// Check for self-assignment
	if (this == &rhs) {
		return *this;
	}

	Spreadsheet temp(rhs); // Do all the work in a temporary instance
	swap(*this, temp); // Commit the wotk with non-throwing operatoins
	return *this;
}
```

The implementation uses the copy-and-swap idiom. For efficiency, but sometimes also for corectness, the first line ususally checks for self-assignment.

This implementetion is recomanded because it guarantees strong exception safety :

 * The first phase makes a temporry copy. This does not modify the state of the current Spreadsheet object, and so there is no problem if an exception is thrown during this phase.

 * The second phase uses the swap() function to swap the temporaty copy with the current object. The swap() function shall never throw exceptions.

 * The third phase is the destruction of the temporary object, witch now contains the original object, to clean up any memory.


&nbsp;

### Disallowing Assignment and Pass-By-Value

Sometimes when you dynamically allocate memory in your class, it's easier to prevent anyone from copying and assigning to your objects.  You can do this explicitly by deleting your _operator=_ and _copy constructor_ :

```
class Spreadsheet
{
	public :
		Spreadsheet(size_t width, size_t height);
		Spreadsheet(const Spreadsheet& src) = delete;
		~Spreadsheet();
		Spreadsheet& operator=(const Spreadsheet& rhs) = delete;
		// . . .
};
```

When you write code to copy or assign to Spreadsheet object, the compiler will complain :

```
'Spreadsheet &Spreadsheet::operator=(const Spreadsheet &)' : attempting to reference a deleted function
```

#### Note : If your compiler doesn't allow explicitly deleting member functions , you can disallow copying and assigning by making your copy constructor and assignment operator private without any implementation.


&nbsp;

## Handling Moving with Move Semantics <a name="move-semantics"></a>

Move semantics for objects require :

 * Move Constructor.
 * Move assignment operator.

These can be used by the compiler when the source object is a temporary object that will be destroyed after the operation is finished. 

Both move the data members from the source object to the new object,leaving the source object in some valid but otherwise indeterminate state. Often, data members are reset to null valies. 

This process actually moves ownership of the memory and other resources from one object to another object. They basically do a shallow copy of the member variabes and witch ownershop to prevent dangling pointers or resources, and to prevent memory leaks.

Before the move semantics, you need to learn about _rvalue_ and _rvalue references_.

&nbsp;

### Rvalue References

In C++, an _lvalue_ is something of whitch you can take an address; for example : a named variable. The name comes from the fact that lvalues can appear on the left-size of an assignment. 

An _rvalue_, on the other hand, is anything that is not an lvalue, sucha s a literal, or temporary object or value. Typically, an rvalue is on the right-side of an assignment operator. For example, take the following statement :

```
int a = 4 * 2;
```
In this statement, `a` is an _lvalue_, it has a name and you can take the address of it with `@a`. The result of the expression `4 * 2` is an _rvalue_. It is a temporary value that is destroyed when the statement finished execution. In this example, a copy of this temporary value is stored in the variable with the name _a_.


A _rvalue reference_ is a reference to an rvalue. In particular, it is a concept that is applied when the rvalue is a temporary object. The purpouse is to make it possible for a particulat function to be chosen when a temporary object is involved. The consequence of this is that certain operations that normally involve copying large values can be implemented by copying pointers to those values, knowing the temporary object will be destrpyed.


A function can specify an rvalue reference parameter by using `&&` as part of the parameter specification. For example `type&& name`. Normally, a temporary object will be seen as a `const type&`, but when there is a function overload that uses an rvalue reference, a temporary object can be resolved to that overload. The next example demonstrates this. The code defines two handleMessage() functions, one accepting an _lvalue_ reference and another accepting an _rvalue_ reference:

```
// lvalue reference
void handleMessage(std::string& message) {
	cout << "handleMessage with lvalue reference: " << message << endl;
}
```
```
//rvalue message
void handleMessage(std::string&& message) {
	cout << "handleMessage with rvalue reference: " << message << endl;
}
```

You can call `handleMessage()` with a named variable as argumenr :

```
std::string a = "Hello ";
std::string b = "World";
handleMessage(a);
```

Any changes `handleMessage()` does through its reference parameter will change the value of a.

You can also call with an expression as argument :

```
handleMessage(a + b);
```

The `handleMessage()` function accepting an lvalie cannot be used, because the expression `a + b` is a temporary, witch is not an lvalue. In this case the rvalue reference version is called. Because it is temporary, any changes `handleMessage()` does through its reference parameter will be lost after the call returns.

A literal can also be used as argument to `handleMessage()`. This also triggers the rvalue reference version because a literal cannot be an lvalue (though it can be passed as argument to a const reference parameter).

```
handleMessage("Hello World"); // calls rvalue
```


If you remove the `handleMessage()` function accepting the lvalue reference, calling `handleMessage(b)` with a named variable will result in a compile error. 

You can force the compiler to call the rvalue reference version of `hanleMessage()` by using `std::move()`, witch casts an lvalue into an rvalue :

```
handleMessage(std::move(b)); //calls rvalue
```
Because the variable `b` is a named variable, an lvalue, you need to use the `std::move()` to cast the lvalue to an rvalue. 

#### Note : A named rvalue reference, such as a rvalue reference parameter is itself an lvalue because it has a name.

 Rvalue references are not limited to parameters of functions. You can declare a variable of an rvalue reference type and assign to it (this is uncommon). Consider the following code, witch is illigal in C++ :

```
int& i = 2;	  // Invalud : reference to a constant.
int a = 2, b = 3;
int& j = a + b;	  // Invalid : reference to a temporary.
```

Using an rvalue reference, the following is legal :

```
int&& i = 2;
int a = 2, b = 3;
int&& j = a + b;
```
&nbsp;

### Implementing move semantics

Move semantics is implemented by using rvalue reference. To add move semantics to a class you need to implement a _move constructor_ and a _move assignment operator_. They should be `noexcept`. This is particularly important for comparibility with the Standard Library as fully compliant implementations of, for example, std containers will only move stored objects if, having move semantics implemented, they guarantee not to throw.

Following the Spreadsheet class example, two helper methods are introduced as well : `cleanup()` witch is used by the destructor and the move assignment operator, and `moveFrom()`, witch moves the member variables from a source to a destination, and than resets the source object.

```
class Spreadsheet
{
	public:
		Spreadsheet(Spreadsheet&& src) noexcept;		// Move constructor
		Spreadsheet& operator=(Spreadsheet&& rhs) noexcept;	// Move assign
	// . . .

	private :
		void cleanup() noexcept;
		void moveFrom(Spreadsheet& src) noexcept;
		// . . .
};
```

Implementation :

```
void Spreadsheet::cleanup() noexcept {

	for (size_t i = 0; i < mWifth; i++) {
		delete[] mCells[i];
	}

	delete[] mCells;
	mCells = nullptr;
	mWidth = mHeight = 0;
}
```
```
void Spreadsheet::moveFrom(Spreadsheet$ src) noexcept {
	
	// Shallow copy of data
	mWidth = src.mWidth;
	mHeight = src.mHeight;
	mCells = src.mCells;

	// Reset the source object, because ownership os moved
	src.mWidth = 0;
	src.mHeight = 0;
	src.mCells = nullptr;
}
```
```
// Move Constructor
Spreadsheet::Spreadsheet(Spreadsheet&& src) noexcept {
	moveFrom(src);
}
```
```
// Move Assignment Operator
Spreadsheet::Spreadsheet::operator=(Spreadsheet&& rhs) noexcept {

	// Check for self-assignment
	if (this == &rhs) {
		return *this;
	}

	// Free up old memory
	cleanup();
	moveFrom(rhs);
	return *this;
}
```

Both the _move constructor_ and the _move assignment operator_ are moving ownership of the memory for `mCells` from the source object to the new object. 

Obviously, move semantics is useful only when you know that the source object will be destroyed.

Move constructors and Move assignment operators can be explicitly deleted or defaulted.

The compiler automatically agenerates a default move constructor for a class if the there is no user-declared copy constructor, copy assignment operator, move constructor or destructor. 


#### Note : If you have dynamically allocated memory in your class, then you typically should implement a destructor, copy constructor, move constructor, copy assignment operator, and move assignment operator. This is caled the __Rule of Five__.


&nbsp;

### Moving Object Data Members


The `moveFrom()` method usese direct assignment because the members are primitives. If your object has other objects as data members, than you should move these objects using the `std::move()`.

Suppose the Spredsheet class had a `std::string` data member caled `mName`. The `moveFrom()` method is then implemented like :

```
void Spreadsheet::moveFrom(Spreadsheet& src) noexcept {
	
	// Move object data members
	mName = std::move(src.mName);
	
	// Move primitives
	// Reset the source object
	// . . .
}
```
&nbsp;

#### Move Constructor and Move Assignment Operator in Terms of Swap

The previous implementations use the `moveFrom()` method to make shallow copies. With this implementation, if you add a new data member you need to modify both the `swap()` function and thr `moveFrom()` method. To avoid this, you can write a move constructor and the move assignment operator in terms of a default constructor and the `swap()` function.

The first thing to do is to add a default constructor to the Spreadsheet class. Users do not need it, so it should be private :


```
class Spreadsheet
{
	private :
		Spreadsheet() = default;
		// . . .
};
```
Next, we can remove the `cleanup()` and `moveFrom()` helper methods. The code for `cleanup()` is moved to the destructor. 

```
Spreadsheet::Spreadsheet(Spreadsheet&& src) noexcept
	: Spreadsheet()
{
	swap(*this, src);
}
```
```
Spreadsheet::Spreadheet::operator=(Spreadsheet&& rhs) noexcept {
	Spreadsheet temp(std::move(rhs));
	swap(*this, temp);
	return *this;
}
```

__The move constructor__ delegated first to the default constructor. Then, the default constructed `*this` is swapped with the given source object.  The __move assignment operator__ first creates a local Spreadsheet instance that is move-constructed with `rhs`. Than, `*this` is swapped with this local move-constructed Spreadsheet instance.

This implementation might be a bit less efficient compared to the earlier one. The advantage is that it requires less code and it is less likely bugs are introduced when data members are added to the class, because you only need to update the `swap()` method.


&nbsp;

### Testing the Spreadsheet Move Operations


They can be tested with the following code :

```
Spreadsheet createObject() {
	return Spreadsheet(3, 2);
}

int main() {
	vector<Spreadsheet> vec;

	for (int i = 0; i < 2; ++i) {
		cout << "Iteration" << i << endl;
		vec.push_back(Spreadsheet(100, 100));
		cout << endl;
	}

	Spreadsheet s(2, 3);
	s = createObject();

	Spreadsheet s2(5, 6);
	s2 = s;
	
	return 0;
}
```
This uses a __vector__. A vector grows dynamically in size by allocating a bigger chunk of memory and than copying the objects from the old vector. If the compiler finds a move constructor, the vector only moves the objects instead of deep-copying them, making it much more efficient.

If you add output statements to all constructors and assignment operators in the Spreadsheet class, the output would be like :

```
Iteration 0		
Normal constructor		(1)
Move constructor		(2)

Iteration 1
Normal constructor		(3)
Move constructor		(4)
Move constructor		(5)

Normal constructor		(6)
Normal constructor		(7)
Move assignment operator	(8)
Normal constructor		(9)
Copy assignment operator	(10)
Normal constructor		(11)
Copy constructor		(12)
```
On the first iteration of the loop, the vector is still empty. Take the following like of code from the loop :
```
vec.push_back(Spreadsheet(100, 100));
```
With this line, a new Spreadsheet object is created with the normal constructor. The Spreadsheet object is created(1) and then moved into the vector, invoking the move constrructor(2).

On the second iteration, a second Spreadsheet object is created(3). At this point, the vector can only hold one element, so it's again resized to make space for a second object. Because it is resized, the previousely added element needs to be moved from the old vector to the new and bigger vector. This triggers a call to the move constructor for each previously added element(4). 

Next, a Spreadsheet objet `s` is created using the normal constructor (6). The `createObject()` function creates a temporary Spreadsheet object with its normal constructor (7), witch is then returned from the function and assigned to the variable `s`. Because the temporary object returned, it ceases to exist after the assignment, the compiler involked the move assignment operator (8) instead of the normal copy assignment operator. Another Spredsheet object is created `s2` using the normal constructor (9). The assignment `s2 = s` involkes the copy addignment operator (10) because the right-hand side object is not a temporary object, but a named object. This copy addignment operator creates a temporary copy, witch triggers a call to the copy constructor, witch first delegates to the normal constructor (11, 12).

If the Spreadsheet class did not implement move semantics, all the calls to the move constructor and move assignment operator would be replaced with calls tot he copy constructor and copy assignment operator. In the previous example, the objects in the loop have 10,000 (100 * 100) elements. The implementation of the Spreadsheet move constructor and move assignment operator doesn't require any memory allocation, while the copy constructor and copy assignment operator require 101 allocations each. In this case, using move semantics can increse performance a lot.

&nbsp;

### Implementing a Swap Function with Move Semantics

Another example where it can increse performance. The following `swapCopy()` does not use move semantics :

```
void swapCopy(T& a, T& b) {
	T temp(a);
	a = b
	b = temp;
}
```
This implementation will hurt performance if T is expensive to copy. With move semantics :

```
void swapMove(T& a, T& b) {
	T temp(std::move(a));
	a = std::move(b);
	b = std::move(temp);
}
```
&nbsp;

### Rule of Zero

Earlier in the chapter we introduces The Rule of Five:
 * destructor
 * copy and move constructors
 * copy and move assignment operators

However, in modern C++, you should adopt the so-called _Rule of Zero_.

The Rule of Zero states that you should design your classes in such a way that they do not require any of those five special member functions.

How do you do that?

Basically, you should avoid having any old-style dynamically allocated memory. Instead, you should use modern constructs such as Standard Library Containers. For example, use a `vector<vector<SpreadsheetCell>>` instead of the `SpredadsheetCell**` data member in the Spreadsheet class. The vector handles memory automatically, so there is no need for the five member functions.

&nbsp;

### More about Methods <a name="more-methods"></a>

### _static_ Methods

Methods, like members, sometimes apply to the class as a while, not to each object. You can write static methods, like members. An example would be the `stringToDoublr()` and `doubleToString()` methods from the `SpreadsheetCell` class. The methods don't access information about specific objects, so they could be static. Here is the class definition :

```
class SpreadsheetCell
{
	// . . .
	private :
		static std::string doubleToString(double inValue);
		static double stringToDouble(std::string_view inString);
		// . . .
};
```

The implementations are identical. You don't repeat the `static` keyward in front of the method definitions. Note that static methods have no `this` pointer, and are not executing for a specific object with access to its non-static members. In fact, a static method is just like a regular function. The only difference is that it can access `protected` and non-static data members on objects of the same type by passing a reference or pointer to such an object.


&nbsp;

### _const_ Methods

The only way to pass `const` data members to a function is to make the method `const` for the compiler to make sure that the member will not be changed.
Here is the SpreadsheetCell class with the methods that don't change any data members marked as const :

```
class SpreadsheetCell 
{
	public :
		// . . .
		double getValue() const;
		std::string getString() const;
		// . . .
};
```
The `const` specifier is part of the method prototype and must accompany the definition as well.

Making a method `const` signs a contract with the client code guaranteeing that you will not change the internal value of the object within the method. You also can't declare a `static` method as `const` because it is redundant. Static methods do not have an instance of the class, so it would be impossible for them to change internal values. 

You can call `const` and non-`const` methods on non=`const` objects. However, you can only call `const` methods on `const` objects.

#### Note : You should get into the habbit of declaring const all methods that don't modify the object so that you can use references to const objects in your program. 


&nbsp;

### __mutable__ Data Members

Suppose you want profile your Spreadsheet application to know how often data is being read. To do this, you could add a counter to the SpreadsheetCell class that counts each call to `getValue()` and `getString()`. The functions are no longer `const` in the compilers eyes if they modify a counter. The solution is to mark the counter variable as `mutable` so that it can be edited from inside a `const` function. 

```
class SpreadsheetCell
{
	// . . .
	private :
		duble mValue = 0;
		mutable size_t mNumAccesses = 0;
};
```

Implementations for `getValue()` and `getString()` would be :

```
double SpreadsheetCell::getValue() const {
	mNumAccesses++;
	return mValue;
}
```
```
std::string SpreadsheetCell::getString() const {
	mNumAccesses++;
	return doubleToString(mValue);
}
```

&nbsp;

### Method Overloading <a name="method-overloading"></a>

Just like you can write different constructors all with the same name, but different parameters. You can do the same with any method or function in C++. This is called ___overloading___.

For example, in the `SpreadsheetCell` class, you can rename both `setString()` and `setValue()` to `set()`.

```
class SpreadsheetCell
{
	public :
		// . . .
		void set(double inValue);
		void set(std::string_view inString);
		// . . .
};
```

The implementations stay the same. When using set, the compiler determines from the arguments passed witch one to call. This is called _overloading resolution_.

You might be tempted to rename `getValue()` and `getString()` to `get()`, however that does not work. C++ does not allow overloading based only on the return type as it would be impossible for the compiler to determine witch instance to call. 

&nbsp;

### Overloading Based on _const_

You can write the same method two times, one being `const` and the other not. The compiler can determine the instance based on the argument being `const` or not.

Often, the implementation is identical. To prevent code duplication, you can use Scott Meyer's ___const_cast()___ pattern. 

For example, the `Spreadsheet` class has a method called `getCellAt()` returning  anon-const reference to a `SpreadsheetCell`. You can add a const overload that returns a `const` reference as follows :

```
class Spreadsheet
{
	public :
		SpreadsheetCell& getCellAt(size_t x, size_t y);
		const SpreadsheetCell& getCellAt(size_t x, size_t y) const;
		// . . .
};
```

Scott Meyer's _const_cast()_ pattern says that you should implement the `const` version as you normally would, and implement a non-const verion by forwarding the cell to the `const` version with the appropiate casts. Basically, cast a `*this` to a `const Spreadsheet&` using `std::as_const()` (defined in `<utility>`), call the const version of `getCellAt()`, and then remove the const from the result using a `const_cast()` :

```
const SpreadsheetCell& Spreadsheet::getCellAt(size_t x, size_t y) const {
	verifyCoordinate(x, y);
	return mCells[x][y];
}
```
```
SpreadsheetCell& Spreadsheet::getCellAt(size_t x, size_t y) {
	return const_cast<SpreadsheetCell&>(std::asconst(*this).getCellAt(x, y));
}
```

With this overload, you can now call `getCellAt()` on `const` and non-`const` objects.


&nbsp;

### Explicitly Deleting Overloads

Overloaded methods can be explicitly deleted, witch allows you to disallow calling a method with particular arguments. For example :

```
class MyClass
{
	public :
		void foo(int i);
};
```
The `foo()` method can be called like :

```
myClass c;
c.foo(123);
c.foo(1.23);
```
For the third line, the compiler converts the `double` value to an `int` value and then calls `foo()`. You can prevent this by explicitly deleting a double instance of `foo()` :

``` 
class MyClass
{
	public :
		void foo(int i);
		void foo(double d) = delete;
};
```
&nbsp;

### Inline Methods <a name="inline"></a>

C++ gives you the ability to say that the call to a method or function should not actually be implemented as a call to a separate block of code. Instead, the compiler should insert the method's body directly into the code where the method is called. This is called _inlining_ and methods that use this are _inlne_ methods. Inlining is safer tan defining `#define` macros.

You can specify an inline by placing the `inline` keyward before the name :

```
inline double SpreadsheetCell::getValue() const {
	mNumAccesses++;
	return mValue;
}
```

If the compiler thinks it will hurt the performance, it will ignore it.


Definitions of inline methods must be available in every source file in witch they are called. You should put them in header files.

There is also an alternative syntax in witch you place the method definition directly in the class definition :

```
class SpreadsheetCell
{
	public :
		// . . .
		double getValue() const { mNumAccesses++; return mValue; }
		std::string getString() const
		{
			mNumAccesses++;
			return doubleToString(mValue);
		}
};
```

&nbsp;

### Default Arguments <a name="default-arguments"></a>

You can specify defaults for function and method parameters in the prototype. If the user enters those arguments, the default will be ignored, otherwise the compiler runs with the defaults.
There is a limitation, you can only provide defaults for a continious list of parameters starting from the _rightmost parameter_.

```
class Spreadsheet
{
	public :
		Spreadsheet(size_t width = 100, size_t height = 100);
		// . . .
};
```

Now you can use the constructor with zero, one or two arguments :

```
Spreadsheet s1;
Spreadsheet s2(5);
Spreadsheet s3(5, 6);
```

A constructor with defaults for all parameters can function as a default constructor. If you try to declare a multi-argument constructor with defaults for all params and a default constructor, the compiler will complain because it doesn't know witch to call in the case of 0 arguments given.

&nbsp;

### Different Kinds Of Data Members <a name="data-member-kinds"></a>

#### _static_ Data Members

A static data member is a member associated with the class instead of an object. They are global variables specific to a class. You can implement a counter to give each spreadsheet an ID as follows :

```
class Spreadsheet
{
	// . . .

	private :
		static size_t sCounter;
};
```

In adition to listing a `static` class member in the class definition, you also need to allocate space for them in the source file, usually the source file in witch you place your class method definitions. You can initialize them, but as default they are 0. Static pointers are initialized to `nullptr`. 

```
size_t Spreadsheet::sCounter;
```
```
size_t Spreadsheet::sCounter = 0;
```
&nbsp;

#### _Inline_ Variables


You can declare static data members as `inline`. The benefit is that you do not have to allocate space for them in the source file.

```
class Spreadsheet
{
	// . . .
	
	private :
		static inline size_t sCounter = 0;
};
```

Now there is no need for the following line :

```
size_t Spreadsheet::sCounter;
```
&nbsp;

### Accessing static Data Members within Class Methods

You can use static data members like any regular data methods.

For example, you might want to create an `mId` data member for the `Spreadsheet` class and initialize it from `sCounter` in the Spreadsheet constructor.

```
class Spreadsheet
{
	public :
		// . . .
		size_t getId const;
	private:
		static size_t sCounter;
		size_t mId = 0;
};
```

In the constructor :

```
Spreadsheet::Spreadsheet(size_t width, size_t height) {
	: mId(sCounter++), mWidth(width), mHeight(height)
{
	mCells = new SPreadsheetCell*[mWidth];
	for (size_t i = 0; i < mWidth; i++) {
		mCells[i] = new SpreadsheetCell[mHeight];
	}
}
```

You should not copy the `mId` in the copy assinment operator. The ID should never change. Thus it should be a const data member.

&nbsp;

### Accessing static Data Members Outside Methods

Access control specifiers apply to static data methods : `sCounter` is `private` so it can't be accessed from outside class methods.

If it were `public`, you could access it from outside like :

```
int c = Spreadsheet::sCounter;
```

However, it is not recomanded. You should grant access throw get/set methods. If you want to grant access to a `static` data member, you need to implement `static` get/set methods.

&nbsp;

### _const static_ Data Members

You should use `static const`  data members instead of global constants when the constants only apply to the class, also called _class constants_. `static const` data members of integral and enumeration type can be defined and initialized inside the class definition wtihout making them `inline` variables. 

For example, you might want to specify a max hight and width for spreadsheets. If the user inserts a greater amount, the max is used instead.

```
class Spreadsheet
{
	public :
		// . . .	
		static const size_t kMaxHeight = 100;
		static const size_t kMax Width = 100;
};
```

You can use the new constant in the constructor like :

```
Spreadsheet::Spreadsheet(size_t width, size_t height)
	: mId(sCounter++)
	, mWidth(std::min(width, kMaxWidth))	// std::min() requires <algorithm>
	, mHeight(std::min(height, kMaxHeight))
{
	mCells = new SpreadsheetCell*[mWidth];
	// . . .
}
```

#### Note : You could also throw an exception instead. However, the destructor will not be called when you throw an exception from the constructor.

#### Note : Non-static data members can also be const. For example, `mId` could be const. Because you cannot assign to const data members, you need to initialize them with in-class initializers or with ctor-constructors. 


`kMaxHeight` and `kMaxWidth` are public, so they can be accessed from anywhere in your program, like global variables with different syntax :

```
cout << "Maximum height is : " << Spreadsheet::kMaxHeight << endl;
```

These values can also be used as default values for the constructor parameters :

```
class Spreadsheet
{
	public : 
		Spreadsheet(size_t width = kMaxWidth, size_t height = kMaxHeight);
		// . . .
};
```
&nbsp;

### Reference Data Members


You need code to control the entire spreadsheet program, witch you could package inside a `SpreadsheetAplication` class.

In order for the classes to communicate, you need the `SpreadsheetAplication` class to know about the `Spreadsheet` class and the `Spreadsheet` class to know about the `SpreadsheetApplicatoin` class. This is a circular reference and cannot be solved with normal `#include#s. The solution is to use a forward declaration in one of the header files. 

```
class SpreadsheetAplication;	//forward declaration

class Spreadsheet
{
	public :
		Spreadsheet(size_t width, size_t height,
			SpreadsheetAplication& theApp);
		// . . .
	
	private :
		SpreadsheetApplication& mTheApp;
};
```

This definition adds a `SpreadsheetApplication` reference as a data member. It's recomanded to use a reference here instead of a pointer because a `Spreadsheet` should always refer to a `SpreadsheetApplication`. This would not be guaranteed with a pointer.

#### Note : This is done for demonstrational purposes, it is not recomanded to couple the classes together this way, but instead to use a paradigm such as MVC (Model-View-Constroler).

Every instance of `Spreadsheet` has the applicatoin reference. A reference cannot exist without refering to something :

```
Spreadsheet::Spreadsheet(size_t width, size_t height,
	SpreadsheetApplication& theApp)
	: mId(sCounter++)
	, mWidth(std::min(width, kMaxWidth))
	, mHeight(std::min(height, kMaxHeight))
	, mTheApp(theApp)
{
	// . . .
}
```

You must also initialize the reference member in the copy constructor. 

&nbsp;

### _const_ Reference Data Members

You might decide that `Spreadsheet` should only have a `const` reference to the application object.

```
class Spreadsheet
{
	public :
		Spreadsheet(size_t width, size_t height,
		    const SPreadsheetApplicatoin& theApp);
		// . . .

	private :
		const SpreadsheetApplication& mTheApp;
};
```

The const reference can only be used to call `const` methods on the `SpreadsheetApplication` object. If you try to call a non-`const` method, you will get a compiler error.

It's also possible to have a `static` reference member or a `static const` reference member.

&nbsp;

### Nested Classes <a name="nested-classes"></a>


You can write nested classes and structs, declare type aliases or create enumerated types as data members. If it is `public`, you can access it from outside the class by the scope resolution syntax : `ClassName::`.

You can also provide a class definition inside another class definition. For example, you could nest the `SpreadsheetCell` class inside the `Spreadsheet` class and call it `Cell`.

```
class Spreadsheet
{
	public :
		class Cell
		{
			public:
				Cell() = default;
				Cell(double initialValue);
				// . . .
		};

		Spreadsheet(size_t width, size_t height,
		   const SpreadsheetApplication& theApp);
		// . . .
};
```


Now `Cell` is defined inside the `Spreadsheet` class, so anytime you must refer to it outside of this class, you must use the scope operator. This applies even to method definitions.

```
Spreadsheet::Cell::Cell(double ininialValue)
	: mValue(initialValue)
{
	// . . .
}
```


You must even use the syntax for return types :

```
Spreadsheet::Cell& Spreadsheet::getCellAt(size_t x, size_t y) {
	verifyCoordinate(x, y);
	return mCells[x][y];
}
```


Fully defining the `Cell` class inside the `Spreadsheet` class makes the definition of it wierd. You could include a forward declaratoin and then define `Cell` separately.

```
class Spreadsheet
{
	public :
		class Cell;

	Spreadsheet(size_t width, size_t height,
	   const SpreadsheetApplication& theApp);
	// . . .
};
```
```
class Spreadsheet::Cell
{
	public :
		Cell() = default;
		Cell(double initialValue);
		// . . .
};
```

If you declare a `private` or `protected` nested class, you can only use it inside the outer class. A nested class has access to all the `protected` and `private` elements of the outer class.


&nbsp;

### Enumerate Types Inside Classes


If you want to define a number of constants inside a class, you should use an enumerated type instead of a collection of `#define`s. For example, you could add support for the cell coloring like :

```
class SpreadsheetCell
{
	public :
		// . . .
		enum class Color{ Red = 1, Green, Blue, Yellow};
		void setColor(Color color);
		Color getColor() const;
	private :
		// . . .
		Color mColor = Color::Red;
};
```
Implementation :

```
void SpreadsheetCell::setColor(Color color) { mColor = color; }

SpreadsheetCell::olor SpreadsheetCell::getColor() const { return mColor; }
```
Usage :
```
SpreadsheetCell myCell(5);
myCell.setColor(SpreadsheetCell::Color::Blue);
auto color = myCell.getColor();
```

&nbsp;

### Operator Overloading <a name="operator-overloading"></a>

You often want to perform operations on objects. For example, you want to sum en entire row of cells.


#### Example : Implementing addition

Usually in OOP, `SpreadsheetCell` objects should be able to add themselves to other `SpreadsheetCell` objects. Adding two cells produces a third cell as result. It doesn't change the originals. 

You can declare an `add()` method like this :

```
class SpreadsheetCell
{
	public :
		// . . .
		SpreadsheetCell add(const SpreadsheetCell& cell) const;
};
```

```
SpreadsheetCell SpreadsheetCell::add(const SpreadsheetCell& cell) const {
	return SpreadsheetCell(getValue() + cell.getValue());
}
```

Usage:
```
SpreadsheetCell myCell(4), anotherCell(5);
SpreadsheetCell aThirdCell = myCell.add(anotherCell);
```
It is not that good.
A better attempt would be to overload the '+' operator.
To do that, you must define a method named `operator+` :

```
class SpreadsheetCell
{
	public :
		// . . .
		SpreadsheetCell operator+(const SpreadsheetCell& cell) const;
};
```
Implementation :
```
SpreadsheetCell SpreadsheetCell::operator+(const SpreadsheetCell& cell) const {
	return SpreadsheetCell(getValue() + cell.getValue());
}
```
Usage:
```
SpreadsheetCell aThirdCell = myCell + another Cell;
```


When the compiler finds an operator like `+`, `-`,`=`, it tries to find functions called `operator+`, `operator-`, `operator=`, etc. It tries first in the class definition, and then looks for global. 

#### Note : A function like this can return anything you want.

&nbsp;

### Implicit Conversions

If you wrote the `operator+` function for `SpreadsheetCell`, you can also add it to an `int`, `double`, `string`, etc.



This happens because the compiler does not just look for an `operator+` function that matches the types exactly. It also tries to find an appropiate conversion. Constructors offer one. If you try to add a `SpreadsheetCell` to a double, the compiler finds the `SpreadsheetCell` constructor and creates a temporary `SpreadsheetCell` Object. It also does this with other types.

It doesn't really make sense to add a `std::string_view` to a `SpreadsheetCell`, so you can prevent this using the `explicit` keyward :

```
class SpreadsheetCell
{
	public :
		SpreadsheetCell() = default;
		SpreadsheetCell(double initialValue);
		explicit SpreadsheetCell(std::string_view initialValue);
		// . . .
};
```

The `explicit` keyward goes only in class definitions.

To avoid implicit constructon of objects from double, you could add a second `operator+` :

```
SpreadsheetCell SpreadsheetCell::operator+(double rhs) const {
	return SpreadsheetCell(getValue() + rhs);
}
```
&nbsp;

### A third attempt

The operator is not commutative :

```
aThirdCell = myCell + 4;	// Works
aThirdCell = myCell + 5.6;	// Works
aThirdCell = 4 + myCell;	// FAILS TO COMPILE
aThirdCell = 5.6 + myCell;	// FAILS TO COMPILE
```

It only works if the `SpreadsheetCell` object is on the left. Addition is supposed to be commutative, so something is wrong here. The problem is that the `operator+` must be called on the `SpreadsheetCell` object.

You can get it to work if you replace the in-class `operator+` method with a global `operator+` function that is not tied to any particular object.

```
SpreadsheetCell operator+(const SpreadsheetCell& lhs,
	const Spreadsheet& rhs)
{
	return SpreadsheetCell(lhs.getValue() + rhs.getValue());
}
```


You need to declare the operator in the header file :

```
class SpreadheetCell
{
	// . . .
}

SpreadsheetCell opearator+(const SpreadsheetCell& lhs,
	const SpreadsheetCell& rhs);
```

Now all four additions work as expected.

&nbsp;

### Overloading Arithmetic Operations

The rest of the arithmatic operators are very similar to the `operator+`


For division, you have to check for division by 0:

```
SpreadsheetCell operator/(const SpreadsheetCell& lhs,
	const SpreadsheetCell& rhs)
{
	if (rhs.getValue() == 0) {
		throw invalid_argument("Divided by zero.");
	}
	return SpreadsheetCell(lhs.getValue() / rhs.getValue());
}
```
&nbsp;

### Overloading the Arithmetic Shorthand Operators

Shorthand operators are `+=` and `-=`.

Writing the `operator+` does not provide `operator+=`. You have to overload it explicitly. 
These operators differ from basic arithmeric operators because they change the object on the left-hand size of the operator instead of creating a new object. They always require a `lhs` operator, so you can write them as methods.

```
class SpreadsheetCell
{
	public :
		// . . .
		SpreadsheetCell& operator+=(const Spreadsheet& rhs);

		SpreadsheetCell& operator-=(const Spreadsheet& rhs);

		SpreadsheetCell& operator*=(const Spreadsheet& rhs);

		SpreadsheetCell& operator/=(const Spreadsheet& rhs);
```

The implementation for `+=`. The same are similar.

```
SpraedsheetCell& SpreadsheetCell::operator+=(const SpreadsheetCell& rhs) {
	set(getValue() + rhs.getValue());
	return *this;
}
```

You cannot, however, write this (witch is good) :

```
5.4 += aThirdCell;
```

When you have both shorthand and basic arithmetic operators, it is recomanded that you implement the basic ones in terms of the shorthand to avoid code duplication :

```
SpreadsheetCell operator+(const SpreadsheetCell& lhs, const SpreadsheetCell& rhs) {
	auto result(lhs);	// Local copy
	result += rhs;
	return result;
}
```
&nbsp;

### Overloading Comparison Operators

Like the basic operators, they should be global so that you can use implicit conversion on both the lhs and rhs of the character. They all return a _bool_.

Here is the declaration for `==`, all are similar :

```
class SpreadsheetCell
{
	// . . .
}

bool operator==(const SpreadsheetCell& lhs, const SpreadsheetCell& rhs);
```

Implementation :

```
bool operator==(const SpreadsheetCell& lhs, const SpreadsheetCell& rhs) {
	return (lhs.getValue() == rhs.getValue());
}
```


Once you have written `==` and `<`, you can write the rest in terms of those two. Here is an example :

```
bool operator>=(const SpreadsheetCell& lhs, const SpreadsheetCell& rhs) {
	return !(lhs < rhs);
}
```


### Building Types with Operator Overloading


#### Note : Provide operator overloading as a service to clients of your class.

&nbsp;

### Building Stable Interfaces <a name="stable-interface"></a>

#### Using Interface and Implementation Classes


In order to separate what the client sees and what is actually implemented in a clean manner, you can implement two classes for every class : the _interface_ class and the _implementation_ class. The implementation class is identical to what we have written till now. The interface class presents `public` methods identical to those of the implementation class, but it only has one data member, a pointer to the implementation class  object. This is called the ___pimpl idiom___, or _private implementation idiom_, or _bridge pattern_. The result of it is that no matter how the implementation class changes, it has no effect on the public interface class. This reduces the need for recompilation.

To use this approach on the `Spreadsheet` class, define a public interface class, `Spreadsheet` that looks like this :

```
#include "SpreadsheetCell.h"
#include <memory>

// Forward Declaration
class SpreadsheetApplication;

class Spreadsheet
{
	public :
		Spreadsheet(const SpreadsheetApplication& theApp,
		   size_t width = kMaxWidth, size_t height = kMaxHeight);
		Spreadsheet(const Spreadsheet& src);
		~Spreadsheet();

		Spreadsheet& operator=(const Spreadsheet& rhs);

		void getCellAt(size_t x, size_t y, const SpreadsheetCell& cell);
		SpreadsheetCell& getCellAt(size_t x, size_t y);

		size_t getId() const;

		static const size_t kMaxHeight = 100;
		static const size_t kMaxWidth = 100;

		friend void swap(Spreadsheet& first, Spreadsheet& second) noexcept;

	private :
		class Impl;
		std::unique_ptr<Impl> mImpl;
};
```


The implementation class, `Impl`, is a private nested class.

The `Impl` class is the same as the one defined before, the only difference is that because it is now a private nested class, you cannot have the global `friend swap()` function. You need a private `swap()` method that is defined as follows :


```
void swap(Impl& other) noexcept;
```

Implementation :

```
void Spreadsheet::Impl::swap(Impl& other) noexcept {
	using std::swap;

	swap(mWidth, other.mWidth);
	swap(mHeight, other.mHeight);
	swap(mCells. other.mCells);
}
```

Now that the `Spreadsheet` class is implemented, it needs a user-declared destructor witch can be defaulted :

```
Spreadsheet::~Spreadsheet() = default;
```

The implementation of the `Spreadsheet` methods just pass the request on the underlying `Impl` object :


```
void Spreadsheet::setCellAt(size_t x, size_t y, const SpreadsheetCell& cell) {
	mImpl->setCellAt(x, y, cell);
}
```
```
SpreadsheetCell& Spreadsheet::getCellAt(size_t x, size_t y) {
	return mImpl->getCellAt(x,y);
}
```

The constructor of the `Spreadsheet` must now construct a new `Impl` :

```
Spreadsheet::Spreadsheet(const SpreadsheetApplication& theApp,
	size_t width, size_t height)
{
	mImpl = std::make_unique<Impl>(theApp, width, height);
}
```
```
Spreadsheet::Spreadsheet(const Spreadsheet& srd) {
	mImpl = std::make_unique<Impl>(*src.mImpl);
}
```

The copy constructor takes a reference to an `Impl`, not a pointer, so you must dereference the `mImpl` pointer.

The assignment operator:

```
Spreadsheet& Spreadsheet::operator=(constSPreadsheet& rhs) {
	*mImpl = *rhs.mImpl;
	return *this;
}
```

In the first line, the `Spreadsheet` assignmnt operator needs to forward the call to the `Impl` assignment operator, witch only runs when you copy direct objects. By dereferencing the `mImpl` pointers, you force direct object assignment, witch causes the operator of `Impl` to be called.



This technique to separate implementation from interface is very powerful. 
