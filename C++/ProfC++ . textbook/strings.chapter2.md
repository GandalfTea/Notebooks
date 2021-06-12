

## C-style Strings

Strings are represented as an array of characters.
The last character of the string is a null characher ("\0") so that the OS can know when the string ends. This character is known as NUL (only one L).

Why should you learn some old strings? : Because you will have to work with old libraries or even some C-based interface in the OS.

For a string like "hello" you must allocate not 5 but 6 characters including the NUL.

Functions for C strings are kept in the <cstring> header. As a general rule, those do not handle memory allocation. 


For exemple : the strcpy() takes two strings as parameters. It copies the second onto the first weather it fits or not. In the following code we build a wrapper around strcpy() to fix this. Remember strlen() returns the amount of characters without NUL, so we have to add one :
```
  char * copyString(char const *  str) {
	char * result = new char[strlen(str) +1];
	strcpy(result, str);
	return result;
  }	 
```
In order to remember about strlen(), try to add more strings together and see that the result that it only needs one NUL.
To combine strings we use strcat() or string concatenate. 
```
 char * appendString(const char * str1, const char * str2, const char * str3) {
	char * result = new char[strlen(str1) + strlen(str2) + strlen(str3) +1]
	strcpy (result, str1);
	strcpy (result, str2);
	strcpy (result, str3);
	return result;
  }
```
The sizeof() returns the size of a variable.
sizeof(char) is 1 because a char only has one byte.
It is not similar to strlen(), for it varies on the way of storage of the actual string.
If it is stored as an array of char, char[], it returns the size with the NUL.
```
> char text1[] = "abcdef";
  size_ t s1 = sizeof(text1); //is 7
  size_ t s2 = strlen(text1); //is 6
```
However, if the string is stores as a char pointer, sizeof() returns the size of the pointer.

&nbsp;
 
### String Literals

Strings that are values, not variables : 
```
> cout << "hello" << endl;
```
String literals are held in the read-only memory, allowing the OS to store only one instence of it.
Even if the program prints 500 times "hello", it will only be stored once. This is called > literal pooling.

String literals are an array of n const char.
The following ields undefined behavior :
```
char * ptr = "hello";		// assign the string literal to a variable
ptr[1] = 'a';			// undefined behaviour!
```
A much safer way is to assign it to a const pointer :
```
const char * ptr = "hello";	//Assign the string to a const	
ptr[1] = 'a';			//Error! Atempts to write read-only memory
```

&nbsp;

### Raw String Literals


Raw string literals are string literals that can span across multiple lines of code and do not care about ".
```
R"(Hello "World")"
```

Escape sequences linke \n and \t are processed as normal text.
```
const char * str = R"(no tab character \t)"
```
no tab character \t


To get a newline in raw string literals, you just have to write on a new code line :
```
const char * str = R"(Line 1
Line 2)";
```
Because the syntax uses ), you can not use them in the string.

To use it, you must use the full syntax :
```
const char * str = R"-(Embedded ) " characters)-"
```

&nbsp;

## The C++ std::string Class

The <string> class takes care of memory allocation.

What is wrong with c-style strings? :

Advantages :
* They are simple, making use of basic character type and arrays
* They are lightweight, taking only the memory they need.
* They are low level, can copy and manipulate them as raw memory

Didadvantages :
* They are unforgiving and suscenpible to dificult bugs
* They don't use the object-style nature of C++
* They require effort to simlate a first-class string type

C++ strings solve most of the problems and have all the advantages.


Using the class: 

You can use + to add strings :
 ```
  string A("12");
  string B("34");
  string C;
  C = A + B; // C is 1234
```
You can use += to concatinate
```
A += B; // A is 1234
```
You can compare with ==
```
A == C // true
```
You can use !=   >    <    =>   =< 


Automatic memory allocation :
```
  string myString = "hello";
  myString += ", there";
  string myOtherString = myString;
  if (myString == myOtherString) {
	myOtherString[0] = 'H';
  }
  cout << myString << endl;	//hello, there
  cout << myOtherString << endl;//Hello, there
```
One thing to notice is that there are no memory leaks. The <string> destructor clears up memory when string objects go out of scope.

&nbsp;

### std::string Literals

A string literal in source code is interpreted as a const char * . You can use the literal "s" to interpret it as a std::string instrad :
```
 auto string1 = "Hello World";		//string1 is a const char *
 auto string2 = "Hello World"s;	//string2 is a std::string 
```
The s literal requires a using namespace std::string _ literals or using namespace std;

&nbsp;

### High-Level Mumeric Conventions

You can use to_ string() on any number to convert it to string.

It also works the other way around :

str is the string to convert
idx is a pointer that recieves the index of the first non-converted character
base is the mathematical base 

the name is (s) string (to) (i) int or (l) for long, etc.
```
 int stoi(const string& str, size_ t * idx = 0, int base = 10);
 long stol(const string& str, size_ t * idx = 0, int base = 10);
 unsigned long stoul(const string& str, size_ t * idx = 0, int base = 10);
```
etc.




How to use :
```
  const string toParse = "     123USD";
  size_ t index = 0;
  int value = stoi(toParse, &index);

  cout << "Parsed value : " << value << endl;
  cout << "First non-parsed character : " << "'" << toParse[index] << "'" << endl;
```
output:
```
Parse value: 123
First non-parsed character: 'U'
```

&nbsp;

### Low-Level Numeric Conversions :
the <charconv> header


Those functions do not perform any memory allocation but they can be orders of magnitude faster than higher-level numerical conversion functionss.

For concerting int to char we have :
```
 to_ chars_ result to_ chars(char * first, char * last, IntegerT value, int base = 10);
```
IntegerT can be any signed or unsigned integer type of char. The result is of type to_ chars_ result :
```
  struct to_ chars_ result {
  char * ptr;
  errc ec;
};
```
The ptr member is equal to the one-past-the-end pointer of the written characters if the conversion was successful or it is equal to last if the conversion failed (in witch case, ec == errc::value_ too_ large).


How to use:
```
//tried and does not work
//What is 12345 and why is there no base?

  std::string out(10, ' ');
  auto result = std::to_ chars(out.data(), out.data() + out.size(), 12345)
  if (result.ec == std::errc()) { / * Conversion successful * / };
```

also works withfloats, double and long double :
```
> to_ char_ result to_ char(char * first, char * last, FloatT value);
```
also, you can change formatting like so: 
```
> to_ char_ result to_ char(char * first, char * last, FloatT value, chars_ format format);
```
options: 
```
  enum class chars_ format {
	scientific,			// (-)d.ddde+dd
	fixed,				// (-)ddd.ddd
	hex,				// (-)h.hhhp+d
	general = fixed | scientific	// (-)ddd.ddd or (-)d.ddde+dd, the shortest.
};
```



To convert char to numbers :
```
> from_ chars_ result from_ chars(const * char first, const * last, IntegerT& value, int base = 10);
> from_ char_ result from_ chars(const char * first, const char * last, FloatT& value, char_ format format = chars_ format::general);
```
from_ chars_ result is a type defined as :
```
struct from_ chars_ result {
	const char * ptr;
	ercc ec;
};
```

&nbsp;

### The std::string_view Class


There  was a problem with passing strings as function parameters. What do you pass it as? const char? and lose the functionality or std::string&? and lose the ability to pass string literals without copying them?

In c++17, you can use the std::string_ view class with the < string_ view> header.


A string_ view is a replacement for const string& but with no copies.
It also adds the methods remove_ prefix(size_ t) and remove_ suffix(size_ t) to move the start and end pointers by a given offset.



#### NOTE : You cannot concatenate a string and a string_ view.




It works very similarly to the std::string.

Example : return the extension of a document
```
string_ view extreactExtension(string_ view fileName) {
	return fileName.substr(fileName.rfind('.'));
}
```

It can be used with all kinds of strings :
```
  string fileName = R"(c:\temp\myfile.ext)";
  cout << "C++ string: " << extractExtension(fileName) << endl;

  const char * cString = R"(c:\temp\myFile.ext)";
  cout << "C string: " << extractExtension(cString) << endl;

  cout << "Literal: " << extractExtension(R"(c:\temp\myFile.ext)") << endl;
```
There is not a single copy created in the past examples. The fileName parameter of the function is just a pointer and a length, and so is the return.





There is also a version of string_ view that accepts any raw buffer and a length. This is useful when a string is not NUL terminated.
```
  const char * raw = / * ..... * /;
  size_ t length = / * ....... * /;
  cout << "Raw: " << extractExtension(string_ view(raw, length)) << endl;
```




There is no way to directly construct a string from a string_ view. You either use a string constructor or use the string_ view::data().
```
> void handleExtension(extractExtension("myFile.ext"));
```
You can convert like so:
```
> handleExtension(extractExtension("myFile.ext").data());	//data() method
  handleExtension(string (extractExtansion("myFile.ext")));	//string() constructor
```


#### NOTE : Always use a string_ view when a function requires a read-only string.


&nbsp;

### std::string_ view Literals :



You can use the standard user-defined "sv" to interpret a string literal as a std::string_ view :
```
> auto sv = "My string_ view"sv;
```
This reguires : using namespace    std::string_ view_ literals    or    std


&nbsp;

### Nonstandard Strings :


People have created their own version of strings. Decide before a project what type you will use.
