

## Mastering Classes and Objects


### Friends

C++ allows classes to declare that other classes are friends and can access protected and private data members and methods. You can specifi that the Bar class is a friend of Foo like :

"""
	class Foo {
		friend class Bar;
		// . . .
	};
"""

You can also specify exact methods :

"""
	class Foo {
		friend void Bar::processFoo(const Foo& foo);
		// . . .
	};
"""

Also works with standalone functions :

"""
	class Foo {
		friend void dumpFoo(const Foo& foo);
		// . . .
	};
"""


The friend declaration also searves as a prototype.



### Dynamic Memory Allocation in Objects

Last chapter described the SpreadsheetCell class. This one is modeled around the Spreadsheet class.

A Spreadsheet is a 2D array of SpreadsheetCell's, with methods to set and retrieve cells at specific locations. 


"""

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

"""

