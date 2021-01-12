
### Conditional operator :

An operator for if statements :
```
> std::cout << ((i < 2) ? "yes" : "no");
```

### Initializers for if statements :
```
> if( <initializer> ; <conditional-expression>) { body }
```
Any variable introduces as initializer is only available in the if statement.
Also works with switches.



### Short circuit logic in logical operators :

C++ evaluates logical operators in order and if one of them completes the condition, the rest will be uncomputed.
Put the cheeper bools first so that the ones that take more processing power have a change of not being computed.
```
> bool result = bool1 || bool2 || (i > 7) || (27 / 13 % i + 1) < 2;
```
if bool1 is true, the rest will not be computed.


### Structured bindings :

You can initialize multiple variables by the values in an array or a struct :
```
> std::array<int, 3> values = {11, 22, 33};
  auto [x, y, z] = values;
```
or
```
> struct Point {double mX, mY, mZ};
  Point point;
  point.mX = 1.0; point.mY = 2.0; point.mZ = 3.0;
  auto [x, y, z] = point;
```


### Initializer lists :

You can pass a list of variables in a function as arguments, for example an array or vector using an initializer list.

> include <initializer_list>

> int makeSum (initializer_ list<int> lst){


### Smart pointers :

2 kinds:
> std::unique_ ptr
> std::shared_ ptr

unique_ ptr just frees the memory itself after it is out of scope or is deleted
```
Employee * anEmployee = new Employee;
// ...
delete anEmployee;
```
to :
```
auto anEmployee = make_ unique<Employee>();
auto anEmployrr = make_ unique<Employee[]>(10); // for arrays
```
shared_ ptr allows for distribution of ownership of the data. Each time one is assigned, a reference count is incremented and every time it goes out of scope, it is decremented. When it gets to 0 it is freed.
```
> auto anEmployee = make_ shared<Employee>();
```

### Exeptions :

A piece of code throws an exeption and another catches the the exeption and takes appropiate action.
```
> .#include <stdexcept> 
> double divideNumbers( double numerator, double denominator){
	if (denominator == 0) {
		throw invalid_ argument("Denominator cannot be 0");
		}
	return numerator / denominator;
  }
```
When the throw line is executed, the progran ends the function without returning value.
You can also usea try/catch block :
```
> try {
	cout << divideNumbers(2.5, 0.5) << endl;
	cout << divideNumbers(2.3, 0) << endl;
	cout << divideNumbers(4.5, 2.5) << endl;
  } catch (const invalid_ argument& exception) {
	cout << "Exception caught : " << exception.what() << endl;
  }
```

The first call is executed, the second throws an exception and junps to the catch block and the third one is nexer executed. The output is :
```
> 5
  An exception was caught : Denominator cannot be 0.
```

### auto

You can use auto as a const reference or just a reference :
```
 auto&
 const auto&
```

### decltype

Steals the type from other variables :
```
> int x = 123;
> decltype(x) y = 456  //here y is an int.
```
This does not strip reference and const.



