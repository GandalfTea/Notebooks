


##	Memory Management




#### Note : In modern C++ you should void low-level memory operations as much as possible in favour of more modern aconstructs such as containers and smart pointers !

&nbsp;


### DOUBLE POINTERS
You can make double pointers, because why the fuck not :
```
  int * * handle = nullptr;
  handle = new int * ;
  * handle = new int;
```
This example shows how pointers can exist both on the stack and the heap.


&nbsp;

### No Throw New Int
When the memory allocation with  new  fails, it throws an exception and the program fails. There is a way to bipass this using the following, witch only returns a nullptr :
```
> int * ptr = new(nothrow) int;
```
It is recomanded to use the standard version of new because a nullptr isn't really helpfull.

&nbsp;

### VARIABLE SIZE ARRAY ON HEAP :
The advantage of putting an array on the heap is that you can define it's size at run time :
```
  Document * createDocArray() {
	size_t numDocs = askUserForNumberOfDocs();
	Document * docArray = new Document[numDocs];
	return docArray;
  }
```
&nbsp;

### DELETING AN ARRAY OF NEW POINTERS :
In case you have a pointer to an array of pointers that point to heap memory, you must delete the m individualy :
```
  const size_ t size  = 4;
  Simple**  mySimplePtrArray = new Simple * [size];

  //Allocate an object to each pointer :
  for (size_t i = 0; i < size; i++) { mySimplePtrArray[i] = new Simple(); }

  //Deleting each allocated object :
  for (size_t i = 0; i < size; i++) {delete mySimplePtrArray[i]; }

  //Delete the array itself :
  delete [] mySimplePtrArray;
  mySimplePtrArray = nullptr;
```

This is of course, out of date for C++. You should use smart pointers in moders std containers.

&nbsp;

### MULTI DIMENTSIONAL ARRAY FOR BOARD GAMES :
```
  char board[3] [3] = {};
  //Test code
  board[0] [0] = 'X';
  board[2] [1] = 'O';
```
It doesn't really matter witch is the X and Y axis if you are consistent with the declating.

The size of the array is the two dimentions multiplied together and than multiplied by the size:

3 * 3 * 1 = 9 for the above example. The 1 is the 1 byte it takes to store the char.


The computer stores multi-dimensional arrays like so :
```
board [0] [0] |
board [0] [1] | board [0]
board [0] [2] |
board [1] [0] 
board [1] [1]
board [1] [2]
.
.
.
```
&nbsp;

### MULTI DIMENSIONAL HEAP ARRAYS :


At first you might think it's this :
```
char * * board = new char [i] [j];	//BUG: Doesn't compile.
```
Heap arrays don't work like stack arrays. Their memory layout isn't contiguous.

You do it like this :
  ```
  char * * allocateCharacterBoard(size_t xDimension, size_t yDimension) {
	char** myArray = new char*[xDimension];		//Allocate first dimension
	for (size_t i = 0; i < xDimension; i++) {
		myArray[i] = new char[yDimension];	//Allocate the subarray	
	}
	return myArray;
  }
```
You also have to manually delete the subarray :
```
  void releseCharacterBoard(char * * myArray, size_t xDimension) {
	for (size_t i = 0; i < xDimension; i++) {
		delete [] myArray[i];			//Delete the subarray
	}
	delete [] myArray;				//Delete first dimension
  }
```


#### NOTE: Try to only use std::containers like std::arrays or std::vector. The old C-style from here is shit.


&nbsp;

### STATIC CAST POINTER :

This way, the pointer can do type checking :
```
  Document * documentPtr = getDocument();
  char * myCharPtr = static_cast<char>(documentPtr);	//BUG! Won't compile.
```

&nbsp;
&nbsp;



##	LOW LEVEL MEMORY OPERATIONS :


### Pointer Arithatic :

If you declare a pointer to an int and increse it by 1, the pointer moves ahead by the size of an int not by a single byte. 
```
  int * myArray = new int[8];
  myArray[2] = 33;		//This is familiar.
  * (myArray + 2) = 33;		//This does the same thing.
```


### Smart Pointers :

included in the <memory> header file.

#### unique_ptr

As a rule of thumb, always store dynamically allocated resources in instances of unique_ptr.

You might want to do this :
```
  void couldBeLeaky(){
	Simple* mySimplePtr = new Simple();
	mySimplePtr->go();
	delete mySimplePtr;
  }
```
This can cause a memory leak if the procedure go() throws an exception.

In this situation and any other, it is recomanded to use unique_ptr :
```
  void notLeaky() {
	auto mySimpleSmartPtr = make_unique<Simple>();
	mySimpleSmartPtr->go();
  }
```
You can dereference unique pointers in the same way as a normal pointer, using both * and ->
```
  mySimpleSmartPtr->go();
  ( * mySimpleSmartPtr).go();
```
You can use the get() method to get direct access to the underlying pointer :
```
  void processData(Simple * simple) {...}
```
```
  auto mySimpleSmartPtr = make_unique<Simple>();
  processData(mySimpleSmartPtr.get());
```
You can free the underlying pointer and change it to another using the reset() :
```
  mySimpleSmartPtr.reser();			//Free resource and set to nullptr
  mySimpleSmartPtr.reser(new Simple());		//Free resource and set to a new Simple instance
```
You can disconnect the underlying pointer with relese()
This returns the undetlying pointer to the resource and than sets the smart pointer to nullptr.
```
  Simple * simple = mySimpleSmartPtr.relese();	//Relese ownership
  //Use the simple pointer
  delete simple;
  simple = nullptr;
```

Because a unique_ptr represents unique ownership, it cannot be copied. 
Using the std::move() it is possile to move a unique_ptr to another one. This moves ownership :
```
  class Foo
  {
	public:
	    Foo(unique_ptr<int> data) : mData(move(data)) { }
	private:
	    unique_ptr<int> mData;
  };
  
  auto myIntSmartPtr = make_unique<int>(42);
  Foo f(move(myIntSmartPtr));
```


&nbsp;

#### shared_ ptr :


You use shared pointer in a similar way you use unique pointer.

To create a new one you use make_shared(), witch is more eficient than shared_ptr direclty :
```
auto mySimpleSmartPtr = make_shared<Simple>();
```
This breed of pointer also supports get() and reset(). The only difference when calling reser(), due to the reference counting, the undetlying resource is only freed when the last shared_ptr is destroyd or reset. Shared pointer does not support relese(). 

You can use use_count() to retrieve the number of shared_ptr instances that are sharign the same resource.

The following example uses a shared_ptr to store a file pointer. When the pointer is reset, the file pointer is automatically closed with a call to CloseFile(). :
```
  void CloseFile(FILE * filePtr) {
	if (filePtr == nullptr)
		return;
	fclose(filePtr);
	cout << "File closed." << endl;
  }
```
```
  int main() {
	FILE * f = fopen("data.txt", "w");
	shared_ptr<FILE> filePtr(f, CloseFile);
	if (filePtr == nullptr) {
		cerr << "Error opening file." << endl;
	} else {
		cout << "File opened." << endl;
		//Use filePtr
	}
	return 0;
  }
```
&nbsp;

#### REFERENCE COUNTING :

Is a techinque for keeping track of the numebr of instances of a class or particular object in use. A reference-counting smart pointer is one that keeps track of how many smart pointers have been built to refer to a single real pointer, or a single object. This is to avoid double deletion.

Double deletion is easy to provoke :
```
  void doubleDelete() {
	Simple * mySimple = new Simple();
	shared_ptr<Simple> smartPtr1(mySimple);
	shared_ptr<Simple> smartPtr2(mySimple;
  }
```
This will crash.
You should not use shared_ptr as in the previous example.

Instead, you should make a copy :
```
  void noDoubleDeletion() {
	auto smartPtr1 = make_shared<Simple>();
	shared_ptr<Simple> smartPtr2(smartPtr1);
  }
```
&nbsp;

#### Aliasing

A shared pointer supports aliasing. This allows shared_ptr to share ownership over a pointer (owned poitnter) with another shared_ptr, but pointing to a diferent object (stored pointer)
```
  class Foo
  {
	public :
		Foo(int value) : mData(value) { }
		int mData;
  };

  auto foo = make_shared<Foo>(42);
  auto aliasing = shared_ptr<int>(foo, &foo->mData);
```

&nbsp;


#### weak_ ptr :

It is related to the shared_ptr.
A weak_ptr can contain a reference to a resource managed by shared_ptr. It does not own a resource.
It does not free memory but it can be used to determine if the memory has been freed by the asociated shared_ptr.

To get access to the pointer stored in week_ptr you need to convert it to a shared_ptr :
* use the lock() method on a weak_ptr, witch returns a shared_ptr; the return is nullptr if the memoty has been dealocated in the meantime.

* create a new shared_ptr and give a weak_ptr as argument to the constructor. This throws an exception if the memory has been dealocated.
```
  void useResource(weak_ptr<Simple>& weakSimple) {
	auto resource = weakSimple.lock();
	if (resource) {
		cout << "Resource still alive." << endl;
	} else {
		cout << "Resource has been dealocated." << endl;
	}
  }

  int main() {
	auto sharedSimple = make_shared<Simple>;
	weak_ptr<Simple> weakSimple(sharedSimple);

	//Try to use the weak_ptr.
	useResource(weakSimple);

	//Reset the shared_ptr.
	sharedSimple.reset();

	useResource(weakSimple);
	
	return 0;
  }

: Simple constructor called!
  Resource still alive.
  Simple destructor called!
  Resource has been dealocated.
```

&nbsp;

### Move Semantics :

All smart pointers support move semantics (discussed later). This means it is very efficient to heve them as return :

```
  unique_ptr<Simple> create() {
	auto ptr = make_unique<Simple>();
	//Do something...
	return ptr;
  }
```
```
> int main() {
	unique_ptr<Simple> mySmartPtr1 = create();
	auto mySmartPtr2 = create();
	return 0;
  }
```

&nbsp;

### emable_shared_from_this :

It is an advanced feature discussed later.

It adds two methods to a class :

* shared_from_this() : returns a shared_ptr that shares ownership of the object.
* weak_from_this() : returns a weak_ptr that tracks ownership.
```
  class Foo : public enable_shared_from_this<Foo> {
	public :
		shared_ptr<Foo> getPointer() {
			return shared_from_this();
		}
  };

> int main(){
	auto ptr1 = make_shared<Foo>();
	auto ptr2 = ptr1->getPointer();
  }
```





&nbsp;

##	COMMON MEMORY PITFALLS :


* Underalocating strings : when there is no trailing '\0' sentinel :
```
  char buffer[1024] = {0};	//Allocate a whole bunch of memory.
  while (true) {
	char* nextChunk = getMoreData();
	if (nextChunk == nullptr) {
		break;
	} else {
		strcat(buffer, nextChunk);	//BUG! No guarantees against buffer overrun!
		delete[] nextChunk;
	}
  }
```

* Accessing Out-Of-Bounds Memory : A pointer to a random location, a lost '\0', etc :
```
  void fillWithM(char * intStr) {
	int i = 0;
	while (inStr[i] != '\0' {
		inStr[i] = 'm';
		i++;
	}
  }
```
If the '\0' to the input string is lost, all memory will be 'm'.
This type of bug, writing to memory bast the end of an array are called BUFFER OVERLOW errors.
&nbsp;

#### * Memory Leaks :

In the following code, the Simple class is written to relese all allocations. When doSomething() is called, the outSimplePtr is changed to another Simple object without deleting the old one :
```
  class Simple {
	public :
		Simple() { mIntPtr = new int(); }
		~Simple() { delete mIntPtr; }
		void setValue(int value) { *mIntPtr = value; }
	private :
		int* mIntPtr;
  };

> void doSomething (Simple * & outSimplePtr) {
	outSimplePtr = new Simple ();	//BUG! Does not delete the original object.
  }

> int main() {
	Simple* simplePtr = new Simple();
	doSomething(simplePtr);
	delete simplePtr;		//Only clears the second object.
	return 0
  }
```
&nbsp;

#### FINDING AND FIXING MEMORY LEAKS :

There are apps that can detect memory leaks. The Microsoft Visual C++ has one in it's debug library.
To add it to other programs, include :
```
  #define _ CRTDBG_MAP_ALLOC
  #include <cstdlib>
  #include <crtdbg.h>
```
Next, define the new operator as follows :
```
   #ifdef _ DEBUG
	#ifndef DBG_NEW
		#define DBG_NEW new (_NORMAL_BLOCK , __FILE__ , __LINE__ )
		#define new DBG_NEW
	#endif
   #endif
```

The last thing is to add the following line at the start of your main() function :

	 _CrtSetDbgFlag(_CRTDBG_ALLOC_MEM_DF | _CRTDBG_LEAK_CHECK_DF);


Output :

	Detected memory leaks!
	Dumping objects ->
	c:\leaky\leaky.cpp(15) : {147} normal block at 0x014FABF8, 4 bytes long.
	  Data: <    > 00 00 00 00
	c:\leaky\leaky.cpp(33) : {146} normal block at 0x014F5048, 4 bytes long.
	  Data: <Pa  > 50 61 20 01
	Object dump complete.



&nbsp;

### Double deleting and Invalid Pointers :


Double deleting of an allocation can create unexpected problems. The effects might take long to notice, depending on what was written in the place of the initial allocation.

Microsoft Visual C++ detects double deletion.




