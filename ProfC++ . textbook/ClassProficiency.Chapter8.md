
# Gaining Proeficency with Classes :


### Introducing the Spreadsheet example :

The spreadsheet aplication contains two basic classes : Spreadsheet and SpreadsheetCell. 
Each Spreadsheet object contains SpreadsheetCell objects. In addition, a SpreadsheetApplication class manages a colection of Spreadsheets.


This chapter focuses on SpreadsheetCell. Multiple implementation methods.

### Class Definition :

> class SpreadsheetCell {
	public :
		void setValue(double inValue);
		double getValue() const;
	private :
		double mValue;
  };

The only difference between a struct and a class is that the default access specifier for a struct is public while for a class it is private (for variables or procedures not in public or private). The SpreadSheetCell can also be written as a struct :

> struct SpreadsheetCell {
		void setValue(double inValue);
		double getValue() const;
	private :
		double mValue;
  };


NOTE : It's custom to use a struct if you just need a collection of publicly available data members and no or very few methods.

NOTE :  You can declare members and access specifiers in any order. You can also repeat them :

> class SpreadsheetCell {
	public :
		void setValue(double inValue);
	private :
		double mValue();
	public :
		double getValue() const;
  };



NOTE : Usually the class definition goes in a header file and the method definition go in a source file that #includes that header file.




# Calling other methods :

In a real spreadsheet application, cells can contain both numbers as well as text. Here is an example of a class that supports both :

>  #include <string> 
   #include < string_ view>

   class SpreassheetCell {
	public :
		void setValue(double inValue);
		double getValue() const;

		void setString(std::string_view inString);
		std::string getString() const;
	private :
		std::string doubleToString(double inValue) const;
		double stringToDouble(std::string_view inString) const;
		double mValue;
  };

The implementation for the new functions is :

> string SpreadsheetCell::doubleToString(double inValue) const {
	return to_string(inValue);
  }


> double SpreadsheetCell::stringToDouble(string_ view inString) const {
	return strtod(inString.data(), nullptr);
  }




# Objects on the Stack :

like this :

> SpreadsheetCell myCell, anotherCell;


# Objects on the Heap :

like this :

> SpreadsheetCell * myCellp = newSpreadsheetCell();
  ( * myCellp).setValue(3.7);
  myCellp-> setValue(3.7);
  delete myCellp;
  myCellp = nullptr;

To avoid problems at deletion, you should use smart pointers :

> auto myCellp = make_ unique<SpreadsheetCell>();
  OR
  unique_ ptr<SpreadsheetCell> myCellp(new SpreadsheetCell());

  myCellp-> setValue(3.7);



# Object Life Cycles :

Objects are created at the point you declare them, together with all embedded objects.

# Writing Constructors :

A constructor without any arguments is called a default constructor.

Here is a first attempt at writing a constructor for the SpreadsheetCell :

> class SpreadsheetCell {
	public :
		SpreadsheetCell(double initialValue);
  };

and the implementation :

> SpreadsheetCell::SpreadsheetCell(double initialValue) {
	setValue(initialValue);
  }



# Constructors on the Stack :

> SpreadsheetCell myCell(5), anotherCell(4);

You do not need to call the constructor specifically.
You also can't call the constructor later like this :

> SpreadsheetCell myCel;
  myCell.SpreadsheetCell(5);	// WILL NOT COMPILE!

# Constructors on the Heap :

> auto smartCellp = make_ unique<SpreadsheetCell>(4);

 OR (not recomanded)

> SpreadsheetCell * myCellp = new SpreadsheetCell(5);
  delete myCellp

NOTE : You can declare a pointer to a SpreadsheetCell without calling the constructor immediately.




# Providing Multiple Constructors :

You can provide more than one constructor.
All constructors have the same name, but different arguments.
In C++, if you have more functions same name, it selects the one needen. This is called overloading.

> class SpreadsheetCell {
	public :
		SpreadsheetCell(double initialValue);
		SpreadsheetCell(std::string_view initialValue);
  };

> SpreadsheetCell::SpreadsheetCell(std::string_ view initialValue) {
	setString(initialValue);
  }


Example of use : 

> SpreadsheetCell aThirdCell("test");				//Uses string
  SpreadsheetCell aForthCell(4.4);				//Uses double
  auto aFifthCellp = make_ unique<SpreadsheetCell>("5.5");	//Uses string



You cannot define a constructor by another. This does not work :

> SpreadsheetCell::SpreadsheetCell(string_ view initialValue) {
	SpreadsheetView(stringToDouble(initialValue));		//DOES NOT WORK
  }



# Default Constructors :

You need a default constructor because an array of objects needs to call it. If you don't have one, the following does not compile :

> SpreadsheetCell cells[3]; 	//FAILS compilation without default constructor.

A default constructor is also needed if you want to store the objects in a std container such as  std::vector.

> class SpreadsheetCell {
	public :
		SpreadsheetCell();
  };

> SpreadsheetCell::SpreadsheetCell() {
	mValue = 0;
  }


You can also leave out the mValue 

> SpreadsheetCell::SpreadsheetCell() {
   }



You use default constructors on the stack like so :

> SpreadsheetCell myCell;
  SpreadsheetCell myCell(); //DOES NOT COMPILE

For heap :

> auto smartCellp = make_ unique<SpreadsheetCell>();
  OR
  SpreadsheetCell * myCell = new SpreadsheetCell();

  delete myCellp;
  myCellp = nullptr;



# Compiler-Generated Default Constructors :


When we first defined SpreadsheetCell, we did not give it a default constructor.
Still, the code works just fine.

The following definitions had a constructor that accepted a string or a double. Still no default.
With the constructors, this code does not compile anymore :

> SpreadsheetCell myCell;
  myCell.setValue(6);

The reason it is not compiling is that if you don't have any constructor, the compiler writes one for you that doesn't take any arguments. If you declare a constructor, the compiler will not declare a default by itself anymore.


# Explicitly Defaulted Constructors :

Like explained earlier, if you write a constructor that takes arguments, you will have to write the default by yourself if you want that functionality.

To avoid this, C++ supports the concept of explicitly defaulted constructors. This allows you to write the class definitions as follows wothout the need to implement the default constructor in the implementation file :

class SpreadsheetCell {
	public :
		SpreadsheetCell() = default;
		SpreadsheetCell(double initialValue);
		SpreadsheetCell(std::string_view initialValue);
 };

You have the 2 custom constructors and the compiler generates a standard compiler-generated default.


# Explicitly Deleted Constructors :

If you do not want any constructor for your class and want to stop the compiler from creating one do:

> class MyClass {
	public :
		MyClass() = delete;
 };




# Constructor Initializers :


Up till now, the constructor uses data members in the body of the constructor.

C++ provides an alternative method for initializing data members called constructor initializer. Here is the same constructor with the ctor-initializer syntax :

> SpreadsheetCell::SpreadsheetCell(double initialValue)
	: mValue(initialValue)
  {
  }

This initializez the data members directly with that value at construction.

If your class has an object as a data member that does not have a connstructor, you need to use the constructor initializer :

> class SpreadsheetCell {
	public :
		SpreadsheetCell(double d);
  };

> class SomeClass {
	public :
		SomeClass();
	private :
		SpreadsheetCell myCell;
  };


You need to initialize the object with a default constructor :

> SomeClass::SomeClass() : mCell(1.0) { }

NOTE : The  constructor initializez data members in the order they appear in the class definition, not the one in the constructor initializer.



# Copy Constructors :

There is a special constructor that allows you to create an object that is an exact copy of another object :

> Class SpreadsheetCell {
	public :
		SpreadsheetCell(const SpreadsheetCell&  src);
  };

The constructor takes a const reference to the source object. 

Implementation :

> SpreadsheetCell::SpreadsheetCell(const SpreadsheetCell& src)
	: mValue(stc.mValue)
  { }


# When the copy constructor is called :

When passing a string to a function, for example, because the string is a class, it will call it's copy constructor.

> string name = "heading one";
  printString(name);   		// copies name

The string copy constructor is called for the inString object in printString().
When the printString() is finnished, the inString is destroyed.


# Calling the copy constructor explicitly :

To create a copy of SpreadsheetCell :

> SpreadsheetCell myCell1(4);
  SpreadsheetCell myCell2(myCell1);	//Now has the same value.


# Explicitly Defaulted and Deleted Copy Constructor :

> SpreadsheetCell(const Spreadsheetcell& src) = default;
> SpreadsheetCell(const Spreadsheetcell& src) = delete;

By deleting the copy constructor, the object can't be copied anymore.
This can be used to disallow passing the object by value


# Initializer-List Constructors :

It is an initializer with a std::initializer_ list<T> as it's first parameter, without any additional parameters.
You need to include <initializer_list> header.

The following class only accepts an initializer_list<T>, otherwise it throws an exeption :

> class EvenSequence {
	public :
		EvenSequence(initializer_list<double> args) {
			if (args.size() % 2 != 0) {
				throw invalid_argument("Initializer_list should "
					"contain even number of elements.";
			}
			mSequence.reserve(args.size());     //request the vector capacity to be at least args.size()
			for (const auto& value : args) {
				mSequence.push_back(value);
			}
		}
		
		void dump() const {
			for (const auto& value : mSequence) {
				cout << value << ", ";
			}
			cout << endl;
		}
	private :
		vector<double> mSequence;
  };



# Delegating Constructors :

Delegated constructors allow constructors to call other constructors from the same class
However, this call cannot be placed in the body. It must be placed in the constructor initializer and it must be alone :

> SpreadsheetCell::SpreadsheetCell(string_ view initialValue) 
	: SpreadsheetCell(stringToDouble(initialValue))
  {  }

The constructor called is runned and than the delegated one.


Make sure to avoid constructor recursion :

> class MyClass {
	MyClass(char c) : MyClass(1.2) { }
	MyClass(double d) : MyClass('m') { }
  };

They both delegate to each other.





# Summary of Compiler-Generated Constructors :

If you define :			Than the compiler generated :		And you can create an object:

[none]				   A default constructor		With no arguments
				   A copy constructor			SpreadsheetCell cell;
									As a copy of another object
									SpreadsheetCell myCell(cell);

A default constructor		   A copy constuctor		        With no arguments
							                SpreadsheetCell cell;
					     	    		        As a copy of another object
								        SpreadsheetCell myCell(cell);

A copy constructor only		   No constructors		        no objects

A single argument or 		   A copy constructor		     With arguments
multi argument non-copy						     SpreadsheetCell cell(6)
constructor only						     As a copy of another object
								     SpreadsheetCell myCell(cell);

A default constructor		   A coopy constructor		     With no argumenrs
as well as a single argument					     With arguments
or multi argument						     SpreadsheetCell cell;
non-copy constructor						     SpreadsheetCell cell(6);
								     As a copy of another object
								     SpreadsheetCell anotherCell(cell





# Object destruction :

The destructor is your chance to perform any cleanup work for the object, such as freeing dynamically allocated memory or closing file handles. 

The compiler writes a basic destructor if you don't provide one.


# Assigning to objects

You can assign the value of one object to another like so :

> SpreadsheetCell myCell(5), anotherCell;
  anotherCell = myCell;

C++ offers a method in every class to perform assignment. The method is called assignment opperator. It's name is operator= because it is actually an overloaing of the = operator for the class.

If you don't write your own assignment opperator, cpp writes one for you to allow objects to be assigned to another.



# Declaring an Assignment Operator :

> class SpreadsheetCell {
	public :
		SpreadsheetCell& operator=(const Spreadsheetcell& rhs);
  };


Unlike a copy constructor, the assignment opperator returns a reference to a SpreadsheetCell object. The reason is that  assignments can be chained :

> myCell = anotherCell = aThirdCell;

The assignment opperator for anotherCell is called with aThirdCell as it's 'right-hand side' parameter. Next, the assignment opperator for MyCell is called. However, it's parameter is not anotherCell, it's the result of the assignment of aThirdCell to anotherCell. If that assignment fails, there is nothing to pass.


NOTE: You could declare the assignment opperator to return whatever type you wanted, including void. However, you should always return a reference to the object on whitch it is called because that's what clients expect.


# Defining an Assignment Opperator :

An assignment opperator can overwrite the current values in an object.

> SpreadsheetCell& SpreadsheetCell::operator=(cosnt Spreadsheetcell& rhs) {
	if (this == &rhs) {
		return *this;
	}

	mValue = rhs.mValue;
	return *this;
  }


# Explicitly Defaulted and Deleted Assignment Operator :

> SperadsheetCell& operator=(const SpreadsheetCell& rhs) = default;
> SpreadsheetCell& operator=(const SpreadsheetCell& ths) = delete;



# Compiler generated Copy Constructor and Copy Assignment Operator :

You can explicitly defaulta copy constructor by :

> MyClass(const MyClass& src) = default;
> MyClass& operator=(const MyClass& rhs) = default;


