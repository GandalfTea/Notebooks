

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

### Note : The Spreadsheet class does not contain a 2D array. Instead it contains a SpreadsheetCell * * . This is because the Spreadsheet object might have different dimentions, so the constructor of the class must dynamically allocate a 2D array, done like do :

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


SpreadsheetCell& Spreadsheet::getCellAt(size_t x, size_t y) {
	verifyCoordinate(x,y);
	return mCells[x][y];
}
```



### Freeing Memory with Destructors


page 273.

