

## Mastering Classes and Objects


### Friends

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



### Dynamic Memory Allocation in Objects

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

mCells is a pointer on the Stack towards a 2D array on the Heap.

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
To deal with this, let's use a verifyCoordonate() method:

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

The destructor does not take any arguments and there can only be one of them. Destructors are implicitly marked as noexept, since they should not throw any exceptions.

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

### Handling Copying and Assignment

Remember that if you don't write a copy constructor and an assignment operator youtself, C++ writes them for you. The copy constructor does not behave well with primitives, only copying bitwise. That presents problems if you dynamically allocate memory in the object. 

The following code copies the spreadsheet s1 to initialize s when s1 is passed to the printSpreadsheet() :

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

The spreadsheet contains one pointer var : mCells. A shallow copy gives the destination object a copy of the mCells pointer but not the underlying data. Thus, in this situatuation, both s1 ans s point to the same data.

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

A lot could go wrong with this code because a lot is going on. It is possible that the 'this' object gets into an invalid state. For example, suppose that the memory is successfully freed, that the mWidth and mHeight are properly set, but an exception is thrown in the loop that is allocating the memory. When that happens, execution of th e remainder of the method is slipped and the method is exited. Now the Spreadsheet instance is corrupt; it's mWidth and mHeight data members state a certain size, but mCells data member does not have the right amount of memory. In conclusion, this code is not very safe.

What we need is an all for nothing mechanism, either everything succeeds or the this object remains untouched. To implement such an exceptionsafe assignment operator, the copy-and-swap idiom is recomanded. For this, a non-member swap() function is implemented as a friend of the spreadsheet class. You could also write a swap() member of the class, however it is recomanded practice to keep it a non-member so that is can also be used by various Srandard Library algorithms. 

Here is a definition of them :

```
class Spreadsheet 
{
	public :
		Spreadsheet& operator=(const Spreadsheet& rhs);
		feiend void swap(Spreadsheet& first, Spreadsheet% second) noexcept;
		// . . .
};
```

The implementation of the swap function swaps each data member using the std::swap() utility function provied by the std headher <utility> :

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



### Disallowing Assignment and Pass-By-Value

Sometimes when you dynamically allocate memory in your class, it's easier to prevent anyone from copying and assigning to your objects.  You can do this explicitly by deleting your operator= and copy constructor :

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



## Handling Moving with Move Semantics 

Move semantics for objects require :

 * Move Constructor.
 * Move assignment operator.

These can be used by the compiler when the source object is a temporary object that will be destroyed after the operation is finished. 

Both move the data members from the source object to the new object,leaving the source object in some valid but otherwise indeterminate state. Often, data members are reset to null valies. 

This process actually moves ownership of the memory and other resources from one object to another object. They basically do a shallow copy of the member variabes and witch ownershop to prevent dangling pointers or resources, and to prevent memory leaks.

Before the move semantics, you need to learn about rvalue and rvalue references.


### Rvalue References

In C++, an _lvalue_ is something of whitch you can take an address; for example : a named variable. The name comes from the fact that lvalues can appear on the left-size of an assignment. 

An _rvalue_, on the other hand, is anything that is not an lvalue, sucha s a literal, or temporary object or value. Typically, an rvalue is on the right-side of an assignment operator. For example, take the following statement :

```
int a = 4 * 2;
```
In this statement, _a_ is an _lvalue_, it has a name and you can take the address of it with `@a`. The result of the expression `4 * 2` is an _rvalue_. It is a temporary value that is destroyed when the statement finished execution. In this example, a copy of this temporary value is stored in the variable with the name _a_.


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

